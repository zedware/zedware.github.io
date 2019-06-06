---
layout: post
title: Weaving Relations for Cache Performance
original: https://blog.csdn.net/zedware/article/details/7251663
---
虽然这是篇较早的论文，但是对实现数据库存储的开发者，对要深入理解行式存储、列式存储、混合存储方式的技术人员来说还是很有参考价值的。严格的关系数据库理论并不要求应用程序开发者了解表的存储格式，但工程实践中并没有那么完美的好事。不同的存储格式对不同类型的应用或多或少存在一些实现/性能/使用上的影响。某些评论批评Oracle一直没有实现列式存储，所以对数据仓库的支持效率不够好。实际上甲骨文也是不得已，一个庞大的企业级软件哪像一个小软件那样可以随便改来改去啊。
Abstract
Relational database systems have traditionally optimzed for I/O performance and organized records sequentially on disk pages using the N-ary Storage Model (NSM) (a.k.a., slotted pages). Recent research, however, indicates that cache utilization and performance
 is becoming increasingly important on modern platforms. In this paper, we first demonstrate that in-page data placement is the key to high cache performance and that NSM exhibits low cache utilization on modern platforms. Next, we propose a new data organization
 model called PAX (Partition Attributes Across), that significantly improves cache performance by grouping together all values of each attribute within each page. Because PAX only affects layout inside the pages, it incurs no storage penalty and does not affect
 I/O behavior. According to our experimental results, when compared to NSM (a) PAX exhibits superior cache and memory bandwidth utilization, saving at least 75% of NSM’s stall time due to data cache accesses, (b) range selection queries and updates on memory
 resident relations execute 17-25% faster, and (c) TPC-H queries involving I/O execute 11-48% faster。
            