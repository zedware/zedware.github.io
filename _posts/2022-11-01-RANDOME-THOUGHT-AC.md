---
layout: post
title: Random Thoughts AC
---

As the title of the blog suggests, this is a collection of random thoughts. Most
of them are on software design, especially on database systems. When there are
friends or students asking questions, I will write down the interesting ones.

The topics list in reverse chronological order.

# Does One Size Fit All or Not?

不少人希望做一套系统搞定一切，然而从用户的角度看，实际上多系统往往是常态。例如厂商 A 只有一套系统，但避免不了厂商 B 搞出另一套类似功能的系统。尤其是在竞争激烈的领域，比如各种新型的数据库。即便是相对成熟的 OLTP 数据库，也有 Oracle、MySQL、PostgreSQL 等很多种。所以，跨系统的访问或数据流动反而是一个更大的需求。

# Pay by Query

Pay by Query 看起来很美好，都是 Query 很难归一化，即便是看起来很简单的一个 SQL 查询也可能访问一大堆数据或需要大量的计算。如果是 KV 则相对比较简单，PbQ 是比较适合的一种计费模式。如果按照 Query 访问到的数据以及计算资源来计价，则计算模型会比较复杂，而且也存在服务商不思进取，减低优化器执行器改进的动力的可能，反正用户多用多付费。未来合理的模式应该会是服务商在竞争中不断降低自己的成本，持续改进优化器执行器；不过前提是数据库的代价模型要和计费模型关联起来，让用户感觉费用是透明的，钱花得值。

# Azure Cosmos DB for PostgreSQL

微软在 2022 年 Ignite 大会宣布 Azure Cosmos DB for PostgreSQL，乍一看还以为多模 NoSQL 的 Cosmos 原生支持关系模型和标准 SQL 了。其实它是 Azure Database for PostgreSQL Hyperscale (Citus) 改了个名。名字这么长难道暗示了它定位模糊，不知道到底该属于哪个产品种类？CitusDB 是微软收购来的 PostgreSQL 分库分表产品，放到 CosmosDB 品牌之下有点诡异。这不，被 Yugabyte 吐槽了：https://dev.to/yugabyte/is-cosmosdb-a-new-sql-database-is-citusdb-a-distributed-sql-did-hyperscale-vanished-in-the-hyperspace-472d 。

# DuckDB

DuckDB is an in-process SQL OLAP database management system。就像 SQLite 几乎每个现代的手机都会用到一样，DuckDB 可能真会成为分析领域的 SQLite。http://t.cn/A6oaFFR9。2019 年 SIGMOD 有一篇 Demo 论文介绍 DuckDB：an embedded analytical database。随着单机内存的变大，大部分 OLTP 数据库都能在内存中放得下，而很多 OLAP 也有在单机就能搞定的趋势。单台服务器的内存很容易达到 TB，加上 SSD，搞个几十甚至上百 TB 很容易。DuckDB 就是为了填补这个空白而生的。如今，它已经成熟稳定了很多。在它之前其实 CWI 做过好几个比较成功的系统，例如 MonetDB（荷兰人喜欢艺术家莫奈）。它也吸取了 MonetDBLite 等的经验教训。

