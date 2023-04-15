---
layout: post
title: What's stack overflow?
tags: [Stack Overflow]
---

* * *

    文章中用到的示例程序：[stack.zip](stack.zip) 。

    顾名思义，stack overflow 就是是栈溢出了。在进行数值运算时，我们常常要和运算结果的溢出打交道。数值运算结果可能上溢（overflow），也可能是下溢（underflow）。不过栈的溢出显然只可能是上溢，即栈空间被用完了。在提起“栈”（stack）这个概念的时候，千万不要忘记了它的兄弟“堆”（heap），也要切记不要把二者搞混了。  
    那么，什么时候会把给用完了呢？如果我们记得C程序中的局部变量是在栈中分配的，函数调用会占用一部分栈空间，则可以很容易地构造出相应的测试用例。

1、定义占用空间过大的局部变量所导致的栈溢出

C:\\> more stack_local.c

/*  
 \* Allocate too much memory from stack will cause stack overflow.  
 */

#include <stdio.h>

int main(int argc, char *argv\[\])  
{  
    int foo\[1000000\];  
    return 0;  
}

C:\\> cl stack_local.c  
Microsoft (R) 32-bit C/C++ Optimizing Compiler Version 14.00.50727.42 for 80x86  
Copyright (C) Microsoft Corporation. All rights reserved.

stack_local.c  
Microsoft (R) Incremental Linker Version 8.00.50727.42  
Copyright (C) Microsoft Corporation. All rights reserved.

/out:stack_local.exe  
stack_local.obj

C:\\> stack_local

此时出现一个异常对话框：[stack-local.jpg](images/jpg/stack-local.jpg) 。

2、函数递归调用导致的栈溢出

C:\\> more stack_recursive.c

/*  
 \* Infinite recursive calls will lead to stack overflow soon.  
 */

#include <stdio.h>

static void foo(void);  
static void bar(void);

int main(int argc, char *argv\[\])  
{  
    foo();  
    return 0;  
}

static void foo(void)  
{  
    bar();  
}

static void bar(void)  
{  
    foo();  
}

C:\\> cl stack_recursive.c  
Microsoft (R) 32-bit C/C++ Optimizing Compiler Version 14.00.50727.42 for 80x86  
Copyright (C) Microsoft Corporation. All rights reserved.

stack_recursive.c  
Microsoft (R) Incremental Linker Version 8.00.50727.42  
Copyright (C) Microsoft Corporation. All rights reserved.

/out:stack_recursive.exe  
stack_recursive.obj

C:\\> stack_recursive

该程序没声没息就结束了。查看进程返回值能发现它其实是异常终止了。只不过没有像 stack_local 那样弹出一个对话框。

C:\\> echo %errorlevel%  
-1073741819

    要搞清楚这两个程序为什么有这点细微的区别，可以查阅一下二者的汇编代码。原来是 \_chkstk() 在起作用，其中 stack\_local 在程序初始加载时就会导致 \_chkstk() 失败，触发异常。而 stack\_recursive 可以正确加载，并运行一段时间，然后导致栈溢出，并触发异常。

    要正确处理栈溢出采用以下办法：  
（1）修正我们的程序，不要造成无穷递归或太深的递归。我们可以把某些递归代码非递归化，例如那个经典的 qsort ，最好就用非递归的算法来实现，就比较皮实一点。  
（2）修正我们的程序，不要定义过大的局部变量，特别是在定义大结构、大数组时要格外小心。有时我们可能会用 _alloca() 这样的特殊函数直接在栈上分配空间，更要多加注意。  
（3）利用编译器的特性，将进程允许的栈大小设置得大一些。例如可以采用 MSC 中的 /STACK 参数开关。  
（4）对于那些还可能导致栈溢出的代码，采用 Microsoft 的结构化异常处理或标准的 C++ 异常处理机制，结合 _resetstkoflw() 进行处理。当然了，要是不嫌麻烦，我们也可以自己探测所用栈的大小，动态地检测是否可能导致栈溢出，以避免可能的异常。

* * *

[Copyright 2006-2007,zedware\_at\_gmail\_dot\_com](mailto:zedware_at_gmail_dot_com)  
Last modified on Thursday, 2006-08-10
