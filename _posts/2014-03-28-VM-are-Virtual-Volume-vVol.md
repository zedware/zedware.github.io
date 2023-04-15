---
layout: post
title: VMware Virtual Volume - vVol
original: https://blog.csdn.net/zedware/article/details/22441709
tags: VM vVol
---

Virtual volume在两年前还是很火热的概念，例如：http://blogs.vmware.com/vsphere/2012/10/virtual-volumes-vvols-tech-preview-with-video.html . 可是2013年的VMware大会上就没有那么多消息了，例如：http://www.networkcomputing.com/storage-networking-management/vmware-gets-real-about-virtual-storage/240161026 . 当然也不会彻底销声匿迹：http://virtualgeek.typepad.com/virtual_geek/2013/08/vmworld-2013-vvol-update-and-vm-granular-storagenow.html . 2014年到处是VSAN。

vVol主要是为了解决大量虚拟机VMDK文件的可管理性问题，套句关系数据库的黑话，叫做阻抗不匹配。虚拟机太多，传统的NFS、SCSI、FC等都管不过来，而且NAS/SAN不感知VMDK，导致很多管理如快照、备份等都是以LUN或Volume为单位的：VM显然不是存储里头的头等公民。不过这个东西需要各个存储厂商的配合，做起来不是那么容易。而且这是一个需要上下打通的特性，多少和传统的分层设计有所区别。

vVol里头主要有这几个角色：storage provider，protocol endpoint以及storage container。SP与此前的VASA很类似。PE当然是用来和LUN或File打交道的接口，不过这个东西要求可以支持大量（记得似乎是百万以上）的VM。实现上还得想办法使得接口可以多路复用以支持这么大一个数，比如以前的一个NFS mount下面挂上一个文件系统，现在要想办法去挂上一堆文件系统。SC就是存储上的storage pool了。VMware的文档里头详细定义了各种API，主要是管理类的，那个文档的版本号很有意思。大概是从a开始的，第一个叫做版本ab，一直到版本mn什么的。
