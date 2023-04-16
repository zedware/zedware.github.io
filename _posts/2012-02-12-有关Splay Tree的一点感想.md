---
layout: post
title: 有关Splay Tree的一点感想
original: https://blog.csdn.net/zedware/article/details/7253327
tags: [Splay Tree]
---

Splay Tree是一种有趣的Binary Search Tree。Wikipedia说它的缺点为：

Perhaps the most significant disadvantage of splay trees is that the height of a splay tree can be linear. For example, this will be the case after accessing all *n* elements in non-decreasing order. Since the height of a tree corresponds to the worst-case access time, this means that the actual cost of an operation can be slow. However the [amortized](http://en.wikipedia.org/wiki/Amortized "Amortized") access cost of this worst case is logarithmic, O(log *n*). Also, the expected access cost can be reduced to O(log *n*) by using a randomized variant[[2]](http://en.wikipedia.org/wiki/Splay_tree#cite_note-1).
A splay tree can be worse than a static tree by at most a constant factor.
Splay trees can change even when they are accessed in a 'read-only' manner (i.e. by *find* operations). This complicates the use of such splay trees in a multi-threaded environment. Specifically, extra management is needed if multiple threads are allowed to perform *find* operations concurrently.

碰巧发现Raymond Chen曾经也写过一点评论：[The cost of trying too hard: Splay trees](http://blogs.msdn.com/b/oldnewthing/archive/2006/01/18/514298.aspx)，提到了上述的一些缺点。

结合以前的工作，联想到了一下几点：
1. B+树为了加速顺序访问而提供了叶子节点之间的链接。
2. 系统中用户看到的只读访问有时确实不是完全彻底的“只读”：
   1. 系统可能会调整内存结构中的信息，例如Cache相关的元信息。
   1. 系统可能会调整外存结构中的信息，例如文件的Access时间。
   1. 系统可能会记录事务日志。
3. 一旦需要考虑并发和事务，事情就会变得有点复杂。


