---
layout: post
title: 可更新视图
original: https://blog.csdn.net/zedware/article/details/20488069
tags: sql view
---

视图是从一个或多个基表（或视图）导出的表。通常视图仅仅是一个虚表，即数据库中只存放视图的定义信息等元数据，而不存放视图对应的数据。 视图上的操作和基表类似，但是 DBMS对视图的更新操作（INSERT、DELETE、UPDATE）往往存在一定的限制。 DBMS对视图进行的权限管理和基表也有所不同。

视图可以提高数据的逻辑独立性，也可以增加一定的安全性。 DBMS在处理视图时和基表存在很多不同的地方，例如：
- 定义：基于基表或视图
- 数据：一般不单独存放
- 查询：允许，和基表类似
- 插入：有限制
- 删除：有限制
- 更新：有限制
- 权限：有所不同

本文主要讨论视图的可更新性，即考虑视图上的更新类操作存在哪些特点和限制。

# SQL92定义的可更新视图

若一个视图是从单个的基表导出的，并且只是去掉了基表的某些行和列，且保留了基表的主码，这样的视图被称为行列子集视图。SQL92中对可更新视图的支持非常有限，主要就是上述的行列子集视图。 SQL92中对可更新视图的要求如下：
1. 视图定义必须是一个简单的SELECT语句，不能带连接、集合操作。即SELECT语句的FROM子句中不能出现多个表，也不能有 JOIN、EXCEPT、UNION、INTERSECT
2. 视图定义的 SELECT子句中不能有DISTINCT
3. SELECT子句中的各列必须来自于基表（视图）的列，不能是表达式
4. 视图所基于的基表（或视图）必须是可更新的
5. 视图定义的 SELECT子句的子查询的FROM子句不能有视图所基于的基表（或视图）
6. 视图定义的 SELECT语句中不能含有GROUP BY子句
7. 视图定义的 SELECT语句中不能含有HAVING子句

根据SQL92的定义，一个视图要么是可更新的，要么是不可更新的，二者必居其一。SQL92 也不允许视图中某些列可以更新，某些列不可以更新。

# SQL99(SQL3)定义的可更新视图

SQL99放宽了对可更新视图的限制，甚至还区分了视图是否可插入、列是否可以为 NULL、连接查询生成的视图可更新。

# 主流DBMS对可更新视图的支持

## MS SQL Server 2000

SQL Server 2000 除了支持SQL标准规定的可更新视图，还提供了两种扩充：
1. INSTEAD OF 触发器 
   在视图上创建这种类型的触发器，可以间接地实现对基表的更新。
2. 某些分区视图是可更新的

对于不具备上述两种特性的视图， SQL Server 遵照SQL标准的规定做了如下限制：
1. SELECT语句的结果列中没有聚集函数，SELECT子句中没有TOP, GROUPBY, UNION，DISTINCT 子句
这里没有提到 INTERSECT, EXCEPT，是因为SQL Server不支持。
2. SELECT语句的结果列中没有导出列。即只能有列名。
3. SELECT语句的FROM字句至少有一个表（或视图）。 

  因为 SQL Server支持不带FROM子句的SELECT语句。例如：

```
CREATE VIEW NoTable AS
  SELECT GETDATE() AS CurrentDate, 
    @@LANGUAGE AS CurrentLanguage, 
    CURRENT_USER AS CurrentUser;

SELECT CURRENT_USER;
```

4. UPDATE和INSERT，如果修改的数据仅涉及视图所基于的一个基表，则可更新。DELETE则只能用于基于单个基表的视图。
5. 分区视图其实是用 UNION ALL联合两个或多个SELECT语句定义的视图。 

### SELECT

基表的所有列必须都在 SELECT的结果列中。
各个 SELECT语句的结果列对应的列是同一类型和COLLATION。
结果列中至少有一列上定义了简单的 CHECK约束，保证该列取某个具体值的时候，要么不满足所有表的CHECK约束，要么满足其中一个表上的CHECK约束。即这些CHECK定义的值域是互不重叠的。
这样的列被成为分区列。表上的约束必须是打开的。
表中的一个列最多在结果列中出现一次。

### PARTITIONED COLUMN

