---
layout: post
title: Random Thoughts AB
tags: [random]
---

As the title of the blog suggests, this is a collection of random thoughts. Most
of them are on software design, especially on database systems. When there are
friends or students asking questions, I will write down the interesting ones.

The topics list in reverse chronological order.

# Various Clocks

## TrueTime

1. True Time, [Spanner document](https://cloud.google.com/spanner/docs/true-time-external-consistency)
2. 7ms, [OSDI 2012 Spanner paper](http://static.googleusercontent.com/media/research.google.com/en/archive/spanner-osdi2012.pdf)
3. 100ns, [OSDI 2020 Time paper](https://www.usenix.org/conference/osdi20/presentation/li-yuliang)

## AWS

1. Time Sync Service, [AWS blog](https://aws.amazon.com/blogs/aws/keeping-time-with-amazon-time-sync-service/)

## Facebook

1. Facebook, 10ms to 100μs, ntpd to chrony, [Facebook 2020 blog](https://engineering.fb.com/2020/03/18/production-engineering/ntp-service/)
2. Open sourcing time appliance, [Facebook 2021 blog](https://engineering.fb.com/2021/08/11/open-source/time-appliance/)

## TSO

1. Percolator, [The paper](https://research.google/pubs/pub36726/)

## HLC

1. HLC, [The paper](http://www.cse.buffalo.edu/tech-reports/2014-04.pdf)
2. [The Advent of Tightly Synchronized Clocks](https://d347awuzx0kdse.cloudfront.net/vicomaus/content-file/the_advent_of_tightly_synchronized_clocks_in_distributed_systems_vicom.pdf)

## Misc

1. [Understanding Precision Time Protocol in Today's Wi-Fi Networks: A Measurement Study](https://www.usenix.org/conference/atc21/presentation/chen)
2. [GPS 时钟](https://cloud.tencent.com/developer/article/1621840)
3. [电力时钟](https://cloud.tencent.com/developer/article/1700110)

# Data Warehouse

## Snowflake

1. [The paper](https://dl.acm.org/doi/abs/10.1145/2882903.2903741)

## Redshift

1. [The evolution of Amazon Redshift (extended abstract)](http://vldb.org/pvldb/vol14/p3162-pandis.pdf), [The talk](https://www.youtube.com/watch?v=nTH8S-lV760)
2. [Fast and Effective Distribution-Key Recommendation for Amazon Redshift](http://www.vldb.org/pvldb/vol13/p2411-parchas.pdf)
3. [AWS Redshift Federated Query, Query on RDS for PG, Aurora for PG and data in S3](https://aws.amazon.com/about-aws/whats-new/2020/04/amazon-redshift-announces-general-availability-for-federated-querying/)

## ClickHouse

1. [算法实现细节的优化，但怎么避免走火入魔，过于陷入细节？](https://presentations.clickhouse.tech/bdtc_2019/#cover)

# User land fs for s3

1. [s3fs](https://github.com/s3fs-fuse/s3fs-fuse)
2. [BlueFS in Ceph](https://www.pianshen.com/article/48531051249/)

# John Ousterhout

John is famous for his work on Log-Structured File System, Tcl, Raft, RAMcloud. He is a professor who writes code, the author of the book A Philosophy of Software Design, and was a Distinguished Engineer at Sun, the founder of several startups. And he was elected to the National Academy of Engineering in 2001.

1. Home page of John, with many funny things [https://web.stanford.edu/~ouster/cgi-bin/home.php](https://web.stanford.edu/~ouster/cgi-bin/home.php)
2. A Philosophy of Software Design | John Ousterhout | Talks at Google [https://www.youtube.com/watch?v=bmSAYlu0NcY](https://www.youtube.com/watch?v=bmSAYlu0NcY)
3. Can Great Programmers Be Taught? - John Ousterhout - at #SoftGeeks [https://www.youtube.com/watch?v=lgZ7Cxt5uIU](https://www.youtube.com/watch?v=lgZ7Cxt5uIU)
4. CS190 [https://web.stanford.edu/~ouster/cgi-bin/cs190-winter21/index.php](https://web.stanford.edu/~ouster/cgi-bin/cs190-winter21/index.php)

# Nearest neighbor search

Traditional Database and GIS [https://blog.crunchydata.com/blog/a-deep-dive-into-postgis-nearest-neighbor-search](https://blog.crunchydata.com/blog/a-deep-dive-into-postgis-nearest-neighbor-search) [https://docs.oracle.com/database/121/SPATL/sdo_nn.htm#SPATL1032](https://docs.oracle.com/database/121/SPATL/sdo_nn.htm#SPATL1032) [https://docs.microsoft.com/en-us/sql/relational-databases/spatial/query-spatial-data-for-nearest-neighbor?view=sql-server-ver15](https://docs.microsoft.com/en-us/sql/relational-databases/spatial/query-spatial-data-for-nearest-neighbor?view=sql-server-ver15) [https://www.youtube.com/watch?v=lYnmS_hwW9g](https://www.youtube.com/watch?v=lYnmS_hwW9g) Spatial Analysis: Nearest Neighbor Analysis with ArcGIS Pro: McDonald's v. Walgreen's

Internet Service

# Flink state db

Alibaba GeminiDB (alternative for RocksDB) [https://segmentfault.com/a/1190000023530460](https://segmentfault.com/a/1190000023530460)

高性能的内存式 StateBackend —— Gemini [https://blog.csdn.net/Foools/article/details/113546027](https://blog.csdn.net/Foools/article/details/113546027)

这里有比较完整的功能说明 [https://help.aliyun.com/document_detail/145959.html](https://help.aliyun.com/document_detail/145959.html)

版本重大功能简介 GeminiStateBackend是Blink开发的新一代后台，它使用自研的存储引擎GeminiDB。通过线 上部分作业的测试显示，GeminiStateBackend的性能可以达到NiagaraStateBackend性能的 1.5倍。GeminiStateBackend的主要特性如下： 采用LSM + Hash索引的存储模型。其中LSM提高写性能，Hash索引存放在内存中以弥补LSM读 放大的缺点。简单来说，GeminiDB将文件划分为更小粒度的单元（Page），以Page为粒度进 行冲洗和压缩处理，Hash索引根据键快速定位其所在的单元，从而减少I/O次数，提高读性 能。 优化Cache策略。如果新插入的数据或压缩后的数据存在热点，GeminiDB会将其缓存在内存 中。而传统的LSM实现会首先将这些数据刷到磁盘上，导致新增数据需要至少经过一次读I/O 才能进入缓存，因此缓存命中率下降。 优化数据冲洗到磁盘的策略。GeminiDB只有在内存的缓存填满后才会将部分数据冲洗到磁盘， 因此，在内存充足并且压缩及时的情况下，不会产生数据文件。而传统LSM的实现中，会将 数据冲洗到磁盘以便进行持久化处理。这在Blink的应用场景中是没有必要的，Blink具有原 生的Checkpoint机制以保证数据的一致性，因此只需在Checkpoint时进行持久化处理。 支持In-memory Compaction。对于内存中数据及时进行压缩，减少写放大以及压缩的读I/O。 使用Java消除RocksDB/Niagara中的JNI开销。 支持增量Checkpoint。 支持Local Recovery，使作业失效后快速恢复。 支持存储计算分离，使作业重启或者Rescale后的快速恢复，功能还在完善中。

收购之后的一个文章 [https://zhuanlan.zhihu.com/p/89616731](https://zhuanlan.zhihu.com/p/89616731) Flink内置了两种状态存储，一种是基于Java Heap的State Backend状态存储插件，另一种 是基于RocksDB的状态存储插件。基于Java Heap的性能非常好，因为是完全基于JVM内存的， 并且没有序列化反序列化。但它的局限在于Java的方案内存容量会是瓶颈，因为Java对内存 的利用率非常低，不如序列化高。经过测试，在物理数据超过几百兆之后，内存的使用率超 过几个G就不能够扩大数据量了，所以系统非常不稳定。业界很多公司都是在用RocksDB来做， 这是非常优秀的开源KV存储，但因为是基于C++写的，所以和Flink的集成上还有很多不方便 的地方，同时RocksDB也不是为Flink设计的，所以Flink在很多状态的数据结构设计上没有 办法进行优化。我们希望针对Flink的状态存储来做一套自己的存储插件，可以提供更强大 的功能，同时也兼容社区的协议，所以Gemini应运而生。Gemini是完全存储计算分离的设计， 它和RocksDB有很大的不同，同时它也可以利用本地SSD做二级缓存来加速访问，尤其是在 Flink出现故障，一个Task失败，重新拉起一个进程时，它可以远程的从HDFS上直接拉起状 态，下载时间会大幅降低，提升了整个Flink SLB体验，包括它在设计的时候采用了Java， 和Flink系统间的整合也会更好。

Flink's backend refactered, state and checkpoint are separated now [https://ci.apache.org/projects/flink/flink-docs-release-1.13/docs/ops/state/state_backends/](https://ci.apache.org/projects/flink/flink-docs-release-1.13/docs/ops/state/state_backends/)

# InfiniBand

[https://zcopy.wordpress.com/tag/rdma/](https://zcopy.wordpress.com/tag/rdma/)

[https://en.wikipedia.org/wiki/InfiniBand](https://en.wikipedia.org/wiki/InfiniBand) Mellanox 技术白皮书《Introduction to InfiniBand》，下面以 4 links 计算：

1. SDR (Single Data Rate)：单倍数据率，即8Gb/s
2. DDR (Double Data Rate)：双倍数据率，即16Gb/s
3. QDR (Quad Data Rate)：四倍数据率，即32Gb/s
4. FDR (Fourteen Data Rate)：十四倍数据率，56Gb/s
5. EDR (Enhanced Data Rate)：100 Gb/s
6. HDR (High Data Rate)：200 Gb/s
7. NDR (Next Data Rate)：400 Gb/s
8. XDR： 1000 Gb/s

# VScode & Golang x509: certificate signed by unknown authority

Error reported by vscode when installing gopls etc. The same error in shell (see below).

```
$ go get golang.org/x/tools/gopls
package golang.org/x/tools/gopls: unrecognized import path "golang.org/x/tools/gopls" (https fetch: Get https://golang.org/x/tools/gopls?go-get=1: x509: certificate signed by unknown authority)
```

A dirty fix is adding the "-insecure" option.

```
$ go get -insecure golang.org/x/tools/gopls
```

Google suggest importing certificates of github.com and proxy.golang.org. Which is proved to have no use.

```
$ openssl s_client -showcerts -connect github.com:443 </dev/null 2>/dev/null|openssl x509 -outform PEM > ~/.crt/github.crt
$ openssl s_client -showcerts -connect proxy.golang.org:443 </dev/null 2>/dev/null|openssl x509 -outform PEM > ~/.crt/proxy.golang.crt
$ sudo cp ~/.crt/* /usr/local/share/ca-certificates/
$ sudo update-ca-certificates --verbose
```

The corporate certificates should be imported.

```
$ sudo cp foo.crt /usr/local/share/ca-certificates/
$ sudo update-ca-cerficates --verbose
```

And now, vscode works (installing go-outline).

```
Tools environment: GOPATH=/home/wsl/golang
Installing 1 tool at /home/wsl/golang/bin in module mode.
  go-outline

Installing github.com/ramya-rao-a/go-outline (/home/wsl/golang/bin/go-outline) SUCCEEDED

All tools successfully installed. You are ready to Go :).
```

# CockroachDB Optimizer

[https://youtu.be/wHo-VtzTHx0?t=2474](https://youtu.be/wHo-VtzTHx0?t=2474)

DPSUBE/On the correct and complete enumeration of the core search space [https://dl.acm.org/doi/10.1145/2463676.2465314](https://dl.acm.org/doi/10.1145/2463676.2465314)

[http://people.csail.mit.edu/rytaft/](http://people.csail.mit.edu/rytaft/)

# AIoT

[https://www.visualcapitalist.com/aiot-when-ai-meets-iot-technology/](https://www.visualcapitalist.com/aiot-when-ai-meets-iot-technology/)

# Data Visualization

[https://www.tableau.com/learn/articles/best-beautiful-data-visualization-examples](https://www.tableau.com/learn/articles/best-beautiful-data-visualization-examples) [http://www.puffpuffproject.com/languages.html](http://www.puffpuffproject.com/languages.html)

# A Conversation with Werner Vogels

2006: A Conversation with Werner Vogels (Jim Gray) [https://queue.acm.org/detail.cfm?id=1142065](https://queue.acm.org/detail.cfm?id=1142065)

2021: A Second Conversation with Werner Vogels (TOM KILLALEA) [https://m-cacm.acm.org/magazines/2021/3/250706-a-second-conversation-with-werner-vogels/fulltext](https://m-cacm.acm.org/magazines/2021/3/250706-a-second-conversation-with-werner-vogels/fulltext)

# David Wheeler

[https://en.wikipedia.org/wiki/David_Wheeler_(computer_scientist)](https://en.wikipedia.org/wiki/David_Wheeler_(computer_scientist))

Wheeler is often quoted as saying "All problems in computer science can be solved by another level of indirection."[26] or "All problems in computer science can be solved by another level of indirection, except for the problem of too many layers of indirection."

Another quotation attributed to him is "Compatibility means deliberately repeating other people's mistakes."

# Float number

What Every Computer Scientist Should Know About Floating-Point Arithmetic [https://docs.oracle.com/cd/E19957-01/806-3568/ncg_goldberg.html](https://docs.oracle.com/cd/E19957-01/806-3568/ncg_goldberg.html)

bfloat16 [https://en.wikipedia.org/wiki/Bfloat16_floating-point_format](https://en.wikipedia.org/wiki/Bfloat16_floating-point_format)

Comparing Floating Point Numbers, 2012 Edition - Many useful links [https://randomascii.wordpress.com/2012/02/25/comparing-floating-point-numbers-2012-edition/](https://randomascii.wordpress.com/2012/02/25/comparing-floating-point-numbers-2012-edition/)

Comparing Floating-Point Numbers Is Tricky - Follows the above link [https://bitbashing.io/comparing-floats.html](https://bitbashing.io/comparing-floats.html)

# Round float number

[https://stackoverflow.com/questions/485525/round-for-float-in-c](https://stackoverflow.com/questions/485525/round-for-float-in-c) [https://github.com/boostorg/math/blob/develop/include/boost/math/special_functions/round.hpp](https://github.com/boostorg/math/blob/develop/include/boost/math/special_functions/round.hpp) [https://stackoverflow.com/questions/4572556/concise-way-to-implement-round-in-c/4572877#4572877](https://stackoverflow.com/questions/4572556/concise-way-to-implement-round-in-c/4572877#4572877) [http://blog.frama-c.com/index.php?post/2013/05/02/nearbyintf1](http://blog.frama-c.com/index.php?post/2013/05/02/nearbyintf1) Part 1, 2, 3

A paper on float's not-determined results [https://arxiv.org/pdf/1802.09883.pdf](https://arxiv.org/pdf/1802.09883.pdf)

# Ninja

[https://david.rothlis.net/ninja-benchmark](https://david.rothlis.net/ninja-benchmark) Ninja determines the parallism by CPU counts, while make uses \`-j'.

[https://medium.com/@alasher/colored-c-compiler-output-with-ninja-clang-gcc-10bfe7f2b949](https://medium.com/@alasher/colored-c-compiler-output-with-ninja-clang-gcc-10bfe7f2b949) Ninja can't produce colorful output

```
$ cd build
$ cmake .. -DCMAKE_BUILD_TYPE=Release -GNinja
$ ninja
```

# Learned Index Updated

Microsoft: Not only read, but still single-threaded, see limitations listed on the page. [https://github.com/microsoft/ALEX](https://github.com/microsoft/ALEX)

Google: [http://mlforsystems.org/assets/papers/neurips2020/learned_abu-libdeh_2020.pdf](http://mlforsystems.org/assets/papers/neurips2020/learned_abu-libdeh_2020.pdf)

# Git download huge projects

Use ssh instead of https. If not using ssh, try this:

```
$ git config --global http.postBuffer 524288000
$ git clone http://github.com/large-repository --depth 1
$ cd large-repository
$ git fetch --unshallow # if failed, try: git fetch --depth (15|50|...) etc.
```

To debug it:

```
$ export GIT_TRACE_PACKET=1
$ export GIT_TRACE=1
$ export GIT_CURL_VERBOSE=1
```

# InfluxDB vs. TimescaleDB

[https://blog.timescale.com/blog/sql-vs-flux-influxdb-query-language-time-series-database-290977a01a8a/](https://blog.timescale.com/blog/sql-vs-flux-influxdb-query-language-time-series-database-290977a01a8a/)

# CMake dependencies

[https://floooh.github.io/2016/01/12/cmake-dependency-juggling.html](https://floooh.github.io/2016/01/12/cmake-dependency-juggling.html) [http://www.saoe.net/blog/using-cmake-with-external-projects/](http://www.saoe.net/blog/using-cmake-with-external-projects/)

1. External project
2. TARGET\_XXX are better

# VScode

## CMakeLists.txt with arguments

```
$ cat .vscode/settings.json

"cmake.parallelJobs": 1,   // default is unlimited

"cmake.configureArgs": [
	"-DWITH_BOOST=/home/wsl/boost_1_70_0"
    ],
```

## VSCode can't download packages in WSL2?

Maybe caused by certificate error behind proxy. Add \`check-certificate=off ' to ~/.wgetrc.

# WSL2

## ffmpeg & convert

To reduce the number of png files generated by ffmpeg, the fps option can be changed from 1/1 to 1/24 or other numbers.

```
$ filename=funny
$ ffmpeg -i funny.mp4 -r 1/1 $filename%03d.png 
$ convert $filename*.png $filename.pdf    # The page are stripped?
$ img2pdf $filename*.png -o $filename.pdf # It works better.
```

## How to reconfig and select default locale, such as en\_US.UTF-8, zh\_CN.UTF-8.

$ sudo dpkg-reconfigure locales

## How to fix the problem of "font changed to 新宋体 in vim or tmux"?

1. Dangerous, it change the default code page! Should not do it.

[HKEY\_LOCAL\_MACHINE\SYSTEM\CurrentControlSet\Control\Nls\CodePage] "ACP"`"1252" "MACCP"`"10000" "OEMCP"="437"

1. Change the code page of cmd. Not work.

[https://blog.csdn.net/MobiuX/article/details/82194028](https://blog.csdn.net/MobiuX/article/details/82194028)

HKEY\_CURRENT\_USER\Console\C:\_Program Files\_WindowsApps\_CanonicalGroupLimited.Ubuntu18.04onWindows\_XXX\_ubuntu1804.exe 添加：CodePage（DWORD类型、值0x01b5），解决问题。

1. Change the TERM from xterm-256color to xterm-color. VERIFIED.

HKEY\_CURRENT\_USER\Software\Microsoft\Windows\CurrentVersion\Lxss\\{5bcb0db1-1263-4893-9ed4-19c28694e27d} DefaultEnvironment: TERM=xterm-256color => TERM=xterm-color

## How to fix Bash command line's  default to overwrite mode

$ ssty -a &#x2026; discard = ^O &#x2026;

Add Control-o: overwrite-mode to your $HOME/.inputrc or /etc/inputrc to use Ctrl+o to toggle between insert mode and override mode.

## How to fix error: RPC failed; curl 56 GnuTLS recv error (-110): The TLS connection was non-properly terminated.

[https://www.jianshu.com/p/90909f86d430](https://www.jianshu.com/p/90909f86d430)

```
sudo apt-get install build-essential fakeroot dpkg-dev
sudo vim /etc/apt/sources.list
mkdir git-source
cd git-source/
sudo apt source git
sudo apt build-dep git
sudo apt-get install libcurl4-openssl-dev
cd git-*
vim ./debian/control # libcurl4-gnutls-dev 修改为 libcurl4-openssl-dev
vim ./debian/rules # 注释TEST=test行
sudo dpkg-buildpackage -rfakeroot -b
cd .. 
sudo dpkg -i git_*_amd64.deb
```

## Date not synced

```
$ sudo hwclock -s

Or
$ wsl --shutdown

Or
$ sudo ntpdate time.windows.com
```

## MySQL related

### /usr/bin/sleep not work

```
sudo mv /usr/bin/sleep /usr/bin/sleep.orig
sudo ln -s "/mnt/c/Program Files/Git/usr/bin/sleep.exe" /usr/bin/sleep
```

### sudo service mysql start

1. script: /etc/init.d/mysql
2. debug it with set -x
3. debug the mysqld\_safe command with stderr/out to /tmp/mysqld\_safe.log

### mysql -u root not work

[https://phoenixnap.com/kb/access-denied-for-user-root-localhost](https://phoenixnap.com/kb/access-denied-for-user-root-localhost)

```
$ grep mysql /etc/passwd
mysql:x:112:119:MySQL Server,,,:/nonexistent:/bin/false => /bin/bash

$ sudo service mysql start
$ sudo mysql  # this command can login as root
```

If \`sudo mysql' works, then follow the intructions in the above link:

```
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'insert_password';
```

# SQLancer

[https://www.youtube.com/watch?v=Np46NQ6lqP8](https://www.youtube.com/watch?v=Np46NQ6lqP8) Finding Logic Bugs in Database Management Systems (Manuel Rigger, ETH SQLancer)

# Show Table Border in Jekyll GitHub Pages

GitHub pages with Jekyll can't show table border by default (using the md exported from Emacs org). The \`frame' attribute of `<table>` has no effect, which should be replaced by \`class' attribue as fllowing.

```
<style>
.tablelines table, .tablelines td, .tablelines th {
    border: 1px solid black;
}
</style>

<table class="tablelines" cellspacing="0" cellpadding="6" rules="all" width="100%">
```

# PIC vs. pic vs. PIE vs. pie

## PIC vs. pic, PIE vs. pie

GCC offial document: [https://gcc.gnu.org/onlinedocs/gcc-7.2.0/gcc/Code-Gen-Options.html#Code-Gen-Options](https://gcc.gnu.org/onlinedocs/gcc-7.2.0/gcc/Code-Gen-Options.html#Code-Gen-Options)

## PIC vs. PIE

[https://mropert.github.io/2018/02/02/pic_pie_sanitizers/](https://mropert.github.io/2018/02/02/pic_pie_sanitizers/)

Hard rules: Position Independent executables may be created from PIC or PIE objects (.o) and PIC or PIE static libraries (.a). PIC shared libraries can only be created from PIC objects or static libraries. Non-PIE executables can be created from any objects or static libraries.

In practice, the most simple solution would be to build everything with -fPIC and be done with it. But this is C++, we like to brag about the fact that we only pay for what we use, so obviously a finer rule may be needed:

Easy rules: If the object is to be linked as a shared library, or a static library that will in turn be linked in a shared library, use -fPIC. If the object is to be linked as a position indenpendent executable, or a static library that will in turn be linked in position independent executable, use -fPIE; Else, use neither.

## What's the hell on -fPIC -fPIE for static linked libraries?

[https://lists.debian.org/debian-devel/2016/05/msg00309.html](https://lists.debian.org/debian-devel/2016/05/msg00309.html) From a hardening perspective, any code that is added to static libraries should be compiled with -fPIE if the static library will only ever be used in executables, and with -fPIC if it also might be used in shared libraries. (Although, to be honest, that use case is a bit rarer.)

# Canary Word

[https://gist.github.com/zedware/1131346b801a25d0fcf0157ddab0aa77](https://gist.github.com/zedware/1131346b801a25d0fcf0157ddab0aa77)

# Ubuntu's Rename Command

[https://github.com/tldr-pages/tldr/issues/3125](https://github.com/tldr-pages/tldr/issues/3125)

Happen to find a reference in a makefile, and encounter an error during the invoke of rename.

/usr/bin/rename.ul is the rename command found in linux-util package for other Linux distributions. \`sudo apt install rename' is the Perl package.

# Ring Buffer

[https://www.snellman.net/blog/archive/2016-12-13-ring-buffers/](https://www.snellman.net/blog/archive/2016-12-13-ring-buffers/)

Naive vs. Linux Kernel method

# Add new services to macOS

Example add \`Search with Bing' to macOS, use it in Safari, Mail, Finder etc. [https://www.macworld.com/article/3293051/how-to-add-more-contextual-search-services-in-macos.html](https://www.macworld.com/article/3293051/how-to-add-more-contextual-search-services-in-macos.html)

# The Aggregate Magic Algorithms

[http://aggregate.org/MAGIC/#Trailing%20Zero%20Count](http://aggregate.org/MAGIC/#Trailing%20Zero%20Count)

# ONE DIV ZERO - Blog

A Brief, Incomplete, and Mostly Wrong History of Programming Languages [http://james-iry.blogspot.com/2009/05/brief-incomplete-and-mostly-wrong.html](http://james-iry.blogspot.com/2009/05/brief-incomplete-and-mostly-wrong.html)

# 布尔函数的敏感度猜想

[https://www.quantamagazine.org/mathematician-solves-computer-science-conjecture-in-two-pages-20190725/](https://www.quantamagazine.org/mathematician-solves-computer-science-conjecture-in-two-pages-20190725/)

# Lord of io\_uring

[https://unixism.net/loti/](https://unixism.net/loti/)

# Notes on the word \`touché'.

1. touché *tuːˈʃeɪ*, which originates from French.

(in fencing) used as an acknowledgment of a hit by one's opponent. used as an acknowledgment during a discussion of a good or clever point made at one's expense by another person.

1. It is also found in a famous company name(德勤 in Chinese).

Deloitte & Touche is the Deloitte Touche Tohmatsu Limited (DTTL) member firm.

1. And the most funnny thing is that there is a word in Chinese(透彻) with

the same meaning and similiar pronounciation.

# Why Zoom Choose Oracle Cloud

[https://www.lastweekinaws.com/blog/why-zoom-chose-oracle-cloud-over-aws-and-maybe-you-should-too/](https://www.lastweekinaws.com/blog/why-zoom-chose-oracle-cloud-over-aws-and-maybe-you-should-too/) The cost of data transfer is about 10x high in AWS than OCI

# Understanding and Benchmarking the Impact of GDPR on Database Systems

VLDB 2020. Analyze GPDR and tests with Redis, Pgsql and An Commercial System. Very good on GPDR and a benchmark called GPDRbench.

# MongoDB and MMAP

[https://engineering.mongodb.com/post/getting-storage-engines-ready-for-fast-storage-devices](https://engineering.mongodb.com/post/getting-storage-engines-ready-for-fast-storage-devices)

References: SOSP 19 paper

1. memcpy with mmap uses AVX, which is faster than memcpy across user/system call
2. batch operations of file truncation (which is a common wise)
3. read/write conclicts controled by atomic add/decr and backfail to syscall when conflict

# Classification of open source programmers

1. Those design and implement new features by their own 👍
2. Those copy and merge patches for their own version
   1. Those copy and merge blindly 😢
   2. Those select, study and merge with choices 😁

# Andy Pavlo's Course

[https://www.youtube.com/watch?v=Hn4l8XC3-PQ&amp;list=PLSE8ODhjZXjasmrEd2_Yi1deeE360zv5O&amp;index=15](https://www.youtube.com/watch?v=Hn4l8XC3-PQ&list=PLSE8ODhjZXjasmrEd2_Yi1deeE360zv5O&index=15)

## Fixed number

Book: Hacker's Delight

## Vector Execution Engine

Looks like data size execeed cache size means no effect.

# UCSD Paper on Optane

[https://www.usenix.org/conference/fast20/presentation/yang](https://www.usenix.org/conference/fast20/presentation/yang)

1. 读慢 (2x-3x DRAM)，写快(1x DRAM)
2. 内部存在块粒度(256 字节)
3. 读写带宽不对称(4 个线程就会耗尽写带宽)

# Oracle sue Google

[https://www.zdnet.com/article/google-vs-oracle-the-next-chapter/](https://www.zdnet.com/article/google-vs-oracle-the-next-chapter/) Google vs. Oracle: The next chapter May 12, 2020 &#x2013; 13:49 GMT (21:49 GMT+08:00)

[https://arstechnica.com/tech-policy/2020/03/before-it-sued-google-for-copying-from-java-oracle-got-rich-copying-ibms-sql/](https://arstechnica.com/tech-policy/2020/03/before-it-sued-google-for-copying-from-java-oracle-got-rich-copying-ibms-sql/)

SCOTUS — Before it sued Google for copying from Java, Oracle got rich copying IBM’s SQL Oracle's history highlights a possible downside to its stance on API copyrights.

TIMOTHY B. LEE - 3/10/2020, 11:21 PM

# SIGEMT

Encountered a wierd signal for mysqld.

EMT means Emulator trap. Processes receive this signal when an emulator trap occurs.

SIGEMT (EMT trap) In theory this signal is generated when an instruction needs to be emulated. The default action is to terminate the process and dump core. The number for SIGEMT is 7.

The backtrace is in jemalloc. Another mysqld instance is in new.

The computer is unstable, raid caused fs RO, memory usage is high.

Not found related source code for x64 in Linux.

```

$ grep -rwIn SIGEMT .
./tools/perf/trace/beauty/signum.c:42:#ifdef SIGEMT
./security/apparmor/include/sig_names.h:49:#ifdef SIGEMT
./security/apparmor/include/sig_names.h:50:	[SIGEMT] = 32,		/* 7, - , 7 */
./include/uapi/asm-generic/siginfo.h:63:	/* SIGILL, SIGFPE, SIGSEGV, SIGBUS, SIGTRAP, SIGEMT */
./include/uapi/asm-generic/siginfo.h:290: * SIGEMT si_codes
./include/linux/signal.h:364: *	|  SIGEMT            |  coredump	|
./include/linux/signal.h:378:#ifdef SIGEMT
./include/linux/signal.h:379:#define SIGEMT_MASK	rt_sigmask(SIGEMT)
./include/linux/compat.h:216:		/* SIGILL, SIGFPE, SIGSEGV, SIGBUS, SIGTRAP, SIGEMT */
./arch/mips/include/uapi/asm/signal.h:32:#define SIGEMT		 7
./arch/mips/kernel/mips-r2-to-r6-emul.c:243: * Returns SIGILL if JR was in delay slot, SIGEMT if we
./arch/mips/kernel/mips-r2-to-r6-emul.c:264:		return SIGEMT;
./arch/mips/kernel/mips-r2-to-r6-emul.c:289:				err = SIGEMT;
./arch/mips/kernel/mips-r2-to-r6-emul.c:922:		return SIGEMT;
./arch/mips/kernel/mips-r2-to-r6-emul.c:997:				return SIGEMT;
./arch/mips/kernel/mips-r2-to-r6-emul.c:1039:						err = SIGEMT;
./arch/mips/kernel/mips-r2-to-r6-emul.c:1054:				return SIGEMT;
./arch/mips/kernel/mips-r2-to-r6-emul.c:1088:						err = SIGEMT;
./arch/mips/kernel/mips-r2-to-r6-emul.c:1123:			return SIGEMT;
./arch/mips/kernel/mips-r2-to-r6-emul.c:1165:					err = SIGEMT;
./arch/mips/kernel/mips-r2-to-r6-emul.c:2224:	if (err && (err != SIGEMT)) {
./arch/mips/kernel/traps.c:1133:		case SIGEMT:
./arch/mips/kernel/asm-offsets.c:265:	DEFINE(_SIGEMT, SIGEMT);
./arch/alpha/include/uapi/asm/signal.h:29:#define SIGEMT		 7
./arch/sparc/include/uapi/asm/signal.h:25:#define SIGEMT           7
./arch/sparc/kernel/traps_32.c:308:	send_sig_fault(SIGEMT, EMT_TAGOVF, (void __user *)pc, 0, current);
./arch/sparc/kernel/traps_64.c:2390:		       0, 0x26, SIGEMT) == NOTIFY_STOP)
./arch/sparc/kernel/traps_64.c:2399:	force_sig_fault(SIGEMT, EMT_TAGOVF,
./kernel/signal.c:3115:#if defined(SIGEMT)
./kernel/signal.c:3116:	[SIGEMT]  = { NSIGEMT,  SIL_FAULT },

```

# Real time data processing

Easy to read [https://blog.pythian.com/defining-real-time-or-streaming-analytics-and-its-use-cases/](https://blog.pythian.com/defining-real-time-or-streaming-analytics-and-its-use-cases/)

# The Seattle Report on Database Research

[https://sigmodrecord.org/publications/sigmodRecord/1912/pdfs/07_Reports_Abadi.pdf](https://sigmodrecord.org/publications/sigmodRecord/1912/pdfs/07_Reports_Abadi.pdf)

Approximately every five years, a group of database re- searchers meet to do a self-assessment of our commu- nity, including reflections on our impact on the industry as well as challenges facing our research community. This report summarizes the discussion and conclusions of the 9th such meeting, held during October 9-10, 2018 in Seattle.

# Paper etc.

[https://www.usenix.org/system/files/nsdi20-paper-brooker.pdf](https://www.usenix.org/system/files/nsdi20-paper-brooker.pdf)

Millions of Tiny Databases Marc Brooker, Tao Chen, and Fan Ping, Amazon Web Services

[https://blog.acolyer.org/2020/02/19/ten-year-egml-predictions/](https://blog.acolyer.org/2020/02/19/ten-year-egml-predictions/) Cloudy with a high chance of DBMS: a 10-year prediction for enterprise-grade ML FEBRUARY 19, 2020

[https://mikaelronstrom.blogspot.com/2020/02/use-cases-for-mysql-ndb-cluster-80.html](https://mikaelronstrom.blogspot.com/2020/02/use-cases-for-mysql-ndb-cluster-80.html) Use Cases for MySQL NDB Cluster 8.0

Meaningful availability [https://www.usenix.org/system/files/nsdi20spring_hauer_prepub.pdf](https://www.usenix.org/system/files/nsdi20spring_hauer_prepub.pdf)

# lldb and hardware watchpoint

[https://reverse.put.as/2019/11/19/how-to-make-lldb-a-real-debugger/](https://reverse.put.as/2019/11/19/how-to-make-lldb-a-real-debugger/)

Ref to \`Step number 3: LLDB logging'.

# Decompile jar with InteliJ

[https://github.com/JetBrains/intellij-community/tree/master/plugins/java-decompiler/engine](https://github.com/JetBrains/intellij-community/tree/master/plugins/java-decompiler/engine)

Verified on macOS.

The jar file name was changed from fernflower.jar to java-decompiler.jar. And doesn't have manifest file embedded, so must use jar -cp instead of jar -jar to invoke it.

```
$ cd /Users/zedware/Library/Application Support/JetBrains/Toolbox/apps/IDEA-C/ch-0/191.7479.19/IntelliJ IDEA CE.app/Contents/plugins/java-decompiler/lib
$ java -cp ./java-decompiler.jar org.jetbrains.java.decompiler.main.decompiler.ConsoleDecompiler -dgs=true ~/Downloads/test.jar /tmp/decompiled
```

The decompiled Java source files are archived into test.jar in /tmp/decomplied.

# MySQL Client in Python

A Terminal Client for MySQL with AutoCompletion and Syntax Highlighting. [https://github.com/dbcli/mycli](https://github.com/dbcli/mycli)

A simpler version. [https://github.com/zedware/notebook/blob/master/samples/python/isql.py](https://github.com/zedware/notebook/blob/master/samples/python/isql.py)

# Notes on the BPF Book

1. Why execsnoop can't list the invocation of top in:

```
$ top | awk '{print $1}'
```

A single top command can be listed.

1. execsnoop can be used to check the login actions.

For example, trace it in session A.

```
$ sudo bcc/tools/execsnoop.py 
```

Then, login through ssh to the QEMU Ubuntu.

```
$ ssh localhost
```

1. Error creating printf map: Invalid argument?

[https://github.com/iovisor/bpftrace/issues/1014](https://github.com/iovisor/bpftrace/issues/1014)

Wait for the fix, then rebuild bpftrace according to the bpftrace/INSTALL.md.

1. mysql

```
$ sudo mysql -uroot -p test
```

1. sar not enabled by default on Ubuntu

In /etc/default/sysstat, change ENABLED="false" to ENABLED="true". In /etc/cron.d/sysstat, there is already two lines for debian-sa1. Restart the service: sudo service sysstat restart

# SYN Flood

1. net.ipv4.tcp\_max\_syn\_backlog = 128
2. net.core.somaxconn = 128
3. listen()'s back in Application code
4. Why mysql client see the connection as established while server not see it?

```
1. The proto requires client to wait for server to send the first message.
2. The server gets too many connections at once, so it enables syncookies.
3. The client responds with a big batch of ACKs at the same time, so some 
   of them get lost somewhere. [WHY]
```

Normal case: three-way handshake

```
client     server
SYN
	   SYN+ACK
	   Save the connection info
ACK
```

In SYN Flooding case, server with not save the connection info, but drop it. [https://en.wikipedia.org/wiki/SYN_cookies](https://en.wikipedia.org/wiki/SYN_cookies)

```
When a client sends back a TCP ACK packet to the server in response to the 
server's SYN+ACK packet, the client must (according to the TCP spec) use n+1 
in the packet's Acknowledgement number, where n is the initial sequence number 
sent by the server. The server then subtracts 1 from the acknowledgement number 
to reveal the SYN cookie sent to the client.

Drawbacks of SYN cookies.

Firstly, the server is limited to only 8 unique MSS values, as that is all that
can be encoded in 3 bits. Secondly, the server must reject all TCP options (such
as large windows or timestamps), because the server discards the SYN queue entry
where that information would otherwise be stored.[1] . Finally, SYN cookies 
place increased load on server resources. Encrypting responses is 
computationally expensive. 

A problem arises when the connection-finalizing ACK packet sent by the client is
lost, and the application layer protocol requires the server to speak first 
(SMTP and SSH are two examples. XXX: And MySQL). In this case, the client 
assumes that the connection was established successfully and waits for the 
server to send its protocol banner, or resend the SYN+ACK packet; however, the 
server is not aware of the session and will not resend the SYN+ACK because it
discarded the backlog queue entry that would enable it to do so. Eventually, the
client will abort the connection due to an application layer timeout, but this 
may take a relatively long time.
```

All you need to know about SYN floods [https://blog.dubbelboer.com/2012/04/09/syn-cookies.html](https://blog.dubbelboer.com/2012/04/09/syn-cookies.html)

Figure 10-2 TCP SYN backlogs [https://learning.oreilly.com/library/view/bpf-performance-tools/9780136588870/ch10.xhtml](https://learning.oreilly.com/library/view/bpf-performance-tools/9780136588870/ch10.xhtml)

MySQL's back\_log defaults to max\_connections [https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html)

In mysqld.cc:

```
/* Fix back_log */
if (back_log == 0 && (back_log = max_connections) > 65535) back_log = 65535;
```

TCP SYN cookies make window size suddenly becomes smaller [https://hustcat.github.io/tcp_syn_cookies_and_window_size/](https://hustcat.github.io/tcp_syn_cookies_and_window_size/)

MySQL's protocol [https://dev.mysql.com/doc/dev/mysql-server/8.0.11/page_protocol_connection_phase.html](https://dev.mysql.com/doc/dev/mysql-server/8.0.11/page_protocol_connection_phase.html) Client may hang on the recv of server's initial handshake packet.

Redhat's solution page [https://access.redhat.com/solutions/30453](https://access.redhat.com/solutions/30453)

An example to reproduce it in MySQL easily can be found at the end of the blog. [https://www.evanjones.ca/tcp-stuck-connection-mystery.html](https://www.evanjones.ca/tcp-stuck-connection-mystery.html)

```
1. start mysqld
2. sudo kill -STOP `pgrep mysqld`
3. start sysbench to create many connections.
4. sudo kill -CONT 'pgrep mysqld'
5. dmesg | grep -i cookies
[Jan 4 04:50] TCP: request_sock_TCP: Possible SYN flooding on port 3306. Sending cookies.  Check SNMP counters.
```

# Computer History

## Hardware and system software

1. 1990 Apple II, Diskless, BASIC and LOGO
2. 1993 IBM PC, Intel 8088, 5 Inch floppy disk, DOS 3.3
3. 1995 80286, 3 inch floopy disk, WPS, CCED, Dbase, Foxbase
4. 1997 80486, Windows 3.1, Borland C++ 3.x, Foxpro, Visual Foxpro
5. 1997 VAX, Oracle 7
6. 1998 Windows 95/98/NT, 200M disk, SCO UNIX, Linux, AIX, Solaris, Visual C++ 5.0
7. 2000 OS/370, OS/390, IBM, EMC, COBOL, HASM, DB2
8. 2001 PocketPC, Windows embedded, Intel xScale, Dragonball, SH4, Windows XP
9. 200X Windows XP/NT/200X
10. 2010 PC Server, UNIX, Solaris, Linux, Symantec, EMC, Dell, VMAX, VNX
11. 2013 PC Server, SSD, 256GiB, 1Gbps \* 2, Fusion IO
12. 2018 PC Server, 768GiB, 25GiB \* 2, Optane, DCPMM, ARM

## Internet

1. 1997 Newsgroup, Telnet/BBS, Gopher
2. 2000 Forum, Sina.com, Search engine
3. 200x Blog, MSN, QQ, Web
4. 201x WeChat, Alipay, Slack, Facebook, Twitter, Weibo
5. 201x Instagram, What's App, ByteDance?

# Constant Folding in MySQL 8.0

[https://dev.mysql.com/worklog/task/?id=11935](https://dev.mysql.com/worklog/task/?id=11935) WL#11935: Add folding of constants when compared to fields

# Linux load average

LA 为什么采用指数衰减移动平均数？ [https://www.helpsystems.com/resources/guides/unix-load-average-part-1-how-it-works](https://www.helpsystems.com/resources/guides/unix-load-average-part-1-how-it-works) (Several articles)

LA 为什么将不可中断状态也计算进去了？ [http://www.brendangregg.com/blog/2017-08-08/linux-load-averages.html](http://www.brendangregg.com/blog/2017-08-08/linux-load-averages.html)

早些年的 OS 以及其他的 OS 似乎都是不算这个的。评论中有人说 BSD 一直是这样的。

引入这个修改的邮件：

```
From: Matthias Urlichs <urlichs@smurf.sub.org>
Subject: Load average broken ?
Date: Fri, 29 Oct 1993 11:37:23 +0200


The kernel only counts "runnable" processes when computing the load average.
I don't like that; the problem is that processes which are swapping or
waiting on "fast", i.e. noninterruptible, I/O, also consume resources.

It seems somewhat nonintuitive that the load average goes down when you
replace your fast swap disk with a slow swap disk...

Anyway, the following patch seems to make the load average much more
consistent WRT the subjective speed of the system. And, most important, the
load is still zero when nobody is doing anything. ;-)
```

# Hudi, Iceberg, Delta Lake etc.

Keywords: stream vs. batch, MemSQL and ES for TB, HDFS for PB

Hudi is from Uber, an incremental data ingest framework. Query engine: presto, impala etc. Storage engine: parquet, orc etc. End2end Latency: < 10 minutes Update/Delete: must have Steam latency: < 1 minute [https://eng.uber.com/hoodie/](https://eng.uber.com/hoodie/)

Iceberg is from Netflix, a table format. [https://iceberg.incubator.apache.org/spec/#appendix-a-format-specific-requirements](https://iceberg.incubator.apache.org/spec/#appendix-a-format-specific-requirements)

Delta Lake [https://www.iteblog.com/archives/2592.html](https://www.iteblog.com/archives/2592.html) 通过写新文件来实现删除

[https://databricks.com/session_eu19/acid-orc-iceberg-and-delta-lake-an-overview-of-table-formats-for-large-scale-storage-and-analytics](https://databricks.com/session_eu19/acid-orc-iceberg-and-delta-lake-an-overview-of-table-formats-for-large-scale-storage-and-analytics)

# AWS Redshift

[https://aws.amazon.com/blogs/aws/new-for-amazon-redshift-data-lake-export-and-federated-queries/](https://aws.amazon.com/blogs/aws/new-for-amazon-redshift-data-lake-export-and-federated-queries/)

1. Data Lake Export to unload data from a Redshift cluster to S3 in Apache Parquet format, an efficient open columnar storage format optimized for analytics.
2. Federated Query to be able, from a Redshift cluster, to query across data stored in the cluster, in your S3 data lake, and in one or more Amazon Relational Database Service (RDS) for PostgreSQL and Amazon Aurora PostgreSQL databases.

Redshift Spectrum, Amazon Athena, Amazon EMR, or Amazon SageMaker can visit data in S3.

# Why mmap is faster than syscall?

[https://medium.com/@sasha_f/why-mmap-is-faster-than-system-calls-24718e75ab37](https://medium.com/@sasha_f/why-mmap-is-faster-than-system-calls-24718e75ab37)

\_\_memmove\_avx\_unaligned\_erms, called in the mmap experiment, is implemented using Advanced Vector Extensions (AVX) (here is the source code of the functions that it relies on). The implementation of copy\_user\_enhanced\_fast\_string, on the other hand, is much more modest. That, in my opinion, is the huge reason why mmap is faster. Using wide vector instructions for data copying effectively utilizes the memory bandwidth, and combined with CPU pre-fetching makes mmap really really fast.

# OOM Killer

oom\_score\_adj: negative value is special [https://dev.to/rrampage/surviving-the-linux-oom-killer-2ki9](https://dev.to/rrampage/surviving-the-linux-oom-killer-2ki9)

# llvm-toolset

[https://access.redhat.com/documentation/en-us/red_hat_developer_tools/2019.1/html-single/using_clang_and_llvm_toolset/index](https://access.redhat.com/documentation/en-us/red_hat_developer_tools/2019.1/html-single/using_clang_and_llvm_toolset/index)

llvm-toolset-7 is clang 5.0.1, while llvm-toolset-7.0 is clang 7.0.1.

# WSL and cygpath

```
 wslpath usage:
     -a    force result to absolute path format
     -u    translate from a Windows path to a WSL path (default)
     -w    translate from a WSL path to a Windows path
     -m    translate from a WSL path to a Windows path, with ‘/’ instead of ‘\\’

     EX: wslpath 'c:\users'

~/.bashrc file:
alias cdw='f(){ cd wslpath $1; unset -f f; }; f'

$ cdw "c:\the\windows\target\path"
```

# Who and cwd

```
#!/usr/bin/env bash

if [ x"root" != x"`id -un`" ]
then
  echo "Effective id should be root"
  exit 1
fi

grep=/usr/bin/grep
me=$PPID
for x in `ps -ef | $grep "\-bash" | $grep -v grep | awk '{print $2}' | sort -u`
do
  if [ $x -eq $me ]
  then
    msg="*"
  else
    msg=" "
  fi

  echo "$msg $x: " `ls -al /proc/$x/ | $grep '^l' | $grep cwd |  awk '{print $3,$11}'`
done
```

# The Most Popular Programming Language

[https://www.bfcamara.com/posts/most-popular-programming-langs-1965-2019](https://www.bfcamara.com/posts/most-popular-programming-langs-1965-2019)

In summary:

1. From 1965 to 1980, Fortran kept the 1st place
2. In 1980, Pascal got first and kept on top for five years.
3. The C programming language got its main popularity from 1985 until 2001.
4. Java has been on top in the 21st century. JavaScript comes next most of the time, with an increase over the years.
5. Until 2019, where JavaScript and Python got the top of popularity.

# Auto Refresh JS Web Page

Run the code in browser's development console.

timeout = prompt("Timeout in seconds:"); count = 0 current = location.href; if (timeout > 0) setTimeout('reload()', 1000 \* timeout); else location.replace(current);

function reload() { setTimeout('reload()', 1000 \* timeout); count++; console.log('Refresh every ' + timeout + ' seconds, times: ' + count); fr4me = '<frameset cols=\\'\*\\'>\n<frame src=\\'' + current + '\\'/>'; fr4me += '`</frameset>`'; with(document) { write(fr4me); void(close())}; }

# GTID mode & last gtid

[http://code.openark.org/blog/mysql/quick-hack-for-gtid_own-lack](http://code.openark.org/blog/mysql/quick-hack-for-gtid_own-lack)

mysql> select gtid\_subset('f7b781a9-cbbd-11e9-affb-008cfa542442:123450000', @@global.gtid\_executed) as transaction\_found;

[https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html)

Can track session gtid and return it to client through protocol. mysql> SET SESSION session\_track\_gtids = OWN\_GTID

[http://devdoc.net/database/MySQL-refman-5.7/connectors-apis.html](http://devdoc.net/database/MySQL-refman-5.7/connectors-apis.html)

```
/* extract any available session state-change information */
enum enum_session_state_type type;
for (type = SESSION_TRACK_BEGIN; type <= SESSION_TRACK_END; type++)
{
  const char *data;
  size_t length;

  if (mysql_session_track_get_first(mysql, type, &data, &length) == 0)
  {
    printf("Type=%d:\n", type);
    printf("mysql_session_track_get_first() returns: %*.*s\n",
	    (int) length, (int) length, data);

    /* check for more data */
    while (mysql_session_track_get_next(mysql, type, &data, &length) == 0)
    {
      printf("mysql_session_track_get_next() returns: %*.*s\n",
	      (int) length, (int) length, data);
    }
  }
}
```

The idea is learnt by MariaDB. [https://jira.mariadb.org/browse/MDEV-15477](https://jira.mariadb.org/browse/MDEV-15477)

# Security Coding

strncpy、strncat、snprintf warnings improved in gcc8 [https://developers.redhat.com/blog/2018/05/24/detecting-string-truncation-with-gcc-8/](https://developers.redhat.com/blog/2018/05/24/detecting-string-truncation-with-gcc-8/)

标准库里头的安全缺陷太多，平常都要靠代码检查工具，例如 KlocWork、Coverity 等来辅 助检查。这方面还是微软在吃了苦头之后，改进迅速，很多年前就搞了带 \_s 后缀的增强版本。

[https://www.us-cert.gov/bsi/articles/knowledge/coding-practices/strncpy-and-strncat](https://www.us-cert.gov/bsi/articles/knowledge/coding-practices/strncpy-and-strncat)

# In-Memory Oracle

ICDE 2018 Paper on Agg push down, Join push down? [https://youtu.be/sE_JEeHQChY?t=2746](https://youtu.be/sE_JEeHQChY?t=2746)

Hot, Middle, Cold vs. LRU 推测粒度比 LRU 的页面要大很多。 怎么判断冷热？怎么判断是暂时性的热？

TPC-H 的 LINEITEM 的 L\_COMMENT 不会被访问。

# Partial Page Write

1. MySQL 用的是方法是：DOUBLE WRITE BUFFER
2. PostgreSQL 用的是在日志中写全页：FULL PAGE WRITE
3. Oracle、SQL Server 看来都没有类似的机制。Oracle 在启动备份命令后也有类似日志 中写全页面的机制。

推测原因：

1. 开源数据库运行的软硬件环境要更恶劣，出现类似问题的概率更大。万一出错了可以靠 物理备份来恢复或者做主备切换。
2. 企业级数据库大多有很好的备份措施，万一出错还提供了页面级别的修复手段。

在检查坏页面的机制上，大家都差不多，依靠页上的特殊标记或计算校验值。不过企业级数 据库的相关机制和工具更为丰富。

# IPTV collections

[https://github.com/iptv-org/iptv](https://github.com/iptv-org/iptv)

# 祖传代码

谁家的祖传代码中都或多或少有一些”宝贝“。例如：

1. 写个嵌套几十层的 if 语句，每层缩进 8 个空格，导致屏幕上看起来缺了一大段代码。
2. 乱起名字的，例如给变量命名为 my\_son, my\_father, my\_wife。
3. 代码中埋下个人信息的，例如把端口号设置为某人的生日。
4. 英语不好，例如把 babysitter 写成 babysister。
5. 废旧代码不删除干净的。
6. 留下不知真假的注释的。

如果留有完成的版本管理历史，有考据癖的可能还能找到原因，否则就只能是祖先的传说了。

# MySQL NoSQL interface

## HANDLER statement

```
mysql> help handler
Name: 'HANDLER'
Description:
Syntax:
HANDLER tbl_name OPEN [ [AS] alias]

HANDLER tbl_name READ index_name { = | <= | >= | < | > } (value1,value2,...)
    [ WHERE where_condition ] [LIMIT ... ]
HANDLER tbl_name READ index_name { FIRST | NEXT | PREV | LAST }
    [ WHERE where_condition ] [LIMIT ... ]
HANDLER tbl_name READ { FIRST | NEXT }
    [ WHERE where_condition ] [LIMIT ... ]

HANDLER tbl_name CLOSE

The HANDLER statement provides direct access to table storage engine
interfaces. It is available for InnoDB and MyISAM tables.

URL: http://dev.mysql.com/doc/refman/8.0/en/handler.html
```

## HandlerSocket plugin

[http://dev.mysql.com/doc/ndbapi/en/index.html](http://dev.mysql.com/doc/ndbapi/en/index.html) NDBAPI [http://yoshinorimatsunobu.blogspot.com/2010/10/using-mysql-as-nosql-story-for.html](http://yoshinorimatsunobu.blogspot.com/2010/10/using-mysql-as-nosql-story-for.html)

## memcached plugin for InnoDB

[https://dev.mysql.com/doc/refman/8.0/en/innodb-memcached.html](https://dev.mysql.com/doc/refman/8.0/en/innodb-memcached.html)

# B+ tree library

[http://panthema.net/2007/stx-btree/](http://panthema.net/2007/stx-btree/) 这个是纯内存的实现，论文有用它的。

号称新版本在：[https://github.com/tlx/tlx/tree/master/tlx/container](https://github.com/tlx/tlx/tree/master/tlx/container)

# k8s related slow network problem

k8s's alow cadvisor caused large network delay

Github's sample, see Linux's network process workflow [https://github.blog/2019-11-21-debugging-network-stalls-on-kubernetes/](https://github.blog/2019-11-21-debugging-network-stalls-on-kubernetes/)

CTrip's samples [https://mp.weixin.qq.com/s/bSNWPnFZ3g_gciOv_qNhIQ](https://mp.weixin.qq.com/s/bSNWPnFZ3g_gciOv_qNhIQ) [https://mp.weixin.qq.com/s/7ZZqWPE1XNf9Mn_wj1HjUw](https://mp.weixin.qq.com/s/7ZZqWPE1XNf9Mn_wj1HjUw)

# DB + ML

AWS Aurora and Machine Learning [https://aws.amazon.com/blogs/aws/new-for-amazon-aurora-use-machine-learning-directly-from-your-databases/](https://aws.amazon.com/blogs/aws/new-for-amazon-aurora-use-machine-learning-directly-from-your-databases/)

# Open Source

虽然基于开源软件做产品可以踩在别人的肩膀上，但不同团队的能力也存在天壤之别。粗略 总结一下，大致可以分为以下几类，欢迎补充。

1. 完全照搬，啥也不做。对系统和代码知之甚少。好处归自己，坏处赖社区。
2. 穿个马甲，换个皮肤。对系统和代码略有了解，有一定概率会改坏系统。
3. 做个全面装修，偶尔修饰一下。知其然而不知其所以然。抄个补丁可能都会搞错。写个 功能也会是残缺不全。仍然属于社区的吸血鬼。
4. 维护自己的版本，甚至 FORK 出来独立发展。有能力（但不一定）给社区实质的反馈。
5. 严格按照社区的规则来玩。参与甚至引导社区的一些发展方向。

# Cloud Spanner

做个全新的数据库免不了要融入生态。Cloud Spanner 也得开发自己的 Hibernate 方言。

[https://cloud.google.com/blog/products/databases/bringing-hibernate-orm-cloud-spanner-database-adoption](https://cloud.google.com/blog/products/databases/bringing-hibernate-orm-cloud-spanner-database-adoption)

# CockroachDB vs. YugabyteDB

创业公司看来喜欢抓住大公司产品（例如 AWS Aurora）来对比。当然，对比必然是有倾向 性的。还好公开的对比总是可以让事实越辩越明朗的，值得称赞和效仿。之前 CockroachDB 对比了一通 Aurora，然后 YugabyteDB 对比了 Aurora 和 CockroachDB。最后， CockroachDB 又来更彻底的比了一把 YugabyteDB。参看：

[https://www.cockroachlabs.com/blog/unpacking-competitive-benchmarks/](https://www.cockroachlabs.com/blog/unpacking-competitive-benchmarks/)

# uuencode & uudecode

Encode a binary file as perf.encode. perf2.svg is the default file name when decoded. Which is the first line of the output (file perf.encode).

Can copy the file to the server by copy&paste, which may be faster than scp in the shell?

Decode the file will generate a file named perf2.svg.

```
$ uuencode perf.svg perf2.svg > perf.encode
begin 664 perf.encode
M/#]X;6P@=F5R<VEO;CTB,2XP(B!S=&%N9&%L;VYE/2)N;R(_/@H\(41/0U19
...

$ uudecode perf.encode 
$ ls perf2.svg
```

## Copy files betweeen ssh session and local macOS through ssh

Copy files from remote to local macOS

```
$ ssh user@server "cat /tmp/file.txt"
```

Copy remote files back to macOS by ssh.

```
$ cat file.txt | ssh desktop pbcopy
```

## Copy files between ssh session and local macOS by clip board

[http://dtrace.org/blogs/ahl/2012/09/14/webex-utilities/](http://dtrace.org/blogs/ahl/2012/09/14/webex-utilities/)

Copy remote file to local macOS

```
$ cat output.txt | gzip -9c | uuencode /dev/stdin
```

Then select the text on the screen, and

```
$ pbpaste | uudecode -o /dev/stdout | gzip -cd
```

Copy local macOS file to remote ssh

```
$ cat output.txt | gzip -9c | uueencode /dev/stdin 
```

Then select the text on the screeen, and how can we get the clip board to remote ssh? Command+V?

```
$ uudecode -o /dev/stdout | gzip -cd
```

# Ubuntu no-password account

## /etc/shadow

U6aMy0wojraho - Found by sb. on the guest account of live CD

## passwd -d

1. Make sure NOPASSWD: is set for ssh config.

```
$ sudo grep NOPASSWD: /etc/sudoers
foo ALL=(ALL) NOPASSWD: ALL
```

1. Delete the password of the account

```
$ sudo passwd -d `whoami`
```

Looks like it works, but later it will ask for passwd for sudo commands. And have to fix it by using \`passwd\` to set a non-empty password.

# bpftrace mysqld

[http://mysqlentomologist.blogspot.com/2019/10/dynamic-tracing-of-mariadb-server-with.html](http://mysqlentomologist.blogspot.com/2019/10/dynamic-tracing-of-mariadb-server-with.html) [https://github.com/iovisor/bpftrace/blob/master/docs/reference_guide.md#4---c-struct-navigation](https://github.com/iovisor/bpftrace/blob/master/docs/reference_guide.md#4---c-struct-navigation)

1. Find the symbol

```
$ grep dispatch_command /usr/share/doc/mysql-server-8.0/*.sym
0000000000d2af46 t _Z16dispatch_commandP3THDPK8COM_DATA19enum_server_command.cold
0000000000f1f5c0 T _Z16dispatch_commandP3THDPK8COM_DATA19enum_server_command         <=== this is the mangled function name
```

1. bpftrace and run a query in mysql client.

```
$ sudo bpftrace -e 'uprobe:/usr/sbin/mysqld:_Z16dispatch_commandP3THDPK8COM_DATA19enum_server_command { printf("%s\n", "haha");}'
Attaching 1 probe...

haha
haha
```

For more examples, see [https://github.com/brendangregg/bpf-perf-tools-book/tree/master/originals/Ch13_Applications](https://github.com/brendangregg/bpf-perf-tools-book/tree/master/originals/Ch13_Applications)

# Perf probe mysqld

1. Prepare

Set up the debug symbol package list and install packages. [https://wiki.ubuntu.com/Debug%20Symbol%20Packages#Getting_-dbgsym.ddeb_packages](https://wiki.ubuntu.com/Debug%20Symbol%20Packages#Getting_-dbgsym.ddeb_packages)

```
$ sudo apt-get install mysql-server-core-8.0-dbgsym
$ ls -l /usr/share/doc/mysql-server-8.0/
```

1. It can't probe C++ funcitons by class-name::method-name, but can use address

[https://stackoverflow.com/questions/20172446/cant-add-perf-probe-for-c-methods](https://stackoverflow.com/questions/20172446/cant-add-perf-probe-for-c-methods)

Find the address of \`bool File\_query\_log::write\_slow(&#x2026;)\` in the symbol file.

```
$ grep write_slow mysqld.sym
00000000012e88b0 T _ZN14File_query_log10write_slowEP3THDyyPKcmyybS3_mP17System_status_var
```

Perf probe it.

```
$ sudo perf probe -x /usr/sbin/mysqld '0x12e88b0'
Added new event:
  probe_mysqld:abs_12e88b0 (on 0x12e88b0 in /usr/sbin/mysqld)

You can now use it in all perf tools, such as:

	perf record -e probe_mysqld:abs_12e88b0 -aR sleep 1
```

Perf record it, and generate several slow queries.

```
$ sudo perf record -e probe_mysqld:abs_12e88b0 -aR
^C[ perf record: Woken up 1 times to write data ]
[ perf record: Captured and wrote 0.890 MB perf.data (2 samples) ]
```

Script out the record.

```
$ sudo perf script 
	  mysqld  9940 [000]  9021.629236: probe_mysqld:abs_12e88b0: (55b8709578b0)
	  mysqld  9940 [000]  9131.469113: probe_mysqld:abs_12e88b0: (55b8709578b0)
```

List and delete the probes.

```
$ sudo perf probe -l
  probe_mysqld:abs_12e88b0 (on write_slow@./sql/log.cc in /usr/sbin/mysqld)

$ sudo perf probe -del probe_mysqld:abs_12e88b0
```

# Brendan Gregg @LISA2019

[https://www.usenix.org/conference/lisa19/presentation/gregg-linux](https://www.usenix.org/conference/lisa19/presentation/gregg-linux)

## commands

```

$ mpstat 10
Linux 5.3.0-23-generic (ubuntu) 	11/19/2019 	_x86_64_	(1 CPU)

02:34:03 PM  CPU    %usr   %nice    %sys %iowait    %irq   %soft  %steal  %guest  %gnice   %idle
02:34:21 PM  all    6.04    0.00    2.62    4.45    0.00    0.24    0.00    0.00    0.00   86.66

git@github.com:brendangregg/pmc-cloud-tools.git
$ pmcarch -p 4093 10

$ perf stat -e cs -p 1928219 -I 1000

$ /usr/share/bcc/tools/cpudist -p 4093 10 1

$ sudo apt-get install sysstat bpfcc-tools bpftrace \
    linux-tools-common linux-tools-$(uname -r) \
    iproute2 msr-tools
$ git clone https://github.com/brendangregg/msr-cloud-tools
$ git clone https://github.com/brendangregg/bpf-perf-tools-book

$ uptime

top, atop
htop - dstat is similar, and now dead (May 2019); see pcp-dstat
vmstat, iostat, free
strace - perf trace will replace it: uses a ring buffer & BPF
tcpdump - Use BPF in-kernel summaries instead.
netstat - use nstat from iproute2
slabtop

https://github.com/tobert/pcstat
pcstat data0* - Show page cache residency by file


docker stats - Soft limits(cgroups) by container 

showboost - Determine current CPU clock rate, see also turbostat
	    https://github.com/brendangregg/msr-cloud-tools
```

## perf

```

Linux Perf Analysis in 60s
1. uptime
2. dmesg -T | tail
3. vmstat 1
4. mpstat -P ALL 1
5. pidstat 1
6. iostat -xz 1
7. free -m
8. sar -n DEV 1
9. sar -n TCP,ETCP 1
10. top

http://techblog.netflix.com/2015/11/linux-performance-analysis-in-60s.html
```

## flame graph

[https://github.com/brendangregg/FlameGraph](https://github.com/brendangregg/FlameGraph)

[http://www.brendangregg.com/FlameGraphs/cpuflamegraphs.html](http://www.brendangregg.com/FlameGraphs/cpuflamegraphs.html) [http://queue.acm.org/detail.cfm?id=2927301](http://queue.acm.org/detail.cfm?id=2927301) The Flame Graph, CACM, June 2016

Linux 2.6+, via perf. Verified on Ubuntu 19.10.

```
$ git clone --depth 1 https://github.com/brendangregg/FlameGraph
$ sudo perf record -F 99 -a –g -- sleep 30
$ sudo perf script --header > out.perf01
$ FlameGraph/stackcollapse-perf.pl < out.perf01 | FlameGraph/flamegraph.pl > perf.svg
```

Linux 4.9+, via BPF. Doesn't work on Ubuntu 19.10 QEMU.

```
$ git clone --depth 1 https://github.com/brendangregg/FlameGraph
$ git clone --depth 1 https://github.com/iovisor/bcc
$ sudo ./bcc/tools/profile.py -dF 99 30 | ./FlameGraph/flamegraph.pl > perf.svg
```

[https://github.com/Netflix/flamescope](https://github.com/Netflix/flamescope)

## trace

```
./funccount -i 1 'bio_*'
./ext4slower 1
bpftrace -e 't:block:block_rq_issue { @[args->rwbs] = count(); }'
```
