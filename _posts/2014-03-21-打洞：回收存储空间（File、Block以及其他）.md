---
layout: post
title: 打洞（Punch a hole）：回收存储空间（File、Block以及其他）
original: https://blog.csdn.net/zedware/article/details/21747885
---
打洞（Hole-punching）这个看起来有点土，容易让人想到“龙生龙凤生凤，老鼠的儿子会打洞”。在虚拟机出现之前，这个问题可能还不太被大家所了解。VM通常会有个很大的文件作为Guest OS的存储，可是你在Guest OS里头删除了很多文件后，这个文件还是占据了很多的空间。怎么让Host文件的文件系统回收掉这些空间呢？如果这些存储是在Amazon这样的地方，占据一堆不用的空间会导致花了冤枉钱。
实际上，高端存储产品中很早就支持类似的功能（Thin Provisioning），例如NetApp这个Blog文章：https://communities.netapp.com/community/netapp-blogs/efficiency/blog/2010/08/04/punching-holes。有兴趣的话，可以深挖一下TRIM，以及SCSI的WRITE_SAME、UNMAP。
这里有个不那么高大上的场景，但是涉及到的场景很有意思，而且可能更好理解一点：https://code.google.com/p/s3backer/wiki/UnsedBlockDeletion。要是一步步测试的话，可以参考这个：http://www.outflux.net/blog/archives/2012/02/15/discard-hole-punching-and-trim/
Linux最初的支持：http://lwn.net/Articles/415889/。更接地气的当属这个，把示例代码都给出来了：https://bitbucket.org/cheater/sparsify/src/9dc09d3f956be44684da294327a622b61041eae1/sparsify.c?at=default#