分区列是主码的一部分。
分区列不能是computed column。
该列上只能有1个约束。
MEMBER TABLES
表可以是本数据库中的，也可以是其他数据库中的，也可以是通过 OPENDATASOURCE 或 OPENROWSET-based引用的。
同一个表只能在 CREATE VIEW语句中出现一次。
不能在compute column上建有索引。
表的主码必须类似（建在同样的列上）。
所有表的 ANSI padding设置一致。

### 其他关于分布式分区表的限制


## ORACLE 8i/9i

ORACLE允许用“WITH READ ONLY”子句显式地指定视图是只读的。若视图不带上述子句，则ORACLE遵照SQL标准提出了以下限制：
1. 不能有集合操作（UNION,UNION ALL,INTERSECT,MINUS)
2. 不能有DISTINCT
3. 不能有聚集函数（AVG,COUNT,MAX,MIN等）和分析函数
4. 不能有GROUP BY,ORDER BY,CONNECT BY,START WITH
5. SELECT列表中不能出现collection expression
6. SELECT列表中不能有子查询
7. 一般不能是JOIN查询（参见i)
8. SELECT列表中若出现系统的伪列或表达式，则更新语句不能修改这些列
9. 可更新的连接视图要满足一些额外的条件

简单的说，视图定义中用到的基本表必须是“键值保持表”。（A base table of a view is considered a *key-preserved *table if every primary-key or unique-key value in the base table is also unique in the result set of the join view—in other words, if the entity integrity of the base
 table is preserved by the join view. ）

我们还可以用INSTEAD OF 触发器来实现视图的更新。详细的信息还请参考文献[7]。

## DB2V8

DB2将视图区分为可删除视图、可更新视图、可插入视图、只读视图、不可用视图等。这样的划分应该更为合理，也比较好理解。DB2的可更新视图：
1. 允许更新某些UNION ALL视图
2. 不允许更新象ORACLE支持的连接查询视图

也可以用INSTEAD OF 触发器来实现视图的更新操作。详细的信息还请参考文献[8]。

主要的几个 DBMS 都实现了 SQL92 定义的可更新视图，SQL99 定义的可更新视图也或多或少的得到了支持。但是需要注意的是，产品之间对可更新视图的实现程度是有差异的，而且由于它们支持的SQL语法存在一些差异，要书写可移植的 SQL 语句需要特别注意。

# 可更新视图的进一步探讨

E.F.Codd提出了评价全关系系统的12条准则。其中的准则6就是讨论视图的更新的。准则6的内容如下：
准则6：视图更新准则。所有理论上可以更新的视图也应该允许由系统更新。 

理论上可以更新的视图指的是对视图的更新要求，存在一个与时间无关的算法，可以无二义性地把更新要求转换为对基表的更新序列。视图更新准则对于系统对数据的逻辑独立性支持是非常重要的。
　　
C. J. Date在文献[2]和他的很多技术文章中对SQL标准定义的可更新视图提出了批评，并提出了自己的一系列观点。他的基本观点是所有的视图本质都是可以更新的，只是某些时候要受制于完整性约束。这是一种比较美妙的境界，实际的 DBMS产品当然还不可能做到这样完美。

# 参考文献

1. 萨师煊、王珊，数据库系统概论（第三版），高等教育出版社，2000
2. C. J. Date，An Introduction to Database Systems (Seventh Edition)，机械工业出版社，2002
3. SQL92, ANSI/ISO/IEC 9075:1992
4. SQL99, ANSI/ISO/IEC 9075-2:1999
5. Peter Gulutzan, Trudy Pelzer, 齐舒创作室译, SQL-3 参考大全, 机械工业出版社，2001
6. Microsoft, [http://msdn.microsoft.com](http://msdn.microsoft.com/), SQL Server 2000 Online Book
7. Oracle, [http://otn.oracle.com](http://otn.oracle.com/), Oracle 9i SQL Reference
8. IBM, [http://www.ibm.com](http://www.ibm.com/), IBM DB2 Universal Database SQL Reference Volume 2
9. [http://www.oracle.com/oramag/oracle/01-mar/index.html?o21o8i.html](http://www-900.ibm.com/developerWorks/cn/dmdd/library/techarticles/0210rielau/0210rielau.shtml)
10. [http://www-900.ibm.com/developerWorks/cn/dmdd/library/techarticles/0210rielau/0210rielau.shtml](http://www-900.ibm.com/developerWorks/cn/dmdd/library/techarticles/0210rielau/0210rielau.shtml)
