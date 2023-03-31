---
layout: post
title: Debugging the Infinite Loop of Scanf
tags: c
---

Somebody wrote a program \`test-scanf.c', and found it loop forever.

    $ less test-scanf.c
    #include <stdio.h>
    
    int main(void) {
        char s[1024];
        while (scanf("%[^\n]", s) != EOF) {
    	printf("s=%s\n", s);
        }
        return 0;
    }

If we input "abc<CR>", the program will print "abc" forever without
waiting the end user to input new characters. Take a careful look at
the man page of scanf, we will find the return value is important to
analyze the problem. The changed version follows.

    $ less test-scanf.c
    #include <stdio.h>
    
    int main(void) {
        char s[1024];
        int rc;
        while ((rc = scanf("%[^\n]", s)) != EOF) {
    	printf("rc=%d\n", rc);
    	printf("s=%s\n", s);
        }
        return 0;
    }

With the same input, we can see rc=1 for the fist output, then rc=0
for the others. Which means the scanf() never matches any valid inputs
except for the first loop. A quick guess is that it sees the '\n', and
put it back to the internal buffer, that the next call of scanf will
do it again, causing an infinite loop.

But we want to dig into the source code to verify the guess. Scanf
it part of glibc, by default we can't step into it.


# Install debug symbols and source code for glibc

On Ubuntu,

    $ sudo apt-get install libc6-dbg
    $ sudo apt-get install libc6-dev
    $ sudo apt-get install glibc-source
    $ ll /usr/src/glibc/glibc-2.33.tar.xz # the source code of glibc
    $ cd /usr/src/glibc
    $ tar -xvf glibc-2.33.tar.xz          # extract it
    $ ll /usr/src/glibc/glibc-2.33/

    $ less test-scanf.c
    #include <stdio.h>
    
    int main(void) {
        char s[1024];
        int rc;
        while ((rc = scanf("%[^\n]", s)) != EOF) {
    	printf("rc=%d\n", rc);
    	printf("s=%s\n", s);
        }
        return 0;
    }
    
    $ gcc -Wall -g test-scanf.c
    $ ldd a.out                      # list the depended shared libraries of a.out
    linux-vdso.so.1 (0x00007ffceb39e000)
    libachk.so => /lib/libachk.so (0x00007fc8ae3bd000)
    libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007fc8ae1d1000)   <=== glibc
    libdl.so.2 => /lib/x86_64-linux-gnu/libdl.so.2 (0x00007fc8ae1ca000)
    libpthread.so.0 => /lib/x86_64-linux-gnu/libpthread.so.0 (0x00007fc8ae1a8000)
    librt.so.1 => /lib/x86_64-linux-gnu/librt.so.1 (0x00007fc8ae19d000)
    /lib64/ld-linux-x86-64.so.2 (0x00007fc8ae4db000)

Let's debug it.

    $ gdb -q a.out
    Reading symbols from a.out...
    (gdb) l
    1       #include <stdio.h>
    2
    3       int main(void) {
    4           char s[1024];
    5           int rc;
    6           while ((rc = scanf("%[^\n]", s)) != EOF) {
    7               printf("rc=%d\n", rc);
    8               printf("s=%s\n", s);
    9           }
    10          return 0;
    (gdb) b 6
    Breakpoint 1 at 0x11a7: file scanf3.c, line 6.
    
    (gdb) set verbose on
    
    (gdb) r
    Starting program: /home/wsl/sandbox/tcpl/a.out
    Using PIE (Position Independent Executable) displacement 0x555555554000 for "/home/wsl/sandbox/tcpl/a.out".
    Reading symbols from /lib64/ld-linux-x86-64.so.2...
    (No debugging symbols found in /lib64/ld-linux-x86-64.so.2)
    Reading symbols from system-supplied DSO at 0x7ffff7fc8000...
    (No debugging symbols found in system-supplied DSO at 0x7ffff7fc8000)
    Reading symbols from /lib/libachk.so...
    Reading symbols from /lib/x86_64-linux-gnu/libc.so.6...
    Reading symbols from /usr/lib/debug//lib/x86_64-linux-gnu/libc-2.33.so...       <=== glibc's symbols
    Reading symbols from /lib/x86_64-linux-gnu/libdl.so.2...
    Reading symbols from /usr/lib/debug//lib/x86_64-linux-gnu/libdl-2.33.so...
    Reading symbols from /lib/x86_64-linux-gnu/libpthread.so.0...
    Reading symbols from /usr/lib/debug/.build-id/b8/3cc21d7f85674171ad9d0660a7d57245d654d4.debug...
    [Thread debugging using libthread_db enabled]
    Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".
    Reading symbols from /lib/x86_64-linux-gnu/librt.so.1...
    Reading symbols from /usr/lib/debug//lib/x86_64-linux-gnu/librt-2.33.so...
    Reading symbols from /lib/x86_64-linux-gnu/libnss_files.so.2...
    Reading symbols from /usr/lib/debug//lib/x86_64-linux-gnu/libnss_files-2.33.so...
    
    Breakpoint 1, main (Reading in symbols for ../sysdeps/x86/libc-start.c...
    ) at scanf3.c:6
    6           while ((rc = scanf("%[^\n]", s)) != EOF) {
    
    (gdb) s
    Reading in symbols for isoc99_scanf.c...
    __isoc99_scanf (format=0x555555556011 "%[^\n]%*c") at isoc99_scanf.c:25
    25      isoc99_scanf.c: No such file or directory.            <=== can't find the source file of glibc
    
    (gdb) dir /usr/src/glibc/glibc-2.33                           <=== fix it
    Source directories searched: /usr/src/glibc/glibc-2.33:$cdir:$cwd
    (gdb) n
    29        va_start (arg, format);
    (gdb) n
    30        done = __vfscanf_internal (stdin, format, arg, SCANF_ISOC99_A);
    (gdb) s
    Reading in symbols for vfscanf-internal.c...
    0x00007ffff7d209ce in __vfscanf_internal (s=0x7ffff7e9f9a0 <_IO_2_1_stdin_>, format=0x555555556011 "%[^\n]%*c", argptr=argptr@entry=0x7fffffffdb20, mode_flags=mode_flags@entry=2) at vfscanf-internal.c:278
    278     {
    (gdb) n
    289       struct __locale_data *const curctype = loc->__locales[LC_CTYPE];
    (gdb) b 2894                                                  <=== read the source code please
    Reading in symbols for vfscanf-internal.c...
    Breakpoint 2 at 0x7ffff7d25651: file vfscanf-internal.c, line 2894.
    (gdb) c
    Continuing.
    a
    
    Breakpoint 2, __vfscanf_internal (s=<optimized out>, format=<optimized out>, argptr=argptr@entry=0x7fffffffdb20, mode_flags=mode_flags@entry=2) at vfscanf-internal.c:2894
    2894                          ungetc_not_eof (c, s);
    (gdb) p/c c
    $1 = 10 '\n'

That's because after the input of "a<CR>", '\n' is unput back to the buffer, and
the call of scanf() in the next loop will see '\n' again and goto line:2894.

    2890: do
    2891: {
    2892:    if (((char *) charbuf.scratch.data)[c] == not_in)
    2893:    {
    2894:        ungetc_not_eof (c, s);
    2895:        break;
    2896:    }


# How to fix the infinite loop?

    $ less test-scanf.c
    #include <stdio.h>
    
    int main(void) {
        char s[1024];
        int rc;
        while ((rc = scanf("%[^\n]%*c", s)) > 0) {
    	printf("s=%s\n", s);
        }
        return 0;
    }

The straightforward way is consuming the '\n' proactivelly. For example,
"%c" can consume a character, including '\n'. Since we don't want the
character, we can use the "%\*c" format specifier.

Lastly, the source code above have the potential security risk of buffer
overflow of s[]. We can add the length after '%': %1000[^\n].

