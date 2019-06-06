---
layout: post
title: 此NULL非彼NULL
---

* * *

    先拿SQL Server 2000来做个实验。假设有如下的表foo：

> CREATE TABLE foo (f1 INT, f2 INT);
> 
> INSERT INTO foo VALUES (1, 100);  
> INSERT INTO foo VALUES (2, 200);  
> INSERT INTO foo VALUES (3, NULL);

    要在foo表中选择出所有f2列不为NULL的记录，显然可以用：

> SELECT f1, f2  
> FROM foo  
> WHERE f2 IS NOT NULL;

    结果是：

> f1 f2  
> \-\-\-\-\-\-\-\-\-\-\- \-\-\-\-\-\-\-\-\-\-\-  
> 1 100  
> 2 200
> 
> （所影响的行数为 2 行）

    而不是：

> SELECT f1, f2  
> FROM foo  
> WHERE f2 = NULL;

    它的结果是：

> f1 f2  
> \-\-\-\-\-\-\-\-\-\-\- \-\-\-\-\-\-\-\-\-\-\-
> 
> （所影响的行数为 0 行）

    前者是标准的语法，也表明NULL是特殊值，不能用简单的=或<>判断。如果我们把问题的解决办法复杂化，似乎以下的语句也可以达到同样的目的：

> SELECT f1,  
> (SELECT f2 FROM foo WHERE foo.f1 = fa.f1 and foo.f2 = fa.f2) AS f2  
> FROM foo fa;

    然而它的结果是：

> f1 f2  
> \-\-\-\-\-\-\-\-\-\-\- \-\-\-\-\-\-\-\-\-\-\-  
> 1 100  
> 2 200  
> 3 NULL
> 
> （所影响的行数为 3 行）

    这个查询结果和预期的不一致，是SQL Server做错了，还是SQL语句不对？

    实际情况应该是这样的：

    SQL语言允许位置SELECT的结果集列的位置上出现子查询。不过需要注意的是，该子查询返回的结果集必须为空或一条元组。一般而言，如果结果集满足这个条件，就可以转换为一个标量表达式，它也就能够放在标量表达式能放的地方。如果我们把上面的查询改成：

> SELECT f1,  
> (SELECT f2 FROM foo) AS f2  
> FROM foo fa;

SQL Server将会报错：

> 服务器: 消息 512，级别 16，状态 1，行 1  
> 子查询返回的值多于一个。当子查询跟随在 =、!=、<、<=、>、>= 之后，或子查询用作表达式时，这种情况是不允许的。

    对于上述的查询，假设用X表示查询结果集中生成列f2的那个子查询，则对于表foo fa的每一条元组：

> 对于元组(1, 100)，X的结果集是100，标量化为100。  
> 对于元组(2, 200)，X的结果集是200，标量化为200。  
> 对于元组(3, NULL)，X的结果集是空，标量化为NULL。

    可见上述查询中X的结果集中出现的NULL，是空结果集标量化后的NULL，而不是表foo中f2列的取值NULL。

* * *

[Copyright 2000-2006,zedware\_at\_gmail\_dot\_com](mailto:zedware_at_gmail_dot_com)  
Last modified on Thursday, 2006-05-18
