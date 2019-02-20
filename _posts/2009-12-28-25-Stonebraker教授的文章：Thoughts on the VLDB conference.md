---
layout: post
title: Stonebraker教授的文章：Thoughts on the VLDB conference
original: https://blog.csdn.net/zedware/article/details/5094669
---
    Stonebraker是个著名数据库研究人员，开源数据库中的代表PostgreSQL就是源于他在加州大学伯克利分校的科研成果。
 
[http://databasecolumn.vertica.com/database-miscellaneous/thoughts-on-the-vldb-conference/](http://databasecolumn.vertica.com/database-miscellaneous/thoughts-on-the-vldb-conference/) 
 
#[Thoughts on the VLDB conference](http://databasecolumn.vertica.com/database-miscellaneous/thoughts-on-the-vldb-conference/ "Thoughts on the VLDB conference")
on Oct 12 in [Conferences & events](http://databasecolumn.vertica.com/category/conferences-events/ "View all posts in Conferences & events"), [Database history](http://databasecolumn.vertica.com/category/database-history/ "View all posts in Database history"), [Database miscellaneous](http://databasecolumn.vertica.com/category/database-miscellaneous/ "View all posts in Database miscellaneous") posted by [Stonebraker](http://databasecolumn.vertica.com/author/stonebraker/ "Posts by Stonebraker")
 
I attended[VLDB 2007](http://www.vldb2007.org/)(33rd International Conference on Very Large Databases) in Vienna, Austria 2 weeks ago. Here are a few highlights for me.
**
Some DBMS trivia about early RDBMS design**
According to one senior researcher that I talked to,[Ted Codd](http://en.wikipedia.org/wiki/Ted_Codd)designed a complete database programming language in the late 1960s  (i.e., something along the lines of[Pascal/R](http://hopl.murdoch.edu.au/showlanguage.prx?exp=919&language=Pascal/R), Rigel, or the modern-day[Ruby on Rails](http://en.wikipedia.org/wiki/Ruby_on_rails)).  According to this researcher, Ted knew he could never win against[PL/1](http://en.wikipedia.org/wiki/Pl/1)within IBM. Hence, he focused on a data sublanguage which could be called from PL/1. This is the reason for the early RDBMS decision to focus on a data sublanguage rather than a database programming
 language, again according to this researcher.
**More progress needs to be made on DBA interfaces**
Another researcher who works for a very large enterprise said the databases they use all have more than 100 “tuning knobs.” His average DBA (of which his company has more than 1,000) uses less than a dozen. His biggest hot button was simplification of the
 DBA interface. He also claimed that the automatic tuning aids from the major OLTP databases were not good enough for “prime time” and his DBAs did not use them.
**Yahoo!, unstructured search, and community portals**
Another researcher said that the Google data management facilities ([Google file system](http://labs.google.com/papers/gfs.html),[MapReduce](http://labs.google.com/papers/mapreduce.html), etc.) have been duplicated and are seemingly available to Yahoo! This will make scalable, brute-force searching of unstructured data more widely available. While on the subject
 of Yahoo!, it is building a “portal factory” that can crank out vertical market-specific portals for communities of users, along the lines of its[portal for database researchers](http://www-db.cs.wisc.edu/cidr/cidr2007/papers/cidr07p19.pdf)described in[CIDR 2007](http://www-db.cs.wisc.edu/cidr/cidr2007/index.html).
**Thoughts on the OLPC project **
Lastly, I enjoyed immensely the invited keynote speech by[Eric Brewer](http://www.intel.com/research/network/e_brewer.htm), who talked about technology to help underdeveloped countries. Since he is a networking guy, the talk focused on low-end networking to support things like remote medical clinics (e.g., time-share the doctor using networking). He also made a
 couple of interesting points about Nicholas Negroponte’s[One Laptop Per Child](http://laptop.org/)(OLPC), $100 computer project. First, he said display prices are the biggest barrier to the $100 price point. Also, he pointed out that villages in underdeveloped countries typically have one cell-phone per village — typically the phone
 is supported by an entrepreneurial resident receiving a “micro loan.” Brewer was much more interested in a computer that was sharable by many children (i.e., one computer per classroom). He used an example of computer-assisted instruction with a mouse per
 student. The software would only advance to the next scene when everybody had the right answer. That way, the fast students have to help the slower ones. In his experience a single-mouse computer just gets monopolized by the best student, and the slower ones
 are left behind. All in all, a very interesting talk.
 
