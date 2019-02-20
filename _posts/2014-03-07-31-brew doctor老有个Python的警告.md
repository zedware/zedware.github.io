---
layout: post
title: brew doctor老有个Python的警告
original: https://blog.csdn.net/zedware/article/details/20740331
---
$brew doctor
Warning: Python is installed at /Library/Frameworks/Python.framework


Homebrew only supports building against the System-provided Python or a
brewed Python. In particular, Pythons installed to /Library can interfere
with other software installs.

看起来挺烦人的。不熟悉这个东西，只好到处Google了：https://github.com/Homebrew/homebrew/issues/27146
http://docs.python.org/2/using/mac.html

这篇文章介绍了安装brew等的过程，写得比较清楚：http://hackercodex.com/guide/python-development-environment-on-mac-osx/

没有彻底整明白这个警告，暂时留着系统自带的Python。


