---
layout: post
title: 说说FoundationDB
original: https://blog.csdn.net/zedware/article/details/44729051
---

#FoundationDB
Apple收购了FoundationDB[1]()，而且后者从开源立即变成了闭源。说实话，在此之前都没有注意到还有这么一个DBMS产品[2]()。从官网上看，它的一些设计理念还是比较切合实际的，毕竟分布式DBMS存在很多难以解决的问题，要在短期内做到Oracle等传统RDBMS那样的程度是不现实的。例如它把重点放到了核心特性上，做了取舍[3](),[4]()：
1. 可扩展性，采用Shared-nothing架构。
2. ACID，支持可串行化的隔离级别。
3. 容错，不仅仅是避免单点故障。
4. 复制，多副本存储。
5. 数据模型，有序KV存储。
6. 变更通知，客户端可以订阅记录的修改。
7. 原子操作，避免了Read-modify-write？
8. OLTP，适合小的读和写。OLTP是有限制的，例如不支持修改太大的事务。
9. OLAP，支持范围扫描大批数据。OLAP不等于大量数据扫描。
10. 核心不支持查询语言，只有API。
11. 并发控制采用MVCC和乐观并发控制。
12. 线程模型，不是一连接一线程。
13. 应用可以是交互式的，不依赖存储过程。
14. 快照形式的备份。不确认这个是否保持一致性。

不过，从这些材料里并不能准确判断它实际的做法[5]()以及完整的限制[6]()。Quora里头找到一个2013年的问答[7]()，说到了做这个系统的一些取舍，可以一看。还有其他的一些材料：
1. [http://2014.nosql-matters.org/cgn/wp-content/uploads/2014/05/Jennifer-Rullmann-NoSQL-and-ACID.pdf](http://2014.nosql-matters.org/cgn/wp-content/uploads/2014/05/Jennifer-Rullmann-NoSQL-and-ACID.pdf)
2. [http://www.methodsandtools.com/archive/acidnosqldatabase.php](http://www.methodsandtools.com/archive/acidnosqldatabase.php) 
As Gilbert and Lynch note, their proof relies on an asynchronous model in which "there is no clock, and nodes must make decisions based only on the messages received and local computation." Mahajan revisit Gilbert and Lynch’s work and strengthen its result
 by employing an asynchronous model allowing local clocks, but no clock globally visible to all nodes. … Real Time Causal (RTC) consistency that is strictly stronger than eventual consistency. Percolator, Megastore, and, most recently, Spanner.

很多人喜欢把NoSQL、NewSQL与传统的RDBMS相比。在很大程度上，这种比较是不太容易的，因为它们针对的需求以及设计的权衡存在着巨大的区别。例如：
1. 处理的数据量。传统的OLTP数据不会太大，集中式系统可以很好的处理。不过，一旦数据分布在不同的IDC，情况就很不一样了。
2. 可靠性要求。集中式系统与分布式系统的处理方式和能力显然不可能一样。Paxos远在RDBMS成熟之后才出现。
3. 数据模型。新的数据库很少能够支持好关系模型的，例如各种约束，以及衍生出来的索引、序列等特性。
4. 查询语言。API还是SQL，甚至XQuery？
5. 数据一致性。集中式系统的ACID显然要胜出一筹。
6. 重要功能。存储过程、视图、触发器、数据仓库，很容易就可以列出一堆新系统很难做到的特性。

To summarize: one size does not fit all。
##Footnotes:
[1]() [https://foundationdb.com/](https://foundationdb.com/)
[2]() [https://en.wikipedia.org/wiki/FoundationDB](https://en.wikipedia.org/wiki/FoundationDB)
[3]() [https://foundationdb.com/key-value-store/features](https://foundationdb.com/key-value-store/features)
[4]() [https://foundationdb.com/key-value-store/white-papers/anti-features](https://foundationdb.com/key-value-store/white-papers/anti-features)
[5]() [https://foundationdb.com/files/Architecture.pdf](https://foundationdb.com/files/Architecture.pdf)
[6]() [https://foundationdb.com/files/FoundationDB-KeyValueStore-Spec-Sheet-3.0.x.pdf](https://foundationdb.com/files/FoundationDB-KeyValueStore-Spec-Sheet-3.0.x.pdf)
[7]() [https://www.quora.com/What-is-FoundationDBs-secret-sauce](https://www.quora.com/What-is-FoundationDBs-secret-sauce)
