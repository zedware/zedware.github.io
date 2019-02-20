---
layout: post
title: Facebook, Google, Twitter, Linkedin推出了WebScaleSQL
original: https://blog.csdn.net/zedware/article/details/22438293
---
名字看起来很可怕的样子：Web Scale。实际是基于Oracle的社区版本MySQL 5.6做的，加了一些工具和补丁。先看看这个新闻：
[http://www.informationweek.com/big-data/software-platforms/facebook-debuts-web-scale-variant-of-mysql-/d/d-id/1141521](http://www.informationweek.com/big-data/software-platforms/facebook-debuts-web-scale-variant-of-mysql-/d/d-id/1141521)
下面这一段是已经开发完成的东西。
The WebScaleSQL community has already developed components, including:
- Features that make operating at Web scale easier, such as a super read-only feature and the ability to specify sub-second client timeouts.

- Changes to improve the performance of WebScaleSQL, including buffer pool flushing improvements, optimizations to certain query types, and support for Non-Uniform Memory
 Architecture (NUMA) interleave policy.

- An automated framework that will run and publish the results of MySQL's built-in test system for each proposed change.

- A suite of stress tests and a prototype automated performance testing system.

下面这一段是正在做的。
WebScaleSQL will welcome other scale-oriented members of the MySQL community to contribute, according to Greene. The group is currently working on:
- Developing an asynchronous MySQL client that won't have to wait to connect, send, or retrieve while querying. This non-blocking client developed and in production
 use at Facebook is being code-reviewed by the other WebScaleSQL teams.

- Adding Facebook production-tested compression and versions of table, user, and compression statistics.

- Adding Facebook's Logical Read-Ahead mechanism for "up to 10x" speed improvements in full table scans, such as nightly logical backups.


目前看来，主要的思路还是形成合力，把大家自己针对业务需求做的东西集中起来。而且明确表态不会是一个官方的fork，只会作为branch存在。这也说明了目前Oracle的版本还是靠谱的，MariaDB还需要努力。至于其中的每一个改进，还是需要慢慢看过来。
网站：[http://webscalesql.org/](http://webscalesql.org/)
GitHub：[https://github.com/webscalesql/webscalesql-5.6](https://github.com/webscalesql/webscalesql-5.6)