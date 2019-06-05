---
layout: post
title: MySQL thread pool
original: https://blog.csdn.net/zedware/article/details/18862813
---

### 这是 Oracle MySQL 的实现：http://mikaelronstrom.blogspot.com/2011/10/mysql-thread-pool-summary.html
### 这是 MariaDB 的实现：http://worklog.askmonty.org/worklog/Server-BackLog/?tid=246
### 这是 Percona 的增强：http://www.percona.com/doc/percona-server/5.6/performance/threadpool.html
### 这是一个同学写的代码分析，大部分内容都有了：http://www.cnblogs.com/nocode/archive/2013/05/25/3098317.html

### MySQL Thread Pool: Summary
A number of blogs have been written with the intent of describing how
the thread pool manages to solve the requirements of the thread pool.

These blogs are:
[MySQL Thread Pool: Problem Definition](http://mikaelronstrom.blogspot.com/2011/10/mysql-thread-pool-problem-definition.html)
[MySQL Thread Pool: Scalability Solution](http://mikaelronstrom.blogspot.com/2011/10/mysql-thread-pool-scalability-solution.html)
[MySQL Thread Pool: Limiting number of concurrent statement executions](http://mikaelronstrom.blogspot.com/2011/10/mysql-thread-pool-limiting-number-of.html)
[Automated benchmark tool for DBT2, Sysbench and flexAsynch](http://mikaelronstrom.blogspot.com/2011/10/automated-benchmark-tool-for-dbt2.html)
[MySQL Thread Pool: Limiting number of concurrent transactions](http://mikaelronstrom.blogspot.com/2011/10/mysql-thread-pool-limiting-number-of_21.html)
[MySQL Thread Pool: When to use?](http://mikaelronstrom.blogspot.com/2011/10/mysql-thread-pool-when-to-use.html)
[MySQL Thread Pool vs. Connection Pool](http://mikaelronstrom.blogspot.com/2011/10/mysql-thread-pool-vs-mysql-connection.html)
[MySQL Thread Pool: Optimal configuration](http://mikaelronstrom.blogspot.com/2011/10/mysql-thread-pool-optimal-configuration.html)
[MySQL Thread Pool: Benchmarking](http://mikaelronstrom.blogspot.com/2011/10/mysql-thread-pool-benchmarking.html)

There are some interesting discussions in the comments on the [scalability solution
 blog](http://mikaelronstrom.blogspot.com/2011/10/mysql-thread-pool-scalability-solution.html)
and on the blog about [limiting number of concurrent statement executions](http://mikaelronstrom.blogspot.com/2011/10/mysql-thread-pool-limiting-number-of.html)
and finally also on the blog about [when to use](http://mikaelronstrom.blogspot.com/2011/10/mysql-thread-pool-when-to-use.html).

These discussions are around when to use it, what other features might be worth
considering and some remarks on the type of benchmarks that could be used to
evaluate solutions.

The requirements we had on the thread pool solution and the solutions were:
1) Split threads into groups individually handled to avoid making the
solution a problem in itself, aim is to manage one active thread per
group.

Solution:
Connections are put into a thread group at connect time by round robin.
Configurable number of thread groups. This ensures that the thread pool
itself isn't a scalability hog.

2) Wait for execution of a query until the MySQL Server has sufficient
CPU and memory resources to execute it.

Solution:
Each thread group tries to keep the number of executing queries to one or
zero. If a query is already executing in the thread group, put connection
in wait queue.

3) Prioritize queries on connections that have an ongoing transaction.

Solution:
Put waiting connections in high priority queue when a transaction is
already started on the connection.

4) Avoid deadlocks when queries are stalled or execute for a long time.

Solution:
Allow another query to execute when the executing query in the thread
group is declared as stalled (after a configurable time).

