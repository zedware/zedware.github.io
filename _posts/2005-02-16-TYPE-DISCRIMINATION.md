---
layout: post
title: SQL 数据类型辨析
tags: sql
---

* * *

随着技术的发展，SQL 标准中定义的数据类型越来越多。SQL 1999 将数据类型分为三大类：预定义类型（predefined）、构造类型（constructed）和用户自定义类型（user-defined）。应用中最常用的还是预定义类型。有些预定义数据类型之间仅存在细微的差别，容易使人混淆。这里列出一些常见的问题，供初学者参考。

1.  **CHAR 和 VARCHAR**

CHAR 类型的全称是 CHARACTER，定义时可以指定其精度。如果插入的值的字符数比定义的精度要小，则系统会自动在最后用空格补齐。  
VARCHAR 类型的全称是 CHARACTER VARYING，定义时也可以指定其精度。和 CHAR 类型不同的是，如果插入的值的字符数比定义的精度要小，系统并不会在最后用空格补齐。  
这两种类型的精度都规定了该类型的字段中可以存放的最大字符数。

数据类型

别名

最大精度

最大标度

最小精度

最小标度

缺省精度

缺省标度

CHARACTER

CHAR

IM

N/A

1

N/A

1

N/A

CHARACTER  
VARYING  

VARCHAR

IM

N/A

1

N/A

1

N/A

> **注：**
> 
> 1.  IM 表示 Implementation Defined, N/A 表示 Not Available。下同。
> 2.  常见的 DBMS 中，CHAR 和 VARCHAR 的最大精度一般大于等于 254。

2.  **NUMERIC 和 DECIMAL**

这两种类型都用来表示精确数，都可以有精度和标度。DECIMAL 还可以简写为 DEC，它和 NUMERIC 之间只有一个细微的区别， 即数据库系统内部在处理 DECIMAL 时，采用的精度和标度可以比实际定义的大。有些实际的系统并不区分 NUMERIC 和 DECIMAL，甚至把 DECIMAL 作为 NUMERIC 同义词实现。对于应用程序来说，只采用 NUMERIC 就可以了。

数据类型

别名

最大精度

最大标度

最小精度

最小标度

缺省精度

缺省标度

NUMERIC

N/A

IM

IM

1

0

IM

IM

DECIMAL

DEC

IM

IM

1

0

IM

IM

> **注：**
> 
> 1.  常见的 DBMS 中，NUMERIC 和 DECIMAL 的最大精度一般大于等于 38。

3.  **REAL、FLOAT、DOUBLE PRECISION**

这三种类型都用来表示浮点数（非精确数），它们都可以接收以科学记数法表示的浮点数。浮点数的精度定义了规格化后的科学记数法表示的浮点数中尾数的有效位数，这种精度被称为二进制精度。SQL 标准要求 REAL 的最大精度小于等于 DOUBLE PRECISION 的最大精度。虽然我们可以用C/C++中的 float 和 double类型来类比 REAL 和 DOUBLE PRECISION，但它们的表示范围并不一定相同。FLOAT 类型比较特殊，定义时甚至可以给它指定精度。在实际应用中 FLOAT 应该尽量避免使用。要更清楚地理解浮点数，可以参考 IEEE 754 浮点数标准。

> **注：**

1.  常见的 DBMS 中，REAL 的二进制精度为 24，指数为 -38 到 +38。
2.  常见的 DBMS 中，FLOAT 在给出精度 <= 24 时，相当于 REAL；精度 > 24 时，相当于 DOUBLE PRECISION。不给出精度时通常缺省为 FLOAT(53) 或 DOUBLE PRECISION。
3.  常见的 DBMS 中，DOUBLE PRECISION 的二进制精度为 53，指数为 -308 到 =308

**参考文献：**

1.  ANSI/ISO, ANSI/ISO/IEC International Standard, Database Language SQL, 1999
2.  Peter Gulutzan, Trudy Pelzer，齐舒创作室 译，SQL-3 参考大全，机械工业出版社, 2000
3.  Microsoft, [http://msdn.microsoft.com](http://msdn.microsoft.com)
4.  Oracle, [http://otn.oracle.com](http://otn.oracle.com)
5.  IBM，[http://www.ibm.com](http://www.ibm.com)

* * *

[Copyright 2000-2005,zedware\_at\_gmail\_dot\_com](mailto:zedware_at_gmail_dot_com)  
Last modifiedWednesday, 2005-02-16
