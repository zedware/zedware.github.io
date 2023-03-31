---
layout: post
title: Shell and SQL Make Friends
tags: sql shell
---

The old school guys tend to use Shell scripts for daily operations. Tcl, Perl, Ruby
came and go. Python seems to be the most popular tool used nowadays. For those who
work on database for their daily life, shouldn't SQL be a good alternative? One of 
the tools is "q". Let's see how to use it and cooperate with Shell script.

    $ wget https://github.com/harelba/q/releases/download/latest/macos-q
    
    $ chmod a+x macos-q 
    
    $ ps -ef | head -3
      UID   PID  PPID   C STIME   TTY           TIME CMD
        0     1     0   0 Tue12AM ??         0:21.23 /sbin/launchd
        0    76     1   0 Tue12AM ??         0:10.59 /usr/libexec/logd

Count the number of process owned by each user and sort it descending by the count.
The head line is removed by sed, and there is no head line in the final results.

    $ ps -ef | sed '1d' | awk '{print $1}' | sort | uniq -c | sort -n -r -k1
     232 501
     225 502
     147 0
       8 278
       7 205
       5 89
       4 202
       4 200
       3 88
       3 265
       3 262
       3 260

The SQL alternative. "-" means stdin. "-H" means the input has a head line. To add 
the head line in the final result, simply add -O.

    $ ps -ef | ./macos-q -H "select count(*),uid from - group by uid order by 1 desc"
    236 501
    224 502
    149 0
    8 278
    7 205
    5 89
    4 202
    4 200
    3 265
    3 262
    3 260

We can pipe the output of q to Shell commands just as usual.

    $ ./macos-q "select * from /var/log/system.log where c6 like 'ASL'" | wc -l
         15


