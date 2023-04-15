---
layout: post
title: 读《The End of an Architectural Era (It’s Time for a Complete Rewrite)》
original: https://blog.csdn.net/zedware/article/details/19846955
tags: DBMS
---

这是Stonebraker等发表在VLDB ’07, September 23-28, 2007, Vienna, Austria的一篇文章。看起来引用率还很高：
###[**The End of an Architectural Era** - Networks and Mobile Systems - MIT](http://nms.csail.mit.edu/~stavros/pubs/hstore.pdf)
nms.csail.mit.edu/~stavros/pubs/hstore.pdf‎by M Stonebraker - ‎2007 - ‎[Cited
 by 327](http://scholar.google.ca/scholar?um=1&ie=UTF-8&lr&cites=1845876197174939675) - ‎[Related articles](http://scholar.google.ca/scholar?um=1&ie=UTF-8&lr&q=related:GxD2F5_enRnuwM:scholar.google.com/)The End of an Architectural Era. (It's Time for a Complete Rewrite). Michael Stonebraker. Samuel Madden. Daniel J. Abadi. Stavros Harizopoulos. MIT CSAIL.在NoSQL稍微消停了点的今天，回过头看看研究者们走过的路，对我们这种程序猿应该也会有点益处。关系数据库在出现以后，变成了大一统的巨兽，也似乎一统江湖了。不过实际的需求种类实在太多，也不乏有各种专用的系统出现，尤其是各种针对特定应用的解决方案。互联网和MySQL的迅猛发展，也逐渐对传统的RDBMS产生了威胁。文章中的很多假设前提和我们做应用，特别是互联网应用的情况很类似，只不过只有极少数人有能力把它上升到理论的高度，并因此与时俱进地设计出系统来。本文虽然没有提出什么创新的技术，但却做了很好的总结归纳，为设计各种专用系统，特别是专用的OLTP系统提供了很好的依据。
For latest news, please visit the web sites of MIT and VoltDB.  Daniel J. Abadi wrote many more papers on the topic latter.
