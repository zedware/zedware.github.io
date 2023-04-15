---
layout: post
title: 自治事务（Autonomous Transaction）
original: https://blog.csdn.net/zedware/article/details/5521392
tags: SQL
---

    “事务”是一个大家耳熟能详的名词了。事务具有四个基本的特性，我们分别将其称为原子性（Atomic）、一致性（Consistency）、隔离性（Isolation）、持久性（Durability）。统称为ACID。与事务有关的还有“分布式事务”、“超长事务”等正式和非正式的名词。这里要说的是“自治事务”（Autonomous Transaction）。

    自治事务可能是Oracle首先引入的，[在DB2 9.7中也有了类似的概念](http://www.ibm.com/developerworks/data/library/techarticle/dm-0907autonomoustransactions/index.html)。在开发实际应用的过程中，有时我们希望能够将事务执行过程中的错误或被执行到的语句记录下来，以便做日后的问题分析或当做审计记录。可是，在没有自治事务之前，依靠数据库的事务机制很难完成这个任务。因为事务具备原子性，如果事务成功提交了，我们在事务中插入的错误/语句记录语句会按照期望的结果被提交；但是，如果事务回滚了呢？我们的记录也被系统给回滚了。在普通的事务处理中，一个会话一般对应于一个服务器的服务处理者（进程或线程），会话中的工作可以理解为一个接一个的事务。因此，一个会话中在同一时刻只会有一个事务。

    文章“[Autonomous Transactions: a Poor Mis-Understood Feature](http://www.orafaq.com/node/1915)”写的很有意思，也很好懂。其中也引用了一些有趣的文字和链接。
