---
layout: post
title: 大模型（LLM）公司如何使用数据库（Database）系统
original: https://mp.weixin.qq.com/s/VckPl2IurdEiRMtCbctHKA
tags: [Data and AI, Postgres, Cursor, OpenAI]
---

*Leave it to the professionals.*

数据库系统作为重要的软件基础设施，即便时髦如大模型领域的公司，在训练和推理等业务中也都不可避免的要使用它。正如大模型自己会产生幻觉，在大模型如日中天的今日，人类也容易产生幻觉——要么认为数据库系统不再重要，要么认为大模型公司在使用数据库系统方面也很专业。首先，大模型的训练过程需要用处理多模态的各种数据，会用到各种通用或专用的数据库系统，来做数据的采集、加工和查询等（也就是 AI 数据工程）。当然，我们也得注意，这里主要用的是各种分析型数据库系统（OLAP、大数据系统、Python+Ray 等），并非传统的事务型数据库（OLTP）。其次，大模型公司在提供推理服务时也需要用数据库来管理用户注册、账户和会话等，这里用到的则主要是事务型数据库（OLTP）。要了解大模型公司对数据库的使用及其专业水平，需要大模型公司的员工来透露他们是如何使用数据库的。

OpenAI

大约三周前的 2026-01-22 日，OpenAI 的员工在其官网发布了这篇文章《Scaling PostgreSQL to power 800 million ChatGPT users》（https://openai.com/index/scaling-postgresql）。仅从标题看，OpenAI 利用 PostgreSQL 支撑起了全球的 8 亿 ChatGPT 用户，很了不起。一时间，网上相关的讨论火爆异常，很多人都说这是 OpenAI 和 PostgreSQL 非常强大的证明。文中也说"with a single primary Azure PostgreSQL flexible server instance⁠ and nearly 50 read replicas spread over multiple regions globally"，那就是说它用了大约一主五十备实现了横向的扩展。

仔细读完整篇文章，我们就会发现：

1. 与业界尤其是互联网领域数据库与业务快速发展的演进类似，OpenAI 也是随着业务发展在逐步演进其数据库基础设施。

2. PostgreSQL 数据库也出现过一些过载导致的故障，例如上层 Cache 失效导致数据库被打爆，多表连接吃完 CPU，或业务新上的特性引起写风暴。然后就是业界熟悉的恶性循环：过载引起业务重试，重试又加大负载，最终导致 ChatGPT 和 API 服务降级。

3. PostgreSQL 的一主多备适合读多写少的业务负载，写如果太多的话，PostgreSQL 的行级多版本（MVCC）容易导致过度的写放大，读也会因为扫描更多的行被放大。长期运行必然会遇到臭名昭著的 VACUUM 问题。

4. OpenAI 也得重走大家的老路，把写负载逐步从 PostgreSQL 剥离到更容易横向扩展的 NoSQL 数据库（Azure CosmosDB）。PostgreSQL 中甚至不允许再建新表了。

5. 业务的强事务要求依赖了一主多备的 PostgreSQL，要改造成分库分表的 PostgreSQL 或别的方案也没那么容易。不过既然现有方案能用，就继续撑到搞不定为止。

文章后面详细介绍了 OpenAI 是怎么结合业务来演进 PostgreSQL，支撑业务发展直到八百万级别的 QPS。下面这些场景和措施，互联网与云计算的专业人员估计都很熟悉：

1. PostgreSQL 从一主一备拓展到一主多备，完成横向的读写分离，达成横向扩展及减小单点故障的影响范围。

2. PostgreSQL 从一主多备直接复制试图拓展到级联复制，达成主备时延和负载均衡等的综合平衡。

3. 数据库团队与上层业务团队密切配合，做好表结构设计和复杂 SQL 查询的设计等。

4. 数据库团队与上层业务团队密切配合，增加连接池、Cache 缓存，做好 SQL 分流、SQL 限流、监控和切换预案等。

5. 实在没法扩展的写业务要么与上层业务配合迁移到扩展性更强的 NoSQL，要么寻求分库分表的 Sharding 方案。

Cursor

斯坦福大学的 CS153 课程有不少搞云计算基础设施的人比较喜欢关注的主题，例如，大约一年前的 2025 年 3 月，它发布了 Cursor 公司 CTO 的访谈视频《Standford CS 153: Infra@Scale - Cursor CTO & Co-Founder Sualeh Asif》（https://youtu.be/4jDQi9P9UIw?si=Sfb1HVg\_o7MKTKfW）。恰巧其中也提到了 Cursor 公司使用 RDS PostgreSQL 的经验教训。推测他们之前没有什么工业界使用数据库的经验，凭着学校课程学到的知识做出了最初的数据库选型和设计。视频访谈中涉及 PostgreSQL 的部分大致总结如下：

1. Cursor 的主要功能是大模型辅助软件开发，需要动态跟踪维护代码仓中的文件和目录变化，这是通过计算文件或目录的 Hash 值实现的。

2. 他们在数据库中弄了个巨大的表，来保存这些 Hash 值。整个数据库大小为 22TiB，但是这个巨大的表就占了 20TiB。

3. 他们居然还在数据库中定义了外键，那个巨大的表偏偏又需要频繁更新，于是更容易触发 Postgres 中臭名昭著的 VACUUM 问题。

4. 听起来他们也没有评估过 Aurora PostgreSQL 或其他 OLTP 数据库。

5. 搞出严重故障之后出现了个救火英雄，直接基于 S3 新写了个系统把 PostgreSQL 给替换了。

这种特殊的频繁更新负载还是需要认真评估选型，单个数据库实例通常都搞不定。

如果说这些大模型领域的新兴公司的案例对我们有什么启示，那应该是：

1. 专业的事情要交给专业的人来干，除非你有钱有人，可以任性，不怕试错。

2. 对于互联网类的业务负载来说，数据库系统（尤其是 OLTP 系统）不能盲目选型，选型要慎重，一旦沾上就不容易切换。😂

3. 底层基础设施要与上层业务系统紧密配合，做好全栈的技术演进和合作，而不是互相之间泾渭分明，整天想着免责甚至甩锅。

