---
layout: post
title: 8 gdb tricks you should know
original: https://blog.csdn.net/zedware/article/details/20305407
---
如果你整天看coredump或者抓bug，对这些应该已经很熟悉了。
###8 gdb tricks you should know
####By Ksplice Post Importer on [Jan
 24, ](https://blogs.oracle.com/ksplice/entry/8_gdb_tricks_you_should#)[2011](https://blogs.oracle.com/ksplice/entry/8_gdb_tricks_you_should#)
Despite its age, gdb remains an amazingly versatile and flexible tool, andmastering it can save you huge amounts of time when trying to debug problems inyour code. In this post, I'll share 10 tips and tricks for
 using GDB to debugmost efficiently.
I'll be using the Linux kernel for examples throughout this post, not becausethese examples are necessarily realistic, but because it's a large C codebasethat I know and that anyone can download and take a look
 at. Don't worry if youaren't familiar with Linux's source in particular -- the details of the exampleswon't matter too much.
1. []()`break WHERE if COND`
If you've ever used gdb, you almost certainly know about the "breakpoint"command, which lets you break at some specified point in the debugged program.
But did you know that you can set conditional breakpoints? If you add `ifCONDITION` to a breakpoint command, you can include an expression to beevaluated whenever the program reaches that point, and
 the program will onlybe stopped if the condition is fulfilled. Suppose I was debugging the Linuxkernel and wanted to stop whenever init got scheduled. I could do:
	(gdb) break context_switch if next == init_task
Note that the condition is evaluated by gdb, not by the debugged program, soyou still pay the cost of the target stopping and switching to gdb every timethe breakpoint is hit. As such, they still slow the target
 down in relation toto how often the target location is hit, not how often the condition is met.

2. []()`command`
In addition to conditional breakpoints, the `command` command lets you specifycommands to be run every time you hit a breakpoint. This can be used for anumber of things, but one of the most basic is
 to augment points in a programto include debug output, without having to recompile and restart theprogram. I could get a minimal log of every `mmap()` operation performed on asystem using:
	(gdb) b do_mmap_pgoff Breakpoint 1 at 0xffffffff8111a441: file mm/mmap.c, line 940.(gdb) command 1Type commands for when breakpoint 1 is hit, one per line.End with a line saying just "end".>print addr>print len>print prot>end(gdb)

3. []()`gdb --args`
This one is simple, but a huge timesaver if you didn't know it. If you justwant to start a program under gdb, passing some arguments on the command line,you can just build your command-line like usual, and then
 put "gdb --args" infront to launch gdb with the target program and the argument list both set:
	[~]$ gdb --args pizzamaker --deep-dish --toppings=pepperoni...(gdb) show argsArgument list to give program being debugged when it is started is  " --deep-dish --toppings=pepperoni".(gdb) b mainBreakpoint 1 at 0x45467c: file oven.c, line 123.(gdb) run...
I find this especially useful if I want to debug a project that has some arcanewrapper script that assembles lots of environment variables and possiblyarguments before launching the actual binary (I'm looking at
 you,libtool). Instead of trying to replicate all that state and then launch gdb,simply make a copy of the wrapper, find the final "exec" call or similar, andadd "gdb --args" in front.

4. []()Finding source files
I run Ubuntu, so I can download debug symbols for most of the packages on mysystem from [ddebs.ubuntu.com](http://ddebs.ubuntu.com/), and I can get sourceusing `apt-get
 source`. But how do I tell gdb to put the two together? If thedebug symbols include relative paths, I can use gdb's `directory` command toadd the source directory to my source path:
	[~/src]$ apt-get source coreutils[~/src]$ sudo apt-get install coreutils-dbgsym[~/src]$ gdb /bin/lsGNU gdb (GDB) 7.1-ubuntu(gdb) list main1192    ls.c: No such file or directory.    in ls.c(gdb) directory ~/src/coreutils-7.4/src/Source directories searched: /home/nelhage/src/coreutils-7.4:$cdir:$cwd(gdb) list main1192        }1193    }1194    1195    int1196    main (int argc, char **argv)1197    {1198      int i;1199      struct pending *thispend;1200      int n_files;1201
Sometimes, however, debug symbols end up with absolute paths, such as thekernel's. In that case, I can use `set substitute-path` to tell gdb how totranslate paths:
	[~/src]$ apt-get source linux-image-2.6.32-25-generic[~/src]$ sudo apt-get install linux-image-2.6.32-25-generic-dbgsym[~/src]$ gdb /usr/lib/debug/boot/vmlinux-2.6.32-25-generic (gdb) list schedule5519    /build/buildd/linux-2.6.32/kernel/sched.c: No such file or directory.    in /build/buildd/linux-2.6.32/kernel/sched.c(gdb) set substitute-path /build/buildd/linux-2.6.32 /home/nelhage/src/linux-2.6.32/(gdb) list schedule5519    5520    static void put_prev_task(struct rq *rq, struct task_struct *p)5521    {5522        u64 runtime = p->se.sum_exec_runtime - p->se.prev_sum_exec_runtime;5523    5524        update_avg(&p->se.avg_running, runtime);5525    5526        if (p->state == TASK_RUNNING) {5527            /*5528             * In order to avoid avg_overlap growing stale when we are

5. []()Debugging macros
One of the standard reasons almost everyone will tell you to prefer inlinefunctions over macros is that debuggers tend to be better at dealing withinline functions. And in fact, by default, gdb doesn't know anything
 at allabout macros, even when your project was built with debug symbols:
	(gdb) p GFP_ATOMICNo symbol "GFP_ATOMIC" in current context.(gdb) p task_is_stopped(&init_task)No symbol "task_is_stopped" in current context.
However, if you're willing to tell GCC to generate debug symbols specificallyoptimized for gdb, using `-ggdb3`, it can preserve this information:
	$ make KCFLAGS=-ggdb3...(gdb) break schedule(gdb) continue(gdb) p/x GFP_ATOMIC$1 = 0x20(gdb) p task_is_stopped_or_traced(init_task)$2 = 0
You can also use the `macro` and `info macro` commands to work with macrosfrom inside your gdb session:
	(gdb) macro expand task_is_stopped_or_traced(init_task)expands to: ((init_task->state & (4 | 8)) != 0)(gdb) info macro task_is_stopped_or_tracedDefined at include/linux/sched.h:218  included at include/linux/nmi.h:7  included at kernel/sched.c:31#define task_is_stopped_or_traced(task) ((task->state & (__TASK_STOPPED | __TASK_TRACED)) != 0)
Note that gdb actually knows which contexts macros are and aren't visible, sowhen you have the program stopped inside some function, you can only accessmacros visible at that point. (You can see that the "included
 at" lines aboveshow you through exactly what path the macro is visible).

6. []()gdb variables
Whenever you `print` a variable in gdb, it prints this weird `$NN =` before itin the output:
	(gdb) p 5+5$1 = 10
This is actually a gdb variable, that you can use to reference that samevariable any time later in your session:
	(gdb) p $1$2 = 10
You can also assign your own variables for convenience, using `set`:
	(gdb) set $foo = 4(gdb) p $foo$3 = 4
This can be useful to grab a reference to some complex expression or similarthat you'll be referencing many times, or, for example, for simplicity inwriting a conditional breakpoint (see tip 1).

7. []()Register variables
In addition to the numeric variables, and any variables you define, gdb exposesyour machine's registers as pseudo-variables, including some cross-architecturealiases for common ones, like `$sp` for the
 the stack pointer, or `$pc` for theprogram counter or instruction pointer.
These are most useful when debugging assembly code or code without debuggingsymbols. Combined with a knowledge of your machine's calling convention, forexample, you can use these to inspect function parameters:
	(gdb) break write if $rsi == 2
will break on all writes to stderr on amd64, where the `$rsi` register is usedto pass the first parameter.

8. []()The `x` command
Most people who've used gdb know about the `print` or `p` command,because of its obvious name, but I've been surprised how many don'tknow about the power of the `x` command.
`x` (for "e**x**amine") is used to output regions ofmemory in various formats. It takes two arguments in a slightlyunusual syntax:
	x/FMT ADDRESS
`ADDRESS`, unsurprisingly, is the address to examine; It can be anarbitrary expression, like the argument to `print`.
`FMT` controls how the memory should be dumped, and consists of (upto) three components:
- A numeric COUNT of how many elements to dump
- A single-character FORMAT, indicating how to interpret and display each element
- A single-character SIZE, indicating the size of each element to display.

`x` displays COUNT elements of length SIZE each, starting fromADDRESS, formatting them according to the FORMAT.
There are many valid "format" arguments; `help x` in gdb will giveyou the full list, so here's my favorites:
`x/x` displays elements in hex, `x/d` displays them as signeddecimals, `x/c` displays characters, `x/i` disassembles memory asinstructions, and `x/s` interprets
 memory as C strings.
The SIZE argument can be one of: `b`, `h`, `w`, and `g`, for one-,two-, four-, and eight-byte blocks, respectively.
If you have debug symbols so that GDB knows the types of everythingyou might want to inspect, `p` is usually a better choice, but ifnot, `x` is invaluable for taking a look at memory.
	[~]$ grep saved_command /proc/kallsymsffffffff81946000 B saved_command_line(gdb) x/s 0xffffffff81946000ffffffff81946000 <>:     "root=/dev/sda1 quiet"
`x/i` is invaluable as a quick way to disassemble memory:
	(gdb) x/5i schedule   0xffffffff8154804a <schedule>:   push   %rbp   0xffffffff8154804b <schedule+1>: mov    $0x11ac0,%rdx   0xffffffff81548052 <schedule+8>: mov    %gs:0xb588,%rax   0xffffffff8154805b <schedule+17>:    mov    %rsp,%rbp   0xffffffff8154805e <schedule+20>:    push   %r15
If I'm stopped at a segfault in unknown code, one of the firstthings I try is something like `x/20i $ip-40`, to get a look at whatthe code I'm stopped at looks like.
A quick-and-dirty but surprisingly effective way to debug memoryleaks is to let the leak grow until it consumes most of a program'smemory, and then attach `gdb` and just `x` random pieces
 ofmemory. Since the leaked data is using up most of memory, you'llusually hit it pretty quickly, and can try to interpret what it musthave come from.


~[nelhage](https://twitter.com/nelhage)***
**Ksplice is hiring!**
Do you love tinkering with, exploring, and debugging Linux systems? Does writing Python clones of your favorite childhood computer games sound like a fun weekend project? Have you ever told a joke whose punch line
 was a git command?
Join Ksplice and work on technology that most people will tell you is impossible: updating the Linux kernel while it is running.
Help us develop the software and infrastructure to bring rebootless kernel updates to Linux, as well as new operating system kernels and other parts of the software stack. We're hiring backend, frontend, and kernel
 engineers. Say hello at [jobs@ksplice.com](mailto:jobs@ksplice.com)!
Category: programming ::: 
Tags: [c](https://blogs.oracle.com/ksplice/tags/c) [debugging](https://blogs.oracle.com/ksplice/tags/debugging) [gdb](https://blogs.oracle.com/ksplice/tags/gdb) [linux](https://blogs.oracle.com/ksplice/tags/linux) [tricks](https://blogs.oracle.com/ksplice/tags/tricks) :::
[Permanent link to this entry](https://blogs.oracle.com/ksplice/entry/8_gdb_tricks_you_should) :::
« [Coffee shop Internet...](https://blogs.oracle.com/ksplice/entry/coffee_shop_internet_access) | [Main](https://blogs.oracle.com/ksplice/) | [Happy
 Birthday Kspli...](https://blogs.oracle.com/ksplice/entry/happy_birthday_ksplice_uptrack) »