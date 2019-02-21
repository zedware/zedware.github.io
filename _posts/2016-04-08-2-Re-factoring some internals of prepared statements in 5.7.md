---
layout: post
title: Re-factoring some internals of prepared statements in 5.7
original: https://blog.csdn.net/zedware/article/details/51100701
---
MySQL的prepare stmt做得确实有很多缺陷，导致很多业务都不去用它。典型的问题例如吃内存、效率不高等。偶遇这篇介绍实现改进细节的博客，写得比较详细，有助于理解代码。建议直接访问原文并查看下面的评论。链接：http://mysqlserverteam.com/re-factoring-some-internals-of-prepared-statements-in-5-7/
