---
layout: post
title: PMDK和CACHE FLUSH
tags: PMDK
---

无论是内存还是字节寻址的 NVM，写出的数据都是要先经过 CPU CACHE，然后通过一些 FLUSH 命令将
数据写出到内存或 NVM。以 Intel CPU 为例，在刷出数据这个功能点上有一系列的改进<sup><a id="fnr.1" class="footref" href="#fn.1">1</a></sup>。
数据经过 CACHE 写入到内存，WRITE BACK CACHE 机制可能会改变前后两个不同数据的写入顺序，
系统都会提供一些 BARRIER 原语来做控制。对于内存而言，控制了内存中的刷出顺序就足够了；但
是对于 NVM 还需要解决数据持久化时的顺序问题。例如：

    a = 1
    b = 2

如果要保证上述两个写操作的顺序，也就是在 b == 2 时，满足 a == 1，而不允许二者的顺序被
调整。对于内存操作可以在二者之间插入 BARRIER。但是对 NVM 而言，还需要控制数据持久化出去
的顺序，否则可能会出现 b = 2 被持久化了，但是因为宕机等原因导致 a = 1 没有被持久化，
从而在恢复时会发现 a 的取值不确定，可能是 1，也可能是其他的东西（垃圾）。

为了解决 NVM 持久化的问题，简单粗暴的做法可能有：

1.  禁止 CACHE。
2.  不用 WRITE BACK CACHE，而是用 WRITE THROUGHT CACHE。
3.  刷出的时候不再按照 CACHE LINE 进行，而是刷出整个 CACHE。

显然这些办法都太粗暴，容易带来很大的性能问题。下面我们先学习和总结一下 Intel CPU 对 CACHE 
刷新的支持和改进。


# CLFLUSH

CLFLUSH 是较早的用法，它的名字很好的表示了它的语义：CACHE LINE FLUSH。它虽然可以刷出 CACHE
中的数据到内存或 NVM，但是有如下的问题：

1.  阻塞住 CPU 的流水线，保证串行化的执行。阻塞的时间大约为 200ns。
2.  会导致 CACHE LINE 失效，下次要读刷出的数据还得重新加载。
3.  只是将数据从 CACHE 刷出到了内存子系统，并没有将数据写出到 NVM 介质。

当然了，CLFLUSH 出来的时候还没有考虑对 NVM 的支持，上述最后一个问题并不奇怪。为了解决这些问题
或缺陷，Intel 先后做了很多探索和扩展。


# CLFLUSHOPT

最容易想到的就是解决 CLFLUSH 的阻塞和串行化问题。例如将每次都阻塞的 CLFLUSH 改进为相互之间
无序的指令，这就是 CLFLUSHOPT。其中 OPT 代表的是 OPTIMIZATION。如果需要保证顺序，则加额外的
 SFENCE 指令，这样至少可以使得多个 CLFLUSHOPT 指令之间可以并行起来。不过 CLFLUSHOPT 并没有
解决失效 CACHE LINE 的问题。


# CLWB

为了解决 CLFLUSH 和 CLFLUSHOPT 每次都会导致 CACHE LINE 失效的缺点，Intel 也提供了 CLWB
指令，它不会失效 CACHE LINE，而是将其标记为未修改。类似于文件系统或数据库中将脏页标记清除掉。
其中 WB 是 WRITE BACK 的缩写。为了保证顺序，也需要加额外的 SFENCE 指令。但是上述三个指令都
没有解决将数据刷新到 NVM 介质的问题。


# PCOMMIT

Intel 曾经发明了 PCOMMIT 这个指令，来将内存中的数据持久化到 NVM 介质。PCOMMIT 指令也是要
串行化执行的，这一点和 CLFLUSH 类似。随着 Intel 对 NVM 支持的改进，PCOMMIT 不再需要，也就
被废弃了<sup><a id="fnr.2" class="footref" href="#fn.2">2</a></sup>。简单的理解就是数据刷出 CACHE 到了 NVM 就可以认为是持久化的了。NVM 实际上
怎么处理的，应用开发者不用关心。

既然 Intel 提供了这么多原语，作为应用开发者岂不是很痛苦，需要针对不同的场景来选择不同的原语？
实际情况当然不是这样的，PMDK 就是为了解决很多类似这样的问题，来方便广大的应用开发者的。它会
根据不同的上下文，结合从 CPUID 获取的 CPU 对不同原语是否支持的信息，做出最佳判断。


# Footnotes

<sup><a id="fn.1" href="#fnr.1">1</a></sup> <http://research.cs.wisc.edu/sonar/tutorial/03-hardware.pdf>

<sup><a id="fn.2" href="#fnr.2">2</a></sup> <https://software.intel.com/en-us/blogs/2016/09/12/deprecate-pcommit-instruction>
