---
layout: post
title: An Introduction to SQL Standard (UPDATED)
tags: SQL
---

This is a brief introduction to SQL standard written in simplified Chinese. It
describe the origin of SQL, the process of the standardization, and the history
of the SQL standard. It also mentions on how to learn the SQL standard, then
comments on the comformance tests of the SQL standard.

Updated on 2022-09-04, please read [A Critique of SQL, 40 Years Later 08.11.2022](https://carlineng.com/?postid=sql-critique#blog).
Updated on 2023-04-21, add SQL/GQL. See also [SQL:2023 is finished: Here is what's new](http://peter.eisentraut.org/blog/2023/04/04/sql-2023-is-finished-here-is-whats-new).

# Table of Contents

1.  [VERSION](#orgd7f44c1)
2.  [SQL 的起源](#orga38e3f1)
3.  [SQL 的标准化](#orga509172)
4.  [SQL 标准的历史](#org0a4d66c)
5.  [学习 SQL 标准](#org8f1f6c4)
6.  [SQL 标准的符合性](#orgf4e50e4)

<a id="orgd7f44c1"></a>

# VERSION

1.  [The latest version(this one)](/SQL-STANDARD-LATEST)
2.  [The 2019 version](/SQL-Standard)
3.  [The 2014 version](/SQL标准简介)


<a id="orga38e3f1"></a>

# SQL 的起源

通常认为，SQL 是 Structured Query Language 的缩写，规范的发音是 “ˌɛsˌkjuːˈɛl”。
但是它的前身是著名的关系数据库原型系统 System R 所采用的 SEQUEL 语言，这也是为什
么有很多人将其读作 “ˈsiːkwəl” 的来源。也有人类比 GNU 这个词的定义（GNU's Not
UNIX），认为 SQL 是 SQL Query Language 的缩写。 作为一种访问关系型数据库的标准语
言，SQL 自问世以来得到了广泛的应用，不仅著名的大型商用数据库产品如 Oracle、DB2、
Sybase、SQL Server 等支持它，很多开源的数据库产品如 PostgreSQL、MySQL 等也支持它，
甚至一些小型的产品如 Access 也支持 SQL。近些年蓬勃发展的 NoSQL 系统最初是宣称不
再需要 SQL 的，后来也不得不修正为 Not Only SQL，来拥抱 SQL。

蓝色巨人 IBM 对关系数据库以及 SQL 语言的形成和规范化产生了重大的影响，第一个版本
的 SQL 标准 SQL86 就是基于 System R 的手册而来的。可惜的是， IBM 起先并没有什么
产品化的想法，倒是 Oracle 富有远见，在 1979 年率先推出了支持 SQL 的商用产品。随
着数据库技术和应用的发展，为各种不同的关系数据库系统提供一致的语言成了一种现实需
要。


<a id="orga509172"></a>

# SQL 的标准化

对 SQL 标准影响最大的机构自然是那些著名的数据库产商，而具体的制订者则是一些非营
利机构，例如国际标准化组织 ISO、美国国家标准委员会 ANSI 等。世界各国通常会参照
ISO 标准和 ANSI 标准（这两个机构的很多标准是差不多等同的）制定自己的国家标准。中
国也是 ISO 标准委员会的成员国，积极参与标准的制定工作，也会经常翻译一些国际标准
对应的中文版。标准为了避免采用具体产品的术语，往往会抽象出很多名词，从而增加了阅
读和理解的难度，翻译成中文之后更容易增加理解难度。对于数据库系统实现者而言，很多
时候还不如直接读英文版本为好。虽然正式的 ISO 标准不总是像 RFC 那样可以从网络上免
费获得，标准草案还是比较容易找到的。待批准的标准草案和最终的标准也没有什么实质上
的区别，能够满足日常工作的需要。ISO 网站现在也提供了一些标准和技术报告的免费下
载<sup><a id="fnr.1" class="footref" href="#fn.1">1</a></sup>。


<a id="org0a4d66c"></a>

# SQL 标准的历史

下面是 SQL 标准简要的发展与演化历史：

-   1986 年，ANSI X3.135-1986，ISO/IEC 9075:1987，SQL-86。这是 ANSI 首次将 SQL 语言标准化的版本。
-   1989 年，ANSI X3.135-1989，ISO/IEC 9075:1989，SQL-89。增加了完整性约束。
-   1992 年，ANSI X3.135-1992，ISO/IEC 9075:1992，SQL-92（SQL2）。最重要的一个版本。 引入了标准的分级概念。
-   1999 年，ISO/IEC 9075:1999，SQL:1999（SQL3）。变动最大的一个版本。改变了标准符合程度的定义；增加了面向对象特性、正则表达式、存储过程、Java 等支持。
-   2003 年，ISO/IEC 9075:2003，SQL:2003。引入了 XML、Window 函数等。
-   2008 年，ISO/IEC 9075:2008，SQL:2008。引入了 TRUNCATE 等。
-   2011 年，ISO/IEC 9075:2011，SQL:2011。引入了时序数据等。
-   2016 年，ISO/IEC 9075:2016，SQL:2016。引入了 JSON、RPR、PTF 等。并于 2019 年引入了 MDA。
-   2023 年，ISO/IEC 9075:2023，SQL:2023。引入了图查询 PGQ，并对 JSON 以及其他 SQL 功能做了修订。

其中，比较新的几个缩写为：

-   RPR = Row Pattern Recognition
-   PTF = Polymorphic Table Functions
-   MDA = Multi-dimensional Arrays
-   PGQ = Property Graph Queries  

细心的读者能发现，从 SQL:1999 开始，标准简称中的短横线（-）被换成了冒号（:），而
且标准制定的年份也改用四位数字了。前一个修改的原因是 ISO 标准习惯上采用冒号，
ANSI 标准则一直采用短横线。后一个修改的原因是标准的命名也遇到了 2000 年问题。

SQL86 大概只有几十页，SQL92 正文大约有 500 页，而 SQL99 则超过了 1000 页。可以看
出，从 SQL99 开始，SQL 标准的个头就非常庞大了，内容包罗万象，可以说已经没有人能
够掌握标准的所有内容了。以 SQL:2003 为例，它包括以下 9 个部分（中间编号空缺是曾
经被占用，之后被废弃的标准造成的）：

-   ISO/IEC 9075-1: Framework (SQL/Framework)
-   ISO/IEC 9075-2: Foundation (SQL/Foundation)
-   ISO/IEC 9075-3: Call Level Interface (SQL/CLI)
-   ISO/IEC 9075-4: Persistent Stored Modules (SQL/PSM)
-   ISO/IEC 9075-9: Management of External Data (SQL/MED)
-   ISO/IEC 9075-10: Object Language Bindings (SQL/OLB)
-   ISO/IEC 9075-11: Information and Definition Schemas (SQL/Schemata)
-   ISO/IEC 9075-13: SQL Routines and Types Using the Java Programming Language (SQL/JRT)
-   ISO/IEC 9075-14: XML-Related Specifications (SQL/XML)

2019 年增加了（还算 SQL:2016）SQL/MDA，总共变成了 10 个部分。

-   ISO/IEC 9075-15: Multi-dimensional arrays (SQL/MDA)

2023 年增加了 SQL/PGQ，总共变成了 11 个部分（暂时还没成为正式标准）。

-   ISO/IEC 9075-16: Property Graph Queries (SQL/PGQ)

其实，标准演进的步伐一直没有停止。2023 年之前就有很多关于流数据的处理、图数据的处理<sup><a id="fnr.2" class="footref" href="#fn.2">2</a></sup><sup>, </sup><sup><a id="fnr.3" class="footref" href="#fn.3">3</a></sup>。可以预期，不久的将来，SQL 标准的总数还会继续增加。

图数据库产品还在快速发展过程中，学术界和工业界合作发起了 LDBC Benchmark。关于它的进展
建议参看 LDBC 作者们的演讲视频和论文<sup><a id="fnr.4" class="footref" href="#fn.4">4</a></sup>。

<a id="org8f1f6c4"></a>

# 学习 SQL 标准

负责具体制定工作的是 ISO 和 IEC 联合成立的一个技术委员会 JTC1/SC32。正因为关系数
据库市场非常成熟，竞争不够激烈，标准本身变得过于庞大等原因，SQL3 制定的周期很长，
制定期间也存在很多争议。例如文章 Is SQL a Real Standard Anymore? 就对 SQL 标准化
进程提出了置疑。WISCorp <sup><a id="fnr.5" class="footref" href="#fn.5">5</a></sup> 网站曾经收集了很多相关的文档，JCC <sup><a id="fnr.6" class="footref" href="#fn.6">6</a></sup>
网站则提供了比较新的更新。

如果要了解标准的内容，比较推荐的方法是泛读 SQL92（因为它涉及了 SQL 最基础和最核
心的一些内容），然后增量式的阅读其他标准。标准在每次更新的时候，委员会的成员们都
为大家提供比较好的介绍文档。例如，针对 SQL:2011，SIGMOD Record 上就有很不错的介
绍<sup><a id="fnr.7" class="footref" href="#fn.7">7</a></sup>。针对最新的内容，例如 SQL:2016，这里有一个更新<sup><a id="fnr.8" class="footref" href="#fn.8">8</a></sup>，
这<sup><a id="fnr.9" class="footref" href="#fn.9">9</a></sup>也是一个很好的补充。如果有兴趣，也可以参考这本书“SQL-99 Complete，
really”<sup><a id="fnr.10" class="footref" href="#fn.10">10</a></sup>。

大约从 2011 年开始，标准委员会还提供了 SQL Technical Reports，虽然它们不是正式标
准的一部分，但却非常有助于理解标准。ISO 的这个网页<sup><a id="fnr.1" class="footref" href="#fn.1">1</a></sup>提供了技术报告的免费下
载，注意它们的编号是 19075，而且标题中带 TR 这个缩写词。技术报告已经累计发布了 9
个：

-   ISO/IEC TR 19075-1: XQuery Regular Expression Support in SQL
-   ISO/IEC TR 19075-2: SQL Support for Time- Related Information
-   ISO/IEC TR 19075-3: SQL Embedded in Programs using the Java programming language
-   ISO/IEC TR 19075-4: SQL with Routines and types using the Java programming language
-   ISO/IEC TR 19075-5: Row Pattern Recognition in SQL
-   ISO/IEC TR 19075-6: SQL support for JSON
-   ISO/IEC TR 19075-7: SQL Support for Polymorphic Table Functions
-   ISO/IEC TR 19075-8: SQL Support for multi dimensional arrays
-   ISO/IEC TR 19075-9: Online analytic processing (OLAP) capabilities
-   ISO/IEC TR 19075-10: SQL model (Under development)

坚持跟踪 SQL 标准以及它在不同产品中的实现情况的网站很少，Modern SQL <sup><a id="fnr.11" class="footref" href="#fn.11">11</a></sup>
是其中优秀的代表。


<a id="orgf4e50e4"></a>

# SQL 标准的符合性

提及标准，大家容易想到的一个问题就是产品对标准的遵循程度，也就是标准的符合性到底
如何，因为它会影响代码在不同系统之间迁移的难度。很遗憾的是，SQL 标准因为定义过于
宽泛等技术和非技术原因，不同产品对标准的符合程度存在很大的差异。大到功能特性，小
到语法语义的细节，在不同产品之间都存在很多差异，造成实际的应用迁移远比 C/C++ 程
序的迁移要复杂很多。因此，尽管很多产品都号称自己符合 SQL 标准，并不意味着应用可
以容易的在它们之间切换。除了 Oracle、DB2 等经典的商业产品，以及 PostgreSQL、
MySQL 等开源产品总体上对 SQL 标准的符合程度较高以外，很多产品提到的 SQL 标准，涉
及的内容其实是 SQL92 里头最基本或最核心的一部分（属于入门级的范畴。SQL92 本身是
分级的，包括入门级、过渡级、中间级和完全级）。

为了验证具体的产品对标准的遵从程度，NIST 还曾经专门发起了一个项目，来做标准符合
程度的测试集合<sup><a id="fnr.12" class="footref" href="#fn.12">12</a></sup>。不过，SQL 标准包含的内容实在太多了，而且有很多特性对新
的 SQL 产品而言也越来越不重要了。从 SQL99 之后，标准中符合程度的定义就不再分级，
而是改成了核心兼容性和特性兼容性；也没有机构来推出权威的 SQL 标准符合程度的测试
认证了。开源的 MySQL 曾经在 6.0 的开发过程中试图大幅度改善对 SQL 标准的符合程度，
不过后来似乎不了了之了。国产数据库作为数据库领域的后来者，曾经对标准的符合程度非
常重视，还组织了专门的机构来做产品的标准符合性测试。在数据库百科全书<sup><a id="fnr.13" class="footref" href="#fn.13">13</a></sup>中
也有很多有关标准的章节。


# Footnotes

<sup><a id="fn.1" href="#fnr.1">1</a></sup> <http://standards.iso.org/ittf/PubliclyAvailableStandards/index.html>

<sup><a id="fn.2" href="#fnr.2">2</a></sup> <https://s3.amazonaws.com/artifacts.opencypher.org/website/ocim5/slides/ocim5+-+SQL+and+GQL+Status+2019-03-06.pdf>

<sup><a id="fn.3" href="#fnr.3">3</a></sup> <https://drive.google.com/file/d/1nQXzl7u5eU0iu8s1Q7ZdBSLuPGBxjZWF/view>

<sup><a id="fn.4" href="#fnr.4">4</a></sup> <https://fosdem.org/2023/schedule/event/graph_ldbc/>

<sup><a id="fn.5" href="#fnr.5">5</a></sup> <https://www.wiscorp.com/free_downloads.html>

<sup><a id="fn.6" href="#fnr.6">6</a></sup> <http://www.jcc.com/resources/sql-standards>

<sup><a id="fn.7" href="#fnr.7">7</a></sup> <http://www.sigmod.org/publications/sigmod-record/1203/pdfs/10.industry.zemke.pdf>

<sup><a id="fn.8" href="#fnr.8">8</a></sup> <https://s3.amazonaws.com/artifacts.opencypher.org/website/materials/SQLStandards_2017-10-20.pdf>

<sup><a id="fn.9" href="#fnr.9">9</a></sup> <https://modern-sql.com/blog/2017-06/whats-new-in-sql-2016>

<sup><a id="fn.10" href="#fnr.10">10</a></sup> <https://crate.io/docs/sql-99/en/latest/>

<sup><a id="fn.11" href="#fnr.11">11</a></sup> <https://modern-sql.com/standard>

<sup><a id="fn.12" href="#fnr.12">12</a></sup> <http://itl.nist.gov/div897/ctg/sql_form.htm>

<sup><a id="fn.13" href="#fnr.13">13</a></sup> <http://mall.cnki.net/reference/detail_R201109078.html>

