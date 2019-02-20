---
layout: post
title: Granola: Low-Overhead Distributed Transaction Coordination
original: https://blog.csdn.net/zedware/article/details/20402627
---
论文里的结论总是很美好。
Abstract
Granola: Low-Overhead Distributed Transaction Coordination
This paper presents Granola, a transaction coordinationinfrastructure for building reliable distributed storage ap-plications. Granola provides a strong consistency model,while significantly
 reducing transaction coordination over-head. We introduce specific support for a new type of*independent*distributed transaction, which we can serial-ize with no locking overhead and no aborts due to write conflicts. Granola uses a novel timestamp-based coordina-tion mechanism to order
 distributed transactions, offeringlower latency and higher throughput than previous systems that offer strong consistency.
Our experiments show that Granola has low overhead,is scalable and has high throughput. We implementedthe TPC-C benchmark on Granola, and achieved 3×thethroughput of a platform using a locking approach. 
https://www.usenix.org/system/files/conference/atc12/atc12-final118.pdf



