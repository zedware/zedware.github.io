---
layout: post
title: 从FusionIO的NVM压缩接口谈起
original: https://blog.csdn.net/zedware/article/details/22897161
---

厂商的宣传总喜欢搞得很吸引人：Fusion-io宣称可以翻倍MySQL的容量，http://www.zdnet.com/fusion-io-claims-to-double-mysql-capacity-7000027970/。Google一下会出现很多类似的内容。可惜宣传稿只是说可以这样、可以那样，显得很是高大。要想知道得更细一点，还得从基础学起。先看看：https://github.com/opennvm/nvm-primitives/。
原来F公司搞得Atomic Write还提交到了T10了。这个README写得还是比较清楚的，例如：
The NVM Primitives library, as described in the API specification, is a lightweight user space
 library that provides basic operations such as simple atomic writes, vectored atomic writes, and mixed vectors of atomic writes and trims. The library leverages highly-optimized primitives such as sparse addressing, atomic-writes, Persistent TRIM, etc., provided
 by the underlying Flash Translation Layer (FTL). The strong consistency guarantees of these Primitives allow calls within this library to achieve high performance combined with ACID compliance. The majority of the APIs in this library offer simple, powerful
 interfaces to prevent torn/incomplete writes without complex journaling or double-buffering approaches. Additional APIs include the ability to query device capabilities, physical capacity, and capacity used, as well as a few other miscellaneous interfaces.

其中也给出了API文档和示例代码的链接。
T10的这个文档http://www.t10.org/cgi-bin/ac.pl比较有意思，引经据典了一番，特别提到了：
The 2004 Okun paper defined a new atomic write operation that provides
 these semantics:
“A storage device that supports Atomic Write (AW) guarantees that either all the blocks in a writeoperation are written or no blocks are written at all.”The 2011 Ouyang paper implemented an atomic write primitive with a NAND-flash based storage device for aMySQL database (see [http://www.mysql.com](http://www.mysql.com/))
 with the InnoDB transactional storage engine (seehttp://www.innodb.com), measuring:a)  43% reduction in data written to storage;b)  20% reduction in transaction latency; andc)  33% throughout improvement  相信上面的这几个数字很多人以前都看到过。从系统的角度看，Fusion-io做的事情是把文件系统等得功能往下层挪了挪，能够更好更快捷的发挥闪存的优势，而不是依靠各层系统软件的慢慢进步。这个SCSI文档值得阅读。




