---
layout: post
title: 微软的数据访问技术（From MDAC to Windows DAC）
original: https://blog.csdn.net/zedware/article/details/4917233
tags: mdac
---

在过去的很多年中，微软发展了很多种具体的数据访问技术。然而，随着技术的发展和进步，很多技术现在都显得很过时了；有些技术因为被非常广泛的使用，仍然得到了一定程度的维护和支持。10多年前被视为主流的技术，到今天不是被彻底抛弃就是被视为 Legacy 了。这里是微软的：[Data Access Technologies Road Map](http://msdn.microsoft.com/en-us/library/ms810810.aspx)。它的维护历史为：January 2002；Revised December 2008，这也基本说明了最近的这些年中，被称为 MDAC 的这部分技术没有什么实质的发展，而是处于一个维护状态。

微软平台上被广泛使用和广泛了解的数据访问接口包括：ODBC、OLE DB/ADO、.NET Data Provider/ADO.NET。从文档中给出的“Obsolete Data Access Technologies“可以看出，微软已经逐步废弃了访问 SQL Server 数据库的 DB-Library，而代之以集成度较高的 SQL Server Native Client，后者集成了 ODBC 和 OLE DB，并增强了一些新功能；也废弃了 Embedded SQL (E-SQL)，而不是像 Oracle、DB2，甚至 Informix 等产品那样维护着嵌入式 SQL。实际上，SQL Server 的 E-SQL 也没有什么人在使用。其主要的几个竞争对手的情况非常不同，很多旧有系统，特别是金融业务系统都大量的使用了嵌入式 SQL。对于 DAO 和 RDO 这两个老古董，微软更是只能抛弃了。

MDAC 本来一直升级到了版本 2.8，不过在 Windows Vista 中改变了做法，改成了 Widows DAC 6.0。估计这个改动已经让很多维护和发布了一些依赖 MDAC 的应用程序的开发者犯糊涂了。如果从应用程序开发、发布和维护的角度来看，微软减少数据访问接口的数量，并且以面向对象语言中接口的方式提供访问是大有裨益的。
