---
layout: post
title: How Do Fixes Become Bugs
original: https://blog.csdn.net/zedware/article/details/7266054
---
软件工程的名声可以说一直不太好，理论和实际脱节的比较厉害。不过这篇文章应该有一定的指导意义。如果真有个开源的工具来改进无穷的Bugfix，应该可以较大的提高开发团队的成产效率。
ABSTRACT
Software bugs affect system reliability. When a bug is ex-posed in the field, developers need to fix them.Unfortunately, the bug-fixing process can also introduce errors, which leads to buggy patches that further aggravate the damage
 to end users and erode software vendors’ reputation. This paper presents a comprehensive characteristic study on incorrect bug-fixes from large operating system codebases including Linux, Open Solaris, FreeBSD and also a mature commercial OS developed and
 evolved over the last 12 years, investigating not only the mistake patterns duringbug-fixing but also the possible human reasons in the development process when these incorrect bug-fixes were introduced. Our major findings include: (1) at least 14.8%24.4%
 of sampled fixes for post-release bugs 1 in these large OSes are incorrect and have made impacts to end users. (2) Among
several common bug types, concurrency bugs are the most difficult to fix correctly: 39% of concurrency bug fixes are incorrect. (3) Developers and reviewers for incorrect fixes usually do not have enough knowledge about the involved code. For example, 27%
 of the incorrect fixes are made by developers who have never touched the source code files associated with the fix. Our results provide useful guidelines to design new tools and also to improve the development process. Based on our findings, the commercial
 software vendor whose OS code we evaluated is building a tool to improve the bug fixing and code reviewing process.
 
