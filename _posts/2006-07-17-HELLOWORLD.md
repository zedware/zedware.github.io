---
layout: post
title: 各种语言写就的“hello world”程序
tags: [Hello World]
---

* * *

    “hello world”程序可能是大家经常遇到的入门程序范例，本文中按照从高级语言到低级语言的顺序组织这些例子。我们先看看 SQL 语言的版本：

> SELECT 'hello world';

    不过，要注意的是，在 Oracle 中需要这样：

> SELECT 'hello world' FROM dual;

    再看看脚本类的：

    Shell script: hello.sh

> #!/bin/sh
> 
> #  
> \# sh hello.sh  
> \# or:  
> \# chmod u+x hello.sh  
> \# ./hello.sh  
> #
> 
> echo "hello world"

    awk: hello.awk

> #  
> \# echo "" | awk -f hello.awk  
> #
> 
> {print "hello world"}

    Perl: hello.pl

> #!/bin/perl
> 
> #  
> \# perl hello.pl  
> #
> 
> print "hello world\\n"

    DOS batch file: hello.bat

> @echo off
> 
> rem  
> rem hello.bat  
> rem
> 
> echo hello world

    VBScript: hello.vbs

> '  
> ' hello.vbs  
> '
> 
> MsgBox "hello world"

    再看看高级语言类的：

    C: hello.c

> /*  
> \* gcc -o hello hello.c  
> \* ./hello  
> */
> 
> #include <stdio.h>
> 
> int main(void)  
> {  
>     printf("hello world\\n");  
>     return 0;  
> }

    C++: hello.cc

> //  
> // gcc -o hello hello.cc  
> // ./hello  
> //
> 
> #include <iostream>
> 
> using namespace std;
> 
> int main(void)  
> {  
>     cout << "hello world" << endl;  
>     return 0;  
> }

    Java: hello.java

> //  
> // javac hello.java  
> // java hello.class  
> //
> 
> import java.io.*;
> 
> class hello  
> {  
>     static public void main(String args\[\])  
>     {  
>         System.out.println("hello world");  
>     }  
> }

    我这里仅仅是举了几种语言的简单例子而已。[ACM **"Hello World"** project](http://www2.latech.edu/%7Eacm/HelloWorld.shtml) 收集了更多的用五花八门的语言写就的例子，这也让我们看到了计算机语言家族的兴旺发达。  

* * *

[Copyright 2006-2007,zedware\_at\_gmail\_dot\_com](mailto:zedware_at_gmail_dot_com)  
Last modified on Monday, 2006-07-17
