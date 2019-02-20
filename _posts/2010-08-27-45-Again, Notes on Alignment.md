---
layout: post
title: Again, Notes on Alignment
original: https://blog.csdn.net/zedware/article/details/5844522
---
Alignment是编程时比较普遍的问题。例如采用C/C++之类的语言进行编程时经常要考虑不同CPU体系结构带来的字节对齐问题，有时甚至要考虑cache line的对齐问题。不幸的是，随着硬盘技术的发展，它也出现了Alignment的问题。因为扇区大小从以前大家熟知的512Byte增长到了4096Byte：[http://bugs.gentoo.org/show_bug.cgi?id=304727](http://bugs.gentoo.org/show_bug.cgi?id=304727)。硬盘作为一种基本的存储设备，它所引起的问题就会影响到几乎所有需要大量I/O的应用。数据库就是这么一种典型的需要大量I/O的应用。如何考虑RAID的条带大小、分区的起始位置以及硬盘扇区的大小呢？[http://blogs.msdn.com/b/jimmymay/archive/2009/05/08/disk-partition-alignment-sector-alignment-make-the-case-with-this-template.aspx](http://blogs.msdn.com/b/jimmymay/archive/2009/05/08/disk-partition-alignment-sector-alignment-make-the-case-with-this-template.aspx)给出了一个不错的指导。
 
