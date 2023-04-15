---
layout: post
title: Build distcc from source code
original: https://blog.csdn.net/zedware/article/details/6153307
tags: C cplusplus
---

distcc is a useful tool to speed up the build. We can dowload it from:
http://code.google.com/p/distcc/. 
 
60-second instructions
 
1.For each machine, download distcc, unpack, and do
./configure && make && sudo make install
 
2.On each of the servers, run distccd --daemon, with --allow options to restrict access.
 
3.Put the names of the servers in your environment:
export DISTCC_POTENTIAL_HOSTS='localhost red green blue'
 
4.Build!
cd ~/work/myproject; pump make -j8 CC=distcc
 
Our own steps to get and build it:
 
1. Download it.
 
$ pwd
/home/zedware
 
$ wget http://distcc.googlecode.com/files/distcc-3.1.tar.bz2
 
2. Try to build it.
 
$ tar -xjfv distcc-3.1.tar.bz2
$ cd distcc-3.1
$ ./configure --prefix=/home/zedware/usr && make && make install
 
Oops. Failed with some errors. We should take a look at:
http://code.google.com/p/distcc/issues/detail?id=29.
 
3. Compile and build Python 2.4 or latter.
 
   We don't have root permissions, install it to /home/zedware/usr/bin.
 
4. Set environments and build distcc again.
 
  $ export PYTHON=/home/zedware/usr/bin/python && ./configure --prefix=/home/zedware/usr
  $ make
  $ make install
 
   Use setenv in csh, export in bash.
 
Goto "60-second instructions".
