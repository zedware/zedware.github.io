---
layout: post
title: How to install NetWorker in Linux?
original: https://blog.csdn.net/zedware/article/details/7251614
---
NetBackup和NetWorker是两个主要的企业级备份软件，相比桌面上的软件，安装和使用都有点麻烦。
这里采用的是一个x86 32bit的NetWorker试用版本。安装/启动GUI的过程中可能会出现一些小问题，如果不幸遇到了，那就一一解决吧。我曾经遇到过Sybase、Java等各模块的问题，通过分析log文件和查找支持文档，最终都解决了。
Uninstall:
   rpm -e lgtonmc-7.6.1-1.i686
   rpm -e lgtoserv-7.6.1-1.i686
   rpm -e lgtoman-7.6.1-1.i686
   rpm -e lgtonode-7.6.1-1.i686
   rpm -e lgtolicm-7.6.1-1.i686
   rpm -e lgtoclnt-7.6.1-1.i686
   rpm -e openmotif-2.3.3-1.i386
   rpm -e libXp-1.0.0-8.1.el5.i386

The packages/rpm can be found:
    /home/zedware/packages/

1. tar -xzf nw76sp1_linux_x86.tar.gz
2. Download libXp.so.6 from:
    wget http://mirror.centos.org/centos/5/os/i386/CentOS/libXp-1.0.0-8.1.el5.i386.rpm
3. Download openmotif from http://www.openmotif.org/:
    wget http://www.openmotif.org/files/public_downloads/openmotif/2.3/2.3.3/openmotif-2.3.3-1.el5.3.i386.rpm
4. Install the packages
   rpm -i libXp-1.0.0-8.1.el5.i386.rpm
   rpm -i openmotif-2.3.3-1.el5.3.i386.rpm
   rpm -i lgtoclnt-7.6.1-1.i686.rpm
   rpm -i lgtolicm-7.6.1-1.i686.rpm
   rpm -i lgtonode-7.6.1-1.i686.rpm
   rpm -i lgtoman-7.6.1-1.i686.rpm
   rpm -i lgtoserv-7.6.1-1.i686.rpm
   rpm -i lgtonmc-7.6.1-1.i686.rpm
6. useradd -m networker
7. /opt/lgtonmc/bin/nmc_config
    # use the user networker just created.
   chkconfig --add networker
   chkconfig --list networker
   chkconfig --add gst
8. service iptables stop
   Or chkconfig --level 12345 iptables off
   Or add rules to port 9000, 9001..., or iptables --flush to delete all rules.
But there are so many ports and processes!
    http://www.tek-tips.com/viewthread.cfm?qid=1518214&page=1
9. hostname rhel5_x86_nwr
    Add "10.x.x.x rhel5_x86_nwr" to etc/hosts.
x. reboot
  Or:
   /usr/sbin/nsrexecd
   /etc/init.d/gst start
x. Download and install JRE 1.6.
    j6u26 or above doesn't work for NWR 7.6.2: https://community.emc.com/thread/127491
x. Install JRE for Firefox: http://it.megocollector.com/?p=832
    cd /usr/lib/mozilla/plugins
    ln -s /usr/java/default/jre/plugin/i386/ns7/libjavaplugin_oji.so
  Or
    ln -s /jre1.6.0/plugin/i386/ns7/libjavaplugin_oji.so
x. Start the GUI http://rhel5_x86_nwr:9000
            