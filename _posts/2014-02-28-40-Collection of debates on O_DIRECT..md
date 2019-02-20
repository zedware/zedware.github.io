---
layout: post
title: Collection of debates on O_DIRECT.
original: https://blog.csdn.net/zedware/article/details/20161041
---
有空看看各种不同角色的讨论，还是有点意思的。先从Linus的这个开始：[O_DIRECT
 (Larry McVoy; Linus Torvalds) - Yarchive](http://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&ved=0CCcQFjAA&url=http%3A%2F%2Fyarchive.net%2Fcomp%2Flinux%2Fo_direct.html&ei=-pwQU7X5FNGZiQe63IHACQ&usg=AFQjCNHI2Dwdmqp-Bu3fOlerTUW8mvPdJQ&sig2=CyXL_SS_pJyDA-eLt4olwQ&bvm=bv.62286460,d.aGc)
注意，这个帖子有点长，而且时间跨度有点大。要有点耐心才能看完。
早些年开始学DBMS的人都知道为什么要用 O_DIRECT，事实上也有很多的系统就是这样做的。当初的文件系统其实很弱，很多方面反而是DBMS先搞出来的，二者在发展过程中不断互相借鉴，互相适配，但又远远没有达到配合默契的程度。在事务处理/日志恢复，缓冲区算法这些技术方面，DBMS的理论和实践都要略胜一筹。为了在磁盘设备上做出比较高性能的数据库，早期的DBMS厂家费劲了心思，要求文件系统提供O_DIRECT这样的接口也是可以理解的。只不过闭源软件没有兴趣把接口定义得那么好，也不全面考虑DBMS之外的通用性。造成既成事实之后，也没有哪家文件系统有兴趣来扭转这种局面，更何况开源的文件系统又比较多，要让DBMS少做点额外的工作，靠文件系统就很难得到一致的性能表现。开源的DBMS中，PostgreSQL是比较喜欢依靠文件系统的，可是遇到了一些麻烦：http://lwn.net/Articles/580542/。其中最典型的问题当属该文章链接的这个：http://www.postgresql.org/message-id/17515.1389715715@sss.pgh.pa.us。
O_DIRECT是如此的混乱，除了上面的长篇大论之外，这里还有了个专门的讨论页面：[Clarifying
 Direct IO's Semantics - Ext4](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&sqi=2&ved=0CCUQFjAA&url=https%3A%2F%2Fext4.wiki.kernel.org%2Findex.php%2FClarifying_Direct_IO%27s_Semantics&ei=nqAQU-iBG477iQfq14DICw&usg=AFQjCNFe1C3k1e1UvuJykIwrtE-1V8yaXg&sig2=SZVzkpcra2LTSnBfQvSwwQ&bvm=bv.62286460,d.aGc)，而数据中也是参数成堆，令人头大不已。例如开源数据库MySQL引入了各种不同的innodb_flush_method参数取值：https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html。对着一大堆参数来调优，对于DBA来说实在不是什么好差事，系统应该稍微智能一点，自己去选择合适的参数。
