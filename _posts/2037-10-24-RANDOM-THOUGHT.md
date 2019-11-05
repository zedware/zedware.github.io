---
layout: post
title: Random Thoughts
---

As the title of the blog suggests, this is a collection of random thoughts. Most of them are on software design, especially on database systems. When there are friends or students ask questions, I will write down the interesting ones.

The topics list in inverse chronological order.

# Total order vs. Partial order

简而言之，全序是集合的所有成员都可以排列成队，例如自然数，任意两个成员之间的关系 是小于关系（严格的全序）或小于等于关系（不严格的全序）。按照数学的描述，具备自反 性、传递性、完备性、非对称性。偏序则放松了要求，某些成员之间允许不能比较大小，所 以形成的图不再是一个线性的队列，而是一个有向无环图。按照数学的描述，不再要求完备 性（也就是集合中所有元素都可比）。显然全序比偏序要严格。

<https://www.quora.com/How-can-you-explain-partial-order-and-total-order-in-simple-terms> <https://en.wikipedia.org/wiki/Total_order> <http://140.177.205.23/TotallyOrderedSet.html>


# MySQL SQL HINT

SQL HINT 是 DBMS 中比较常见的 SQL 优化手段。无论产品做得多么优秀，都不可能面面 俱到，所以需要在 SQL 语句中加一些特殊的注释来告诉系统怎么做。典型的提示如：告诉 系统按照给定的连接顺序、连接算法去执行。容易发现，不同系统的提示语法很可能不一样， 所以将它们放到注释中是一个比较好的主意，可以在注释中玩出一些花样，而又减小对代码 移植的影响。MySQL 8.0 中大大改进了 HINT 功能，对其分类和语法都做了规范化，又朝 着正规军前进了一步。


# Multiple-thread vs. Multiple-process

DBMS 的进程/线程模型基本上是和 OS 的进程/线程模型同步发展的，因此早期的时候两个 领域的名词用得也不太一样。不知道是研究者们有意为之还是相互之间不太交流同步导致的。 回过头来看，一个系统选择什么样的进程/线程模型，很大程度上受当时主流的硬件平台和 OS 能力的影响。也有少数超前设计的系统，在 OS 还没有准备好多线程的情况下自行实现 了自己的多线程（DB 的人喜欢叫它多线索）。如果是今天，设计一个 DBMS，更多人可能会 选择单进程多线程模型，甚至大量引入协程。

开源社区，尤其是 PG 社区中，经常会有类似的讨论，似乎是每隔一段时间就会来一波。例 如：<https://news.ycombinator.com/item?id=13643911>， <https://www.postgresql.org/message-id/3647.130.243.12.138.1098892576.squirrel%40130.243.12.138> 等。有些讨论可能就变成口水战了。从设计和应用的角度去看，值得思考的有以下问题：

1.  OS（以及开发语言）对进程、线程、协程的支持程度？
2.  DB 要面对的并发连接数？
3.  开发团队是否具备相关的开发能力？
4.  最终用户是否可以接受？

例如，Windows 用户就很害怕看到系统中有很多同名的占据了大量虚存的进程，以为中了病 毒，技术支持人员就得花费大量精力去解释此类问题。

人大的数据库课程中有专门的章节来介绍和对比各种 DBMS 进程/线程模型，包括函数库模 式的设计（例如 BDB、RocksDB）、单进程多线程的设计（例如 MySQL）、多进程的设计 （例如 PG）等。也讨论了客户端到服务器的连接是一对一的、多对一的、多对多的等各种 形式。Stonebraker 等的“Architecture of a Database System”（<http://db.cs.berkeley.edu/papers/fntdb07-architecture.pdf>） 的第二章 “Process Models”也有专门的介绍。


# Embarrassingly parallel

不要望文生义，这个 Embarrassingly 在这里类似 Perfect，表示很容易并行化的算法。 Wikipedia（<https://en.wikipedia.org/wiki/Embarrassingly_parallel>) 不仅列出了其他 的说法，也说明了为什么会有这么一个看起来比较奇怪的名字。


# Looking Back at Postgres (Joseph M Hellerstein, 2019)

这是 Michael Stonebraker 获得图灵奖之后的纪念文集（回忆录）中的一篇文章。Joseph 坚持要免费提供下载版本，于是整个图书系列中每一本都有了一章免费的样章。他的博客中 还提到了为什么 Ingres 和 Postgres 的吉祥物是一个乌龟：because ‘it’s slow but it gets there.’（<https://databeta.wordpress.com/2019/01/09/a-history-of-postgres/>）

