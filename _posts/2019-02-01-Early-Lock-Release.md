---
layout: post
title: Early Lock Release
---

# Table of Contents

1.  [Early Lock Release 的原理](#org3357c0c)
2.  [MySQL 中的 ELR](#orgf877d8c)
    1.  [Server 层面的 ELR](#org8e45247)
    2.  [InnoDB 层面的 ELR](#orgd2eddec)
    3.  [ELR 对性能的影响](#org972170a)
3.  [更多有趣的细节](#org7b7e8e4)
    1.  [某个号称曾经实现过 ELR 的数据库](#orgd34997b)
    2.  [InnoDB 层 ELR 代码的引入时间](#orgf356832)
    3.  [XA 事务恢复时为什么是按照事务号从大到小的顺序恢复的？](#orgd74f4aa)
    4.  [Server 层的 ELR 的精简后的例子](#org1e91402)
    5.  [InnoDB 恢复非 prepared 的事务的顺序](#org63cd52e)
    6.  [InnoDB 的性能测试](#orgbfbd3d9)
        1.  [修改 MySQL](#orgb14f2ed)
        2.  [性能测试](#org656e46c)



<a id="org3357c0c"></a>

# Early Lock Release 的原理

数据库领域存在很多优化措施（例如 group commit），它们很早就被提出来了，ELR 也不例外<sup><a id="fnr.1" class="footref" href="#fn.1">1</a></sup>。在多核处理器的时代，ELR 又被人发掘出来评估对系统性能的影响，例如 <sup><a id="fnr.2" class="footref" href="#fn.2">2</a></sup> 和 <sup><a id="fnr.3" class="footref" href="#fn.3">3</a></sup> 评估了包括 ELR 在内的多种性能优化措施。

在数据库中，典型的一个事务 t1 操作流程如下：

    0. begin transaction;
    1. search & lock tuple for update;
    2. update tuple & generate log;
    3. write commit log;
    4. flush logs;
    5. unlock tuples;
    6. commit & notify user;

容易发现，事务在写出并且持久化日志的时候会带来 IO，而 IO 通常而言比较耗时。如果事务中此前的操作都是内存操作的话（在内存数据库或 LSM 结构的系统中更明显），这些 IO 的相对耗时就会显得更大。IO 虽然可以做成异步的，但是在 IO 结束之前锁都仍然会被持有，从而会阻塞其他的并发事务。如果可以把第 5 步释放锁的操作提前，放到第 4 步刷出日志之前，则可以让并发操作同一记录（试图获得锁）的事务 t2 提前开始执行，从而可以增加系统的吞吐量。显然，第 6 步的 commit 操作不能提前，仍然必须等到日志持久化完成之后才能通知用户提交成功，因此用户事务的响应时间不会缩短。

unlock 提前会带来什么副作用吗？此时 t2 可以读取并修改 tuple 的内容，从数据依赖的角度看，t2 依赖了 t1。万一 t1 在提交的过程中（flush log 等操作）失败了，导致事务回滚，t2 也必须回滚（可以称之为：级联回滚）。更进一步，t2 此前读到的 t1 的数据实际上是脏数据（读到了未提交的数据，并且还在其上做了更新）。如果业务逻辑本来就是要读并且更新（例如在电商秒杀业务中，做减库存这样的热点行更新操作），脏读并不会造成实质上的不良后果。如果脏读并写了一条记录到另一个持久化的系统（例如消息队列中），则可能会带来业务上的副作用。

导致提交失败最可能的原因是写日志失败，例如日志盘满或坏了。随着云计算的流行，网络盘的使用非常普遍，写盘失败的概率可以认为是相对增加了。如果把数据库存在可拔插盘上（例如移动盘），拔插移动盘也容易触发写盘失败。如果日志文件设计成只有一个并确保总是按照 LSN 从小到大的顺序写出，在 t1 的日志写盘失败的时候，t2 必定也会失败。只不过在内存中事务已经提交了，对某些系统而言，要做回滚操作可能会很复杂或难以实现。最简单的策略反而是让系统自动 crash，系统重启的时候自然会去走崩溃恢复的逻辑。不过，提交失败的具体原因比较多，可能是盘满了、也可能是盘坏了等，需要针对性的处理，虽然要处理得很完善不那么容易<sup><a id="fnr.4" class="footref" href="#fn.4">4</a></sup>、<sup><a id="fnr.5" class="footref" href="#fn.5">5</a></sup>。


<a id="orgf877d8c"></a>

# MySQL 中的 ELR

MySQL 作为最流行的开源数据库，自然不会放过这样一个经典的优化。下面就让我们来看一看它做了哪些尝试。


<a id="org8e45247"></a>

## Server 层面的 ELR

Facebook 曾经在 MySQL 的 Server 层做过一个 ELR 的实现<sup><a id="fnr.6" class="footref" href="#fn.6">6</a></sup>，来改进高并发场景下的热点行更新的系统性能。不过这个实现在被 port 到 MariaDB 后发现存在致命的缺陷<sup><a id="fnr.7" class="footref" href="#fn.7">7</a></sup>，最后导致代码被回滚掉了。这个改进虽然失败了，但是其中的思考和经验教训<sup><a id="fnr.8" class="footref" href="#fn.8">8</a></sup>对我们理解 MySQL 以及 ELR 还是很有价值的。

众所周知，MySQL 在支持 binlog 的时候，事务提交要走一个内部的分布式事务（XA），执行 prepare()、写 binlog、commit() 三个步骤。这个改进试图把锁的释放提前到 prepare() 阶段，从而提高性能。按照前面一节的描述，实现时需要保证事务 t1、t2 等有依赖关系的事务之间的提交顺序，也需要保证在异常恢复时它们之间的顺序。

在事务 t1 提交失败时，mysqld 可以自杀。但是在异常恢复时，对于 prepared 但是没有 committed 事务，InnoDB 层是按照事务 ID 从大到小的顺序恢复的。而事务 ID 的分配并不等价于事务之间的依赖关系（事务 ID 可以在事务开始或首次进行写操作时分配），因此恢复的时候，事务的先后顺序可能会被搞反，从而导致数据错乱。如果不修改系统来记录事务的依赖关系，并因此修改对应的事务恢复逻辑，这个 Bug 就无法回避。考虑到修改的代价以及数据正确比性能更重要，这最终导致了 patch 被回滚。


<a id="orgd2eddec"></a>

## InnoDB 层面的 ELR

下面以 MySQL 5.7.20 为例，分析一下 InnoDB 中事务提交时的基本调用流程。事务提交的主要函数为 trx0trx.cc 中的 trx\_commit() 函数，它调用了 trx\_commit\_low()，后者又调用了 trx\_commit\_in\_memory()，它们之间是一个线性关系，比较直接。

    trx_commit() -> trx_commit_low() -> trx_commit_in_memory()

而 trx\_commit\_in\_memory() 会先调用 lock\_trx\_release\_locks()，再调用 trx\_flush\_log\_if\_needed()。（注，在 trx->flush\_log\_later 为 true 时，写日志的时机则还要往后一些。) 

    trx_commit_in_memory() -> lock_trx_release_locks(), trx_flush_log_if_need()

事务在内存中提交的时候，按照 WAL 的原则应该是先持久化日志，再释放锁。但是这里却是先释放锁，再刷日志。在 lock0lock.cc, lock\_trx\_release\_locks() 函数中有一段有趣的注释： 

    /* The following assignment makes the transaction committed in memory
    and makes its changes to data visible to other transactions.
    NOTE that there is a small discrepancy from the strict formal
    visibility rules here: a human user of the database can see
    modifications made by another transaction T even before the necessary
    log segment has been flushed to the disk. If the database happens to
    crash before the flush, the user has seen modifications from T which
    will never be a committed transaction. However, any transaction T2
    which sees the modifications of the committing transaction T, and
    which also itself makes modifications to the database, will get an lsn
    larger than the committing transaction T. In the case where the log
    flush fails, and T never gets committed, also T2 will never get
    committed. */
    /*--------------------------------------*/
    trx->state = TRX_STATE_COMMITTED_IN_MEMORY;
    /*--------------------------------------*/

注释说的就是我们在前面提到的脏读问题。那么 InnoDB 是怎么处理日志提交失败（这里只看写出日志失败）的呢？调用序列如下：

    trx_flush_log_if_needed() -> trx_flush_log_if_needed_low() -> log_write_up_to() 
    -> log_write_flush_to_disk_low() -> fil_flush() -> os_file_fsync_posix()

可以看到， fil\_flush() 最后会走到 os\_file\_fsync\_posix()（这里以 Linux 为例）。最后这个函数是 fsync 的包装，而如果 fsync 失败了（例如遇到 EIO 等错误），它会 sleep，然后重试。而由于 fsync 本身实现的问题<sup><a id="fnr.5.100" class="footref" href="#fn.5">5</a></sup>，重试后的 fsync 会成功返回。所以，写日志失败并不会立即 kill 掉 mysqld，因此可能会导致数据错误。


<a id="org972170a"></a>

## ELR 对性能的影响

从原理上看，ELR 可以改进系统的性能。参考文献<sup><a id="fnr.2.100" class="footref" href="#fn.2">2</a></sup>、<sup><a id="fnr.3.100" class="footref" href="#fn.3">3</a></sup>则给出了具体的测试验证。Facebook 也是因为性能才尝试在 Server 层做出改进。如果仅测试 InnoDB，它本身的实现也对性能有改善。可是在 MySQL 中因为对 binlog 的支持引入了内部的 XA 机制，将这些优化的效果给掩盖掉了。


<a id="org7b7e8e4"></a>

# 更多有趣的细节


<a id="orgd34997b"></a>

## 某个号称曾经实现过 ELR 的数据库

<https://ayende.com/blog/165090/early-lock-release-transactions-and-errors> 谈到了 ELR 的缺点，在云计算日益流行的今天，刷日志失败的情况会更加常见，因为块存储是个网络盘。此时，如果要回滚正在提交的 ELR 事务以及其后发出的事务显得特别复杂：正在提交的这个事务内存中已经提交了，要回滚比较困难。而后续的事务也可能已经在内存中提交了。<https://ayende.com/blog/174946/the-guts-n-glory-of-database-internals-early-lock-release> 后来这哥们不知道为什么又说了一次。


<a id="orgf356832"></a>

## InnoDB 层 ELR 代码的引入时间

5.6 分支的以下提交引入了函数 lock\_trx\_release\_locks()。

    commit b01272846a7d00cc0de1bb77670bb8aac95f2840
    Author: Sunny Bains <Sunny.Bains@Oracle.Com>
    Date:   Fri Jun 11 12:12:27 2010 +1000
    
        Move all lock_mutex_enter()/lock_mutex_exit() to lock0*.c except for trx0i_s.c.
        Introduce two new functions to handle the previous use case.

但是，上面那段注释在之前就存在了。在文件 storage/innobase/trx/trx0trx.c 的 trx\_commit() 函数内。对应的提交是：

    commit c307820962d620452e6284c210d5c2947100a55c
    Author: osku <>
    Date:   Thu Oct 27 07:29:40 2005 +0000
    
        Import 5.0 code.


<a id="orgd74f4aa"></a>

## XA 事务恢复时为什么是按照事务号从大到小的顺序恢复的？

因为恢复的时候，trx\_recover\_for\_mysql() 从 rw\_trx\_list 中读取需要恢复的事务，而 rw 事务链表是按照事务号递减的顺序保存的。因此，得到的待恢复事务链表就自然保持了这个顺序。xarecover\_handlerton() 中遍历链表，调用 hton->rollback\_by\_xid() 恢复自然也就是这个顺序了。


<a id="org1e91402"></a>

## Server 层的 ELR 的精简后的例子

<https://launchpadlibrarian.net/73897524/innodb_release_locks_early_recovery.test>


<a id="org63cd52e"></a>

## InnoDB 恢复非 prepared 的事务的顺序

显然不会有事务 ID 的顺序问题，只需要把 redo 做完就行了。

recv\_recovery\_from\_checkpoint\_start(flushed\_lsn);
recv\_sys->apply\_log\_recs 恢复过程中会将其设置为 true，之后读页面进来就会做恢复
recv\_recover\_page 是函数 recv\_recover\_page\_func 的包装

innobase\_start\_or\_create\_for\_mysql -> recv\_recovery\_from\_checkpoint\_start -> recv\_group\_scan\_log\_recs -> recv\_apply\_hashed\_log\_recs 主要路径
                                   -> recv\_apply\_hashed\_log\_recs -> recv\_recover\_page 次要路径，处理最后一批被 recv\_group\_scan\_log\_recs 读出来的记录


<a id="orgbfbd3d9"></a>

## InnoDB 的性能测试


<a id="orgb14f2ed"></a>

### 修改 MySQL

1.  彻底关闭 binlog 以及对应的 XA 比较困难，所以修改：

    ./storage/innobase/handler/ha_innodb.cc:4387:                   trx->flush_log_later = true;

为 false，使得 trx\_commit\_in\_memory() 中释放锁之后立即刷新日志。而不是得到 XA 事务 COMMIT 的时候。

1.  将 trx\_commit\_in\_memory() 中释放锁的代码移动到 flush 日志之后。


<a id="org656e46c"></a>

### 性能测试

采用 1.0 版本的 sysbench。16 个并发，表的记录数 16。

    $ sysbench --db-driver=mysql --mysql-host=127.0.0.1 --mysql-port=3306 --mysql-user=root --mysql-password= --mysql-db=test --threads=16 --time=300 --table_size=16 /usr/local/share/sysbench/oltp_read_write.lua run
    
    with elr（只有第1步的修改）
        transactions:                        179676 (598.88 per sec.)
        queries:                             8324279 (27745.87 per sec.)
        ignored errors:                      306078 (1020.20 per sec.)
        reconnects:                          0      (0.00 per sec.)
    
    without elr（包含第1、2步的修改）
        transactions:                        45610  (151.98 per sec.)
        queries:                             4472795 (14903.77 per sec.)
        ignored errors:                      231084 (769.99 per sec.)
        reconnects:                          0      (0.00 per sec.)


# References

<sup><a id="fn.1" href="#fnr.1">1</a></sup> Implementation Techniques for Main Memory Database Systems. <https://15721.courses.cs.cmu.edu/spring2016/papers/p1-dewitt.pdf>

<sup><a id="fn.2" href="#fnr.2">2</a></sup> Aether: A Scalable Approach to Logging. <http://www.cs.albany.edu/~jhh/courses/readings/johnson.vldb10.logging.pdf>

<sup><a id="fn.3" href="#fnr.3">3</a></sup> Improving OLTP concurrency through Early Lock Release. <https://infoscience.epfl.ch//record/152158/files/ELR-single-column.pdf>

<sup><a id="fn.4" href="#fnr.4">4</a></sup> PostgreSQL's fsync() surprise. <https://lwn.net/Articles/752063/>

<sup><a id="fn.5" href="#fnr.5">5</a></sup> PostgreSQL's handling of fsync() errors is unsafe and risks data loss at least on XFS. <https://www.postgresql.org/message-id/flat/CAMsr%2BYHh%2B5Oq4xziwwoEfhoTZgr07vdGG%2Bhu%3D1adXx59aTeaoQ%40mail.gmail.com#CAMsr+YHh+5Oq4xziwwoEfhoTZgr07vdGG+hu=1adXx59aTeaoQ@mail.gmail.com>

<sup><a id="fn.6" href="#fnr.6">6</a></sup> Implement release of row locks in InnoDB during prepare() phase. <http://worklog.askmonty.org/worklog/Server-Sprint/?tid=163>

<sup><a id="fn.7" href="#fnr.7">7</a></sup> &#x2013;innodb-release-locks-early=1 breaks InnoDB crash recovery. <https://bugs.launchpad.net/maria/+bug/798213>

<sup><a id="fn.8" href="#fnr.8">8</a></sup> Tale of a Bug. <https://kristiannielsen.livejournal.com/15893.html>
