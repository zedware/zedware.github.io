---
layout: post
title: An Analysis of Data Corruption in the Storage Stack
original: https://blog.csdn.net/zedware/article/details/6124626
---
                http://www.usenix.org/event/fast08/tech/full_papers/bairavasundaram/bairavasundaram_html/index.html

磁盘会有故障，RAID也会，控制器也会，文件系统，数据库，应用都会。不过大多数软件都是依靠硬件（磁盘和RAID）检错和纠错的。文件系统已经意识到问题了，并且着手解决它们。Oracle数据库也有一些检错和纠错的办法。Oracle也鼓吹其Unbreakable Linux可以更好的处理silent data error。这里有一篇在MySQL上做相关实验的Paper：http://www.cs.wisc.edu/adsl/Publications/corrupt-mysql-icde10.pdf。看致谢也是NetApp赞助的课题，它引用了usenix上面的这篇文章。            