这篇文章（<https://arxiv.org/pdf/1901.01973.pdf>） 很有意思，把相关的项目、产品、人 物都联系起来了。DB 领域的人应该都能在里头找到熟悉的人或事情。有意思的细节也有不少，例如：

1.  法国画家 Ingres （安格尔）的作品相信很多人见过。
2.  爱默生的名句"A foolish consistency is the hobgoblin of little minds” 是不是让 我们对 Consistency 的认识更上层楼？


# The Simplest KV Store

怎么用十行代码实现一个 KV Store？

```
$ more kv.sh
#!/usr/bin/env bash
# DEMO ONLY

set() {
  echo "Set K=$1 V=$2"
  ln -s $2 $1
}

del() {
  echo  "Del K=$1"
  unlink $1
}

get() {
  echo "Get K=$1"
  ls -l $1 | awk '{print $11}'
}

getAll() {
  echo "Get all"
  ls -l | grep '^l' | awk '{print $9, $10, $11}'
}
```

```
$ source kv.sh 

$ set BJ Beijing
Set K=BJ V=Beijing

$ get BJ
Get K=BJ
Beijing

$ set SH Shanghai
Set K=SH V=Shanghai

$ getAll
Get all
BJ -> Beijing
SH -> Shanghai

$ del BJ
Del K=BJ
```


# Linux System Performance

经常会有人问到如何分析 Linux 下的性能问题，其实绝大部分都可以通过学习 Brendan Gregg 的网站解决。所以这才是最应该先去参考的材料。例如他在 LISA2019 的 Slides： <https://www.slideshare.net/brendangregg/lisa2019-linux-systems-performance>


# DFS vs. DDB

Ceph 说在本地 fs 基础上做分布式 fs 是常见的选择，可是实践上却很受伤。因为：

1.  本地 fs 的事务支持是在太弱，增强它又有很大的额外开销。
2.  本地 fs 臭名昭著的元数据访问性能会拖累整个系统。
3.  本地 fs 对新存储介质的支持进展实在太过于缓慢。

数据库系统，包括分布式数据库系统的设计，早就有过类似的教训。例如：

1.  本地 fs 对 db 的存储/事务子系统不那么友好，除了 PG，很少有系统依赖它。
2.  本地 db 的【分布式】事务能力很弱或开销较大，会拖累整个系统。
3.  本地 db 对新介质的支持也是过于缓慢。

从分层设计和复用的角度看，基于本地 fs/db 设计分布式 fs/db 是一个合理的选择， 这也可以满足快速搭建一个能用可用的系统的需求。但是考虑到整体性能、系统适应性等 因素，把本地 fs 当做黑盒子设计分布式 fs 不总是一个好的设计。同样的，把本地 db 当做一个黑盒子设计分布式 db 也不见得是一个好的设计。如果本地 fs/db 又先天不足 或无法掌握其代码，那就更不是一个好的选择了。

如果说很多分布式 fs 针对的场景跟本地 fs 还有比较大的差异的话，分布式 db 针对的场景 跟本地 db 的差异就没那么大了。例如：某些分布式 fs 是针对大文件或 Append Only 文 件设计的，分布式 db 却不可能只针对大事务或只读事务去做设计。分布式 db 首先要解决 的是分布式事务以及其他的 ACID 问题，需要解决的问题是本地 db 要解决的问题的超集。 分布式 fs 却可以只解决本地 fs 问题的子集。


# PG vs. MySQL

PG 和 MySQL 社区经常会有口水战，理性一点的做法是互相促进、互相学习。大部分人作为 用户，从互为备胎的角度考虑一下，岂不是更好？当然也有人是做开发的，不考虑二者版权 协议的差异，把它们集成到一起，例如把 PG 的存储引擎换成 InnoDB，发挥各自的所长。 甲骨文等公司或社区应该都不会干这种勾当，但是不同产品的互相促进和学习是实实在在发 生的事情。


# MySQL Hash Join

MySQL 8.0 终于实现了 HashJoin，这说明了几个问题：

1.  之前的版本是多么的弱，所以以它为学习模版是有缺陷的，以它为功能参考是容易建立优势的。
2.  它的生态环境的完备可能弥补了功能上的先天不足。
3.  最初设计实现的是一群非专业人士，Sun 的时代没搞好，Oracle 的时代又拖延了时间，现在终于步入正轨了。
