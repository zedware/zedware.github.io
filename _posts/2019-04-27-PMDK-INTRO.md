---
layout: post
title: PMDK 及其应用场景
tags: PMDK NVM
---

PMDK 的全称是 Persistent Memory Development Kit，它包含了 Intel 开发的一系列旨在
方便非易失性内存的应用开发的函数库和工具。PMDK 是一个还在快速发展变化的新鲜事物，不过版本的迭代更新等很多信息都可以公开看到<sup><a id="fnr.1" class="footref" href="#fn.1">1</a></sup>。需要注意的是，PMDK 的网
站入口<sup><a id="fnr.2" class="footref" href="#fn.2">2</a></sup> 和 Git 仓库的入口<sup><a id="fnr.3" class="footref" href="#fn.3">3</a></sup>不是严格的一一对应关系。

实际上，PMDK 的 Git 仓库 <sup><a id="fnr.3.100" class="footref" href="#fn.3">3</a></sup> 中包含了
libpmem、libvmem、libpmemblk、libpmemlog、libpmemobj、libpmempool、libvmmalloc
等很多函数库。还有很多函数库和工具分散在其他的一些 Git 仓库中，例如我们下面要
介绍的 libpmemkind、libvmemcache、libpmemkv 以及支持 C/C++、Java、Python 等语言的绑定。在此前的文章[ A Try on PMDK ](https://zedware.github.io/PMDK/)中，我使用的是 PMDK 中最底层的一个函数库 libpmem。

Intel 把前面提到的很多函数库、工具等统称为 PMDK <sup><a id="fnr.2.100" class="footref" href="#fn.2">2</a></sup>。可以推测，它在快速发
展过程中会不断的增加新的成员，也可能会删除旧的成员。这些五花八门的函数库、工具、
例子和文档正说明了 NVM 潜在应用范围之广泛，也证明了 Intel 在不遗余力的构建围绕
NVM 的软件生态。


# 典型应用场景

PMDK 包含了这么多的函数库和工具，它们分别对应的就是不同的应用场景。从易用性和收益两个维度分别来
看，随着函数库使用难度的增加，采用它所获得的性能也有对应的提升。典型的使用场景包括：


## 将 DRAM 和 NVM 一起当做内存使用

这是一种最简单快捷，又无需修改应用程序代码，可以说几乎是即插即用、立竿见影的做法。NVM 比
DRAM 要便宜很多，而单条容量又是 DRAM 的好几倍（例如 32 GiB vs. 256 GiB）。操作系统将其识别为内存，DRAM 实际上作为 NVM 的一层 CACHE，应用程序看到的内存就是 NVM 构成的更大的
“DRAM”。

显然，这种模式下应用程序一行代码都没有改，严格来说跟 PMDK 也没有什么关系。很多需要大内存又不修改程序代码的场景都可以采用这种模式。


## 将 DRAM 和 NVM 分别当做不同层次的内存使用

这种方式也是将 NVM 当做易失性内存使用，不过不再将 DRAM 当做其 CACHE，也不能够对
应用完全透明。缺点是要求应用程序修改代码，最好还能够识别数据的冷热，自行决定将哪
些数据放到 DRAM，哪些数据放到 NVM。PMDK 提供的函数库 libmemkind 就是为了解决这种
应用场景的需求的。代码片段<sup><a id="fnr.4" class="footref" href="#fn.4">4</a></sup>的例子如下：

    struct memkind *pmem_kind = nullptr;
    size_t max_size = 1 << 30; /* gigabyte */
    
    /* Create PMEM partition with specific size */ 
    memkind_create_pmem(PMEM_DIR, max_size, &pmem_kind);
    
    /* allocate 512 bytes from 1 GB available */
    char *pmem_string = (char *)memkind_malloc(pmem_kind, 512);
    
    /* deallocate the pmem object */
    memkind_free(pmem_kind, pmem_string);

很多需要大内存，又可以自行修改程序代码的场景都可以尝试这种模式，例如 REDIS 类的内存 KV 系统。


## 将 NVM 当做对象缓存使用

这种方式仍然将 NVM 当做易失性内存使用，不过用途更单一，就是当做对象缓存。PMDK 中
的 libvmemcache 函数库<sup><a id="fnr.5" class="footref" href="#fn.5">5</a></sup>就是做这个用途的。如果现有系统中仅仅需要增加一个巨大的缓存，
这是一种很方便的做法。代码片段如下：

    VMEMcache *cache = vmemcache_new("/tmp", VMEMCACHE_MIN_POOL, VMEMCACHE_MIN_EXTENT, VMEMCACHE_REPLACEMENT_LRU);
    
    const char *key = "foo";
    vmemcache_put(cache, key, strlen(key), "bar", sizeof("bar"));
    
    char buf[128];
    ssize_t len = vmemcache_get(cache, key, strlen(key), buf, sizeof(buf), 0, NULL); 
    vmemcache_delete(cache);


## 将 NVM 当做持久化的 KV 存储使用

与之前的几种用法都不同，这种方式将 NVM 当做持久化存储使用，用来当做一个持久化的
KV 存储。PMDK 中的 libpmemkv 函数库<sup><a id="fnr.6" class="footref" href="#fn.6">6</a></sup>就是针对这种用途设计的。它还支持多种不同语言
的绑定，例如下面是一个 Node.js 的例子：

    const pmemkv = require('pmemkv');
    
    const kv = new KVEngine('vsmap', '{"path":"/dev/shm/"}');
    
    kv.put('key1', 'value1');
    assert(kv.count === 1);
    assert(kv.get('key1') === 'value1');
    
    kv.all((k) => console.log(` visited: ${k}`));
    
    kv.remove('key1');
    kv.stop();


## 将 NVM 当做事务性对象存储使用

这种方式也是将 NVM 当做持久化存储使用，但又需要采用自己的存储格式。既然应用程序
要有自己的存储格式，就需要解决很多通用的问题，例如基本的原子性和持久性保证。为了
便于理解，可以近似认为应用程序需要自己来做一个简单的数据库系统。PMDK 中的
libpmemobj 函数库 <sup><a id="fnr.7" class="footref" href="#fn.7">7</a></sup> 是针对这种场景设计的，

它提供了基本的事务支持，允许应用程序用特定的语法表示事务，从而将应用程序从这些比
较困难的底层细节里头解放出来。如果对比一下 C 语言和 C++ 的两种实现，会发现前者比较复杂，而后者就比较容易理解和使用。

当然，如果要在 NVM 上实现自己的高性能数据库，还是需要自己从底层做起，不能采用现成的函数库这么简单粗暴的做法。


## 直接使用底层的原语

这种方式是最接近物理硬件的高性能做法，正如前面的例子所述，它需要应用程序采用
MMAP、MEMCPY 等一系列针对 NVM 优化过的原语来进行访问。显然，应用程序需要做比较大
的改动，这也是很多高性能系统所倾向于采用的做法，PMDK 中的 libpmem 函数库<sup><a id="fnr.8" class="footref" href="#fn.8">8</a></sup>就是用来解决这
种需求的，它实际上也是前面很多函数库的基础，

虽然看起来比较底层，libpmem 的使用还是比较简单的，而且它封装了
[PMDK 和 CACHE FLUSH](https://zedware.github.io/CACHEFLUSH/)提到的各种
FLUSH 指令等具体的细节，对应用程序的侵入性不算太大，


# 编程模型以及对应的工具

仅仅提供这些函数库是不够的，Intel 还提供了一系列的工具来协助解决程序的性能等问题，
例如<sup><a id="fnr.4.100" class="footref" href="#fn.4">4</a></sup>的第 17 页就给出了一个很好的总结和图示。从底层的 MLC 到上层的
VTune，可谓应有尽有。熟悉 Linux 性能优化的人应该容易联想到 Brendan Gregg 的这个系列<sup><a id="fnr.9" class="footref" href="#fn.9">9</a></sup>。


# 更多的参考资源

如果还需要访问更多的资源，特别是 Linux 或 Windows 特定的资源，推荐先看<sup><a id="fnr.4.100" class="footref" href="#fn.4">4</a></sup>
中最后一页的资源链接：


## Getting Started

1.  IntelIDZpersistentmemory-<https://software.intel.com/en-us/persistent-memory>
2.  Entryintooverallarchitecture-<http://pmem.io/2014/08/27/crawl-walk-run.html>
3.  Emulatepersistentmemory-<http://pmem.io/2016/02/22/pm-emulation.html>


## Linux Resources

1.  LinuxCommunityPmemWiki-<https://nvdimm.wiki.kernel.org/>
2.  PmemenablinginSUSELinuxEnterprise12SP2-<https://www.suse.com/communities/blog/nvdimm-enabling-suse-linux>- enterprise-12-service-pack-2/


## Windows Resources

1.  UsingByte-AddressableStorageinWindowsServer2016-<https://channel9.msdn.com/Events/Build/2016/P470>
2.  AcceleratingSQLServer2016usingPmem-<https://channel9.msdn.com/Shows/Data-Exposed/SQL-Server-2016-and-Windows>- Server-2016-SCM&#x2013;FAST


## Other Resources

1.  SNIAPersistentMemorySummit2018-<https://www.snia.org/pm-summit>
2.  IntelmanageabilitytoolsforPmem-<https://01.org/ixpdimm-sw/>


# Footnotes

<sup><a id="fn.1" href="#fnr.1">1</a></sup> <https://pmem.io>

<sup><a id="fn.2" href="#fnr.2">2</a></sup> <http://pmem.io/pmdk/>

<sup><a id="fn.3" href="#fnr.3">3</a></sup> <https://github.com/pmem/pmdk>

<sup><a id="fn.4" href="#fnr.4">4</a></sup> <https://github.com/pmemhackathon/2019-04-08/blob/master/slides/intro.pdf>

<sup><a id="fn.5" href="#fnr.5">5</a></sup> <https://pmem.io/vmemcache/manpages/master/vmemcache.3.html>

<sup><a id="fn.6" href="#fnr.6">6</a></sup> <https://github.com/pmem/pmemkv>

<sup><a id="fn.7" href="#fnr.7">7</a></sup> <http://pmem.io/pmdk/libpmemobj/>

<sup><a id="fn.8" href="#fnr.8">8</a></sup> <http://pmem.io/pmdk/libpmem>

<sup><a id="fn.9" href="#fnr.9">9</a></sup> <http://www.brendangregg.com/linuxperf.html>
