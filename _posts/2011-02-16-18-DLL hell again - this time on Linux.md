---
layout: post
title: DLL hell again - this time on Linux
original: https://blog.csdn.net/zedware/article/details/6189219
---
突然发现Fedora中的yum不好使了，一执行就报错，说什么不能import某某库。
折腾半天，发现曾经手工装过Python，莫非是它搞出了包的冲突？
 
尝试查找rpm并逐一安装，期间出现的错误多多，不是import某某就是无法找到某某符号。
总结一下：
1. 发现错误不要惊慌，程序还是讲道理的；
2. import之类的错误，可以在Python交互式界面中重现一下。然后大致看看代码，并用
rpm -qlai 之类的命令查查缺的包到底叫啥。发现后去下载一个安上。
3. 无法找到某某符号之类的错误一般是系统中的.so不对。可以同上看看是哪个rpm，找来
安上。
4. 个别某某符号无法找到可能是ld.so.conf之类的配置中有重名的低版本so。例如我不幸
的发现有个软件安装了个低版本的libcurl.so了。
 
我们不会是第一个倒霉蛋，但是也确实可能遇到的问题与别人不完全一样。是程序总是讲
道理的。根据具体情况具体分析，总能解决问题增长经验的。这里是和我遇到的某个问题
类似的场景：[[SOLVED] YUM: undefined symbol: CRYPTO_set_locking_callback](http://www.linuxquestions.org/questions/fedora-35/yum-undefined-symbol-crypto_set_locking_callback-839211/)
