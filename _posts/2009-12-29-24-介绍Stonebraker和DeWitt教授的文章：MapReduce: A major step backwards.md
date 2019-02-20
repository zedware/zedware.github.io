---
layout: post
title: 介绍Stonebraker和DeWitt教授的文章：MapReduce: A major step backwards
original: https://blog.csdn.net/zedware/article/details/5094852
---
*以下引用的两篇文章作者为：David J. DeWitt and Michael Stonebraker* 
[MapReduce: A major step backwards](http://databasecolumn.vertica.com/database-innovation/mapreduce-a-major-step-backwards/ "MapReduce: A major step backwards")，说明了MapReduce虽然很热也可能很好，但是： 
1. A giant step backward in the programming paradigm for large-scale data intensive applications  

2. A sub-optimal implementation, in that it uses brute force instead of indexing
3. Not novel at all — it represents a specific implementation of well known techniques developed nearly 25 years ago
4. Missing most of the features that are routinely included in current DBMS 
5. Incompatible with all of the tools DBMS users have come to depend on

[MapReduce II](http://databasecolumn.vertica.com/database-innovation/mapreduce-ii/ "MapReduce II")，主要从以下几个Comments中的典型问题做了回应：
1. MapReduce is not a database system, so don’t judge it as one  

2. MapReduce has excellent scalability; the proof is Google’s use 
3. MapReduce is cheap and databases are expensive 
4. We are the old guard trying to defend our turf/legacy from the young turks

    也许有人会说，当初关系数据库刚出来的时候，不是也有一群老同志反对吗，那个获得过图灵奖的[Charles Bachman](http://en.wikipedia.org/wiki/Charles_Bachman)还反对得最起劲呢！严格来说，这两件事还是有本质不同的。关系数据库和网状数据库等要解决的问题是类似的；MapReduce和传统数据库要解决的问题目前看来还是有很大区别的。只不过石刹车教授和David DeWitt依据多年的数据库研究特别是并行数据库研究经验，给年轻人提出了忠告。适当泼点冷水还是有好处的。
 
   p.s. 喜欢吵架的同学可以看这里：[http://c2.com/cgi/wiki?ModernDinosaurDiscussion](http://c2.com/cgi/wiki?ModernDinosaurDiscussion) 。
 
