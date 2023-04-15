---
layout: post
title: 重读A Critique of ANSI SQL Isolation Levels
original: https://blog.csdn.net/zedware/article/details/42399375
tags: SQL ACID
---
                
A Critique of ANSI SQL Isolation Levels

1. 1995年发表。
2. SQL是以现象（phenomena）来定义四个隔离级别的，但是与实际系统差别太大。
3. SQL没有涵盖某些常见的隔离级别，特别是多版本并发控制下的Snapshot Isolation。
4. 关系数据库ACID默认是要支持可串行化的，不过实际系统大多提供了较低的隔离级别。这也为NoSQL、NewSQL只提供较低的隔离级别提供了论据。
5. SQL标准的phenomena包括：Dirty Read、Non-repeatable Read和Phantom。
6. Phenomena与anomalies是有区别的。
7. SQL的隔离级别带有很强的封锁实现的影响，用phenomena来定义隔离级别是为了允许非封锁的实现。
8. Gray以前是用一致性程度来定义不同的隔离级别的，它和SQL的定义是不一样的。
9. 什么是可串行化是有严格定义的。
10. SQL定义的现象可以从严解释或从宽解释（这就是用自然语言定义的缺点，容易导致歧义）。
11. SQL对可串行化的定义很是比较明确的，只不过那个表格容易误导大家。这也是为什么Oracle等只实现了Snapshot Isolation的产品也被大家误认为支持可串行化。
12. 经典的读锁、写锁、两阶段锁以及对应的隔离级别的定义。
13. 隔离级别之间的关系定义：强，弱，等价，不可比。
14. DB2也就是C J Date定义的游标稳定性和可重复读级别。
15. Dirty Write、Lost Update、Constraint violation（Read Skew、Write Skew）。
16. 不同多版本系统的做法，例如：Oracle的Read Consistency。
17. SQL里头的REPEATABLE READ其实名不副实。
xx. 虽然拿这些现象来定义不同的隔离级别看起来不太严谨，但是它们有助于我们识别不同隔离级别、不同实现（不管它们给隔离级别起了什么花里胡哨的名字）的差异。


