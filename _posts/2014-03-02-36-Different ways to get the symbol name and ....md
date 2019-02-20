---
layout: post
title: Different ways to get the symbol name and ...
original: https://blog.csdn.net/zedware/article/details/20306143
---
万一你拿到callstack的是一堆的地址，不用担心，只要你有符号信息，就可以用这些命令恢复出熟悉的样子了。
$ addr2line -e foo.gdb -f -s -C 0x1023456Ifoo::compare(unsigned long long)foo.cxx:648
(gdb) l *0x10234560x1023456 is in Ifoo::compare(unsigned long long) (foo.cxx:648).<more lines snipped>
(gdb) info symbol 0x1023456Ifoo::compare(unsigned long long) + 478 in section .text
