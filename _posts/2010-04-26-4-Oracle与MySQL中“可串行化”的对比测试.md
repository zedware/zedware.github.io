---
layout: post
title: Oracle与MySQL中“可串行化”的对比测试
original: https://blog.csdn.net/zedware/article/details/5529389
---
Oracle与MySQL中“可串行化”的对比测试

      Thomas Kyte 在“Oracle 9i&10g编程艺术”一书中为了说明事务的隔离级别，提供了一些例子。这里我们来看看“可串行化”级别下的表现。

以普通用户登录系统，并创建测试用的表。

idle> connect scott/tiger
Connected.

scott@FOO> select * from v$version;

BANNER
--------------------------------------------------------------------------------
Oracle Database 11g Enterprise Edition Release 11.2.0.1.0 - Production
PL/SQL Release 11.2.0.1.0 - Production
CORE    11.2.0.1.0    Production
TNS for Linux: Version 11.2.0.1.0 - Production
NLSRTL Version 11.2.0.1.0 - Production

scott@FOO> create table a(x int);

Table created.

scott@FOO> create table b(x int);

Table created.

修改事务的隔离级别为“可串行化”

时刻 会话1                                                                          会话2
t1     scott@FOO> alter session set isolation_level = serializable;

       Session altered.

t2                                                                                      scott@FOO> alter session set isolation_level = serializable;

                                                                                         Session altered.

t3     scott@FOO> insert into a select count(*) from b;

        1 row created.

t4                                                                                      scott@FOO> insert into b select count(*) from a;

                                                                                         1 row created.

t5     scott@FOO> commit;

        Commit complete.

t6                                                                                      scott@FOO> commit;

                                                                                         Commit complete.

让我们来检查一下结果。

scott@FOO> select * from a;

     X
----------
     0

scott@FOO> select * from b;

     X
----------
     0

      确实如Tom所说，两个表的记录值都是0。这是因为会话1和会话2中的事务看到的都是事务开始时的数据库状态。在Oracle中，可串行化模式下并不能做到我们通常理解的那样：会话1和会话2对应的事务依次串行执行的结果。如果会话1先执行，会话2后执行，则结果应该是表a中的值为0；表b中的值为1。反之，a的值为1；b的值为0。

      让我们看看MySQL的表现。

mysql> select @@version;
+-----------+
| @@version |
+-----------+
| 5.1.45    |
+-----------+
1 row in set (0.01 sec)
 
      MySQL中有多个存储引擎，记得选择事务支持比较好的innodb。
mysql> create table a(x int) engine = 'innodb';
Query OK, 0 rows affected (0.04 sec)

mysql> create table b(x int) engine = 'innodb';
Query OK, 0 rows affected (0.04 sec)
 
      MySQL中默认是一语句已提交的，和Oracle不一样。我们把它修改为非自动提交。
mysql> set autocommit = 0;

t1     mysql> set session transaction isolation level serializable;
        Query OK, 0 rows affected (0.00 sec)
                                                                                        mysql> set autocommit = 0;  
t2                                                                                     mysql> set session transaction isolation level serializable;
                                                                                        Query OK, 0 rows affected (0.00 sec)
t3     mysql> insert into a select count(*) from b;
        Query OK, 1 row affected (0.01 sec)
        Records: 1  Duplicates: 0  Warnings: 0

t4                                                                                     mysql> insert into b select count(*) from a;
                                                                                        因为MySQL采用的是封锁机制，这时会话会停在这里，直到会话1输入
                                                                                        commit并执行后才会出现下面的提示：
                                                                                        Query OK, 1 row affected (7.51 sec)
                                                                                        Records: 1  Duplicates: 0  Warnings: 0
t5     mysql> commit;
        Query OK, 0 rows affected (0.00 sec)

t6                                                                                    mysql> commit;
                                                                                       Query OK, 0 rows affected (0.00 sec)

让我们来查查最后的数据：

mysql> select * from a;
+------+
| x    |
+------+
|    0 |
+------+
1 row in set (0.01 sec)

mysql> select * from b;
+------+
| x    |
+------+
|    1 |
+------+
1 row in set (0.00 sec)

      这相当于会话1中的事务先执行，会话2中的事务后执行。正好符合我们对“可串行化”的理解。

      在说明Oracle有优势的特点时，Tom总忘不了去强调一下它有多好；在说明Oracle不那么有优势，甚至不一定合理的地方时，他会说“这个概念经常被误解”。其实，这些技术细节上的差异都是多版本并发控制和封锁并发控制导致的；凡事有利必有弊，我们需要根据不同需要去取舍。
 
