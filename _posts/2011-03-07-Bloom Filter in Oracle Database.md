---
layout: post
title: Bloom Filter in Oracle Database
original: https://blog.csdn.net/zedware/article/details/6230135
tags: Oracle DBMS
---

Bloom Filter用的范围很广，不仅在Search Engine中大量采用，在Data Deduplication中也频频遇到。那么它在数据库中的应用情况怎么样呢？放狗找一下，这个比较有意思：[http://antognini.ch/papers/BloomFilters20080620.pdf](http://antognini.ch/papers/BloomFilters20080620.pdf)。
今天在和同事讨论问题的时候突然想起来，这个玩意就是以前在Database Machine论文中见过的Bit vector啊：http://academic.research.microsoft.com/Publication/803587
