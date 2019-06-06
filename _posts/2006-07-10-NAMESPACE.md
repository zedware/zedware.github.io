---
layout: post
title: Ambiguous call to overloaded function
---

* * *

先看看我们的测试程序：

$ cat -n namespace.cpp  
 1  
 2  #include <cassert>  
 3  #include <cstdio>  
 4  #include <cstdlib>  
 5  
 6  /* Here is our namespace Foo */  
 7  namespace Foo  
 8  {  
 9      void sub(void)  
10     {  
11         printf("Foo::sub()\\n");  
12     }  
13  }  
14  
15  /* sub() is in global namespace. */  
16  void sub(void)  
17  {  
18     printf("global sub()\\n");  
19  }  
20  
21  int main(int argc, char *argv\[\])  
22  {  
23      /* calls sub() in global namespace. */  
24      sub();  
25      /* calls sub() in namespace Foo. */  
26      Foo::sub();  
27  
28      /* change search path to global and Foo. */  
29      using namespace Foo;  
30  
31      /* compiler cannot determine which sub() to be called. */  
32      // expect error  
33      // sub();  
34      //  
35  
36      /* tell the compiler which one to use. */  
37      ::sub();  
38      Foo::sub();  
39  
40      return 0;  
41  }  
42

D:\\> version  
Microsoft Windows Server 2003, Enterprise Edition Service Pack 1 (Build 3790)

D:\\> cl /DWIN32 namespace.cpp  
Microsoft (R) 32-bit C/C++ Optimizing Compiler Version 12.00.8804 for 80x86  
Copyright (C) Microsoft Corp 1984-1998. All rights reserved.

namespace.cpp  
Microsoft (R) Incremental Linker Version 6.00.8447  
Copyright (C) Microsoft Corp 1992-1998. All rights reserved.

/out:namespace.exe  
namespace.obj

D:\\> namespace  
global sub()  
Foo::sub()  
global sub()  
Foo::sub()

$ uname -a  
CYGWIN_NT-5.2 uniware 1.5.5(0.94/3/2) 2003-09-20 16:31 i686 unknown unknown Cygwin

$ gcc --version  
gcc (GCC) 3.3.1 (cygming special)  
Copyright (C) 2003 Free Software Foundation, Inc.  
This is free software; see the source for copying conditions. There is NO  
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.

$ gcc -Wall -o namespace namespace.cpp

$ ./namespace  
global sub()  
Foo::sub()  
global sub()  
Foo::sub()

结果和VC++的一致。如果把第33行的注释去掉，则：

D:\\> cl /DWIN32 namespace.cpp  
Microsoft (R) 32-bit C/C++ Optimizing Compiler Version 12.00.8804 for 80x86  
Copyright (C) Microsoft Corp 1984-1998. All rights reserved.

namespace.cpp  
namespace.cpp(33) : error C2668: 'sub' : ambiguous call to overloaded function

$ gcc -Wall namespace.cpp  
namespace.cpp: In function `int main(int, char**)':  
namespace.cpp:33: error: call of overloaded `sub()' is ambiguous  
namespace.cpp:17: error: candidates are: void sub()  
namespace.cpp:10: error: void Foo::sub()

GCC编译的结果和VC++的类似，不过GCC的提示信息更加准确，也有助于定位问题。

人总是会犯错误的，微软的工程师也不例外，[这里](http://support.microsoft.com/kb/243298/en-us)就是Visual C++ 6.0中的类似错误。

* * *

[Copyright 2006-2007,zedware\_at\_gmail\_dot\_com](mailto:zedware_at_gmail_dot_com)  
Last modified on Monday, 2006-07-10
