---
layout: post
title: Re-factoring some internals of prepared statements in 5.7
original: https://blog.csdn.net/zedware/article/details/51100701
---
MySQL的prepare stmt做得确实有很多缺陷，导致很多业务都不去用它。典型的问题例如吃内存、效率不高等。偶遇这篇介绍实现改进细节的博客，写得比较详细，有助于理解代码。建议直接访问原文并查看下面的评论。链接：http://mysqlserverteam.com/re-factoring-some-internals-of-prepared-statements-in-5-7/

#Re-factoring some internals of prepared statements in 5.7
[May
 15, 2014](http://mysqlserverteam.com/re-factoring-some-internals-of-prepared-statements-in-5-7/ "Permalink to Re-factoring some internals of prepared statements in 5.7")[Optimizer](http://mysqlserverteam.com/category/performance/optimizer/), [Performance](http://mysqlserverteam.com/category/performance/), [Prepared
 Statements](http://mysqlserverteam.com/category/prepared-statements/), [Uncategorized](http://mysqlserverteam.com/category/uncategorized/)[Guilhem
 Bichot](http://mysqlserverteam.com/author/guilhem/ "View all posts by Guilhem Bichot")When the MySQL server receives a SELECT query, the query goes through several consecutive phases:
- parsing: SQL words are recognized, the query is split into different parts following the SQL grammar rules: a list of selected expressions, a list of tables to read, a WHERE condition, …
- resolution: the output of the parsing stage contains names of columns and names of tables. Resolution is about making sense out of this. For example, in “`WHERE foo=3`“,
 “foo” is a column name without a table name; by applying SQL name resolution rules, we discover the table who contains “foo” (it can be complicated if subqueries or outer joins are involved).
- optimization: finding the best way to read tables: the best order of tables, and for each table, the best way to access it (index lookup, index scan, …). The output is the so-called “plan”.
- execution: we read tables as dictated by the plan above, and send output rows to the client.

This design hasn’t changed since many many years. Originally, MySQL didn’t have prepared statements. So it took a query, passed it through the phases above, and then threw it away. This still happens nowadays when using non-prepared statements.
But, with a prepared statement, come more requirements. MySQL has to be able to execute the query a second time (and a third time, and so on, at the user’s will). No matter the used API (the [C
 API](http://dev.mysql.com/doc/refman/5.7/en/c-api-prepared-statement-function-overview.html) or the [SQL API](http://dev.mysql.com/doc/refman/5.7/en/sql-syntax-prepared-statements.html)), this usually looks like the following dialogue:
12345678# Assume that "t" contains 3 rows with values 1, 2, 98 in a columnuser:PREPAREstmtFROM'SELECT * FROM t';MySQL:okayuser:EXECUTEstmt;MYSQL:1,2,98user:INSERTINTOtVALUES(0);user:EXECUTEstmt;MYSQL:1,2,98,0SQL mandates that syntax and semantic errors be reported by PREPARE, without postponing them to EXECUTE. So, PREPARE needs to do at least parsing and resolution, to spot any such error.
Then there is an implementation question: when it receives `"EXECUTE stmt"`, how can MySQL remember what “stmt” is?
The simplest, most obvious solution would be, during PREPARE, to simply store `"stmt" => "SELECT * FROM t"` in some session-local key/value dictionary. When `"EXECUTE
 stmt"`comes, MySQL would find the query string in the dictionary, and go through all stages (parsing, resolution, optimization, execution). And when the next `"EXECUTE stmt"` comes, it would
 do the same again.
Of course, that would not be efficient. At least parsing and resolution theoretically don’t need to be re-done every time: query’s text doesn’t change, so only one parsing is necessary; the structure of tables (names and type of columns) doesn’t change, so
 only one resolution is necessary [1](http://mysqlserverteam.com/re-factoring-some-internals-of-prepared-statements-in-5-7/#footnote).
 So, what PREPARE actually stores in the key/value dictionary, is `"stmt" => tree`, where “tree” is the output of the resolution phase; it’s an in-memory tree of C++ objects, each object representing
 an expression, a column of a table, a table, a left join, a boolean condition, …
With this design, only optimization and execution are repeated by each `"EXECUTE stmt"` [2](http://mysqlserverteam.com/re-factoring-some-internals-of-prepared-statements-in-5-7/#footnote).
 Repeating optimization is sometimes a smart thing to do; imagine that between the first and second EXECUTE, the table’s data distribution has significantly changed (perhaps due to other clients), then the plan of the first EXECUTE is maybe not the best one
 anymore, and searching for a new plan makes sense. In other cases, repeating optimization is a waste, because the old plan would be good enough. As of today, MySQL always repeats optimization; making this more flexible would be an interesting enhancement for
 the future, but today’s situation is: we repeat optimization.
Optimization does everything it can to speed up the query. Imagine the query references 10 tables in its FROM clause. To find the best order to access those 10 tables, we need to consider 10! possible orders, roughly 3,000,000. It takes time to consider that
 many plans.  The optimizer has techniques to cut this down, let me show one. If one table is referenced like this in the WHERE clause:
`WHERE t.pk=39 AND ...`
then, because “t.pk” is a primary key, I know that at most one row of this table will participate in the result, and it makes sense to read it with a primary key lookup. And this read has to be done only once in this execution. So let’s put “t” first in the
 plan, unconditionally. This technique, known as “constant tables”, divides the number of possible orders by a factor of 10 in our example (only 9 tables left to order). Now, imagine that the WHERE clause looks like this:
`WHERE t.pk=39 AND t.c1>t2.c2`
As soon as I read the row of “t” with a primary key lookup (looking up 39), I get access to the value of t.c1; say it is 100. My condition can thus be simplified to:
`WHERE 100>t2.c2`
Notice how the AND has disappeared.
The inequality predicate “100>t2.c2” is interesting: assuming that t2.c2 is indexed, it means that a range scan is a possibility for reading t2.
This little example was meant to demonstrate that MySQL, in the optimization phase, does data-based transformations to the query: transformations which depend on the content of tables, and which apply for one single execution; indeed, when the next execution
 comes, it may be that the row of “t” with t.pk=39 now has t.c1=1200, so our condition simplifications don’t hold anymore. Another way to say this, is: data-based transformations must be non-permanent.
So the AND operator which was killed above during the optimization of the first EXECUTE, must be resurrected for the optimization of the second EXECUTE.
To achieve this, at the end of resolution we rename tree to permanent_tree, then we make a copy of it, which we name tree. The optimization phase has access to tree, and
 no access to permanent_tree. The optimization operates on tree, does data-based transformations on it. When the second EXECUTE starts, permanent_tree is fetched, copied into a new tree,
 on which the optimization operates. For example, the AND operator always exists inpermanent_tree.
What about permanent transformations? For example, consider this query:
`SELECT * FROM t1 JOIN (t2 JOIN t3 ON t2.a=t3.a) ON t2.b+t3.b>t1.b

WHERE t1.b*2>15;`
The result of this query is defined by the SQL standard as: get the result of the most inner join (t2,t3), filter with t2.a=t3.a, then join the result with t1, filter with t2.b+t3.b>t1.b, then filter with t1.b*2>15. With this definition, the order of tables’
 reading and the order of application of conditions are constrained. For example, reading t2 then t1 then t3 is not possible. But if we notice that the query is equivalent to:
`SELECT * FROM t1, t2, t3 WHERE t2.a=t3.a AND t2.b+t3.b>t1.b

AND t1.b*2>15;`
then we have 3! = 6 possible orders for tables. More plans to examine, but more potential for finding a good plan – it is very possible that the best plan, the one yielding the fastest execution, is not among the ones suggested by the SQL standard’s definition.
The equivalence between both queries is semantics-based, not data-based. Thus, the transformation from the first to the second query can be permanent: it can be done once for all, not at every EXECUTE.
Permanent transformations include:
- JOIN-to-WHERE, like in the example above
- outer-join-to-inner-join, when there is something in the WHERE clause which allows to deduce that NULL-complemented rows will actually not participate in the result
- semi-join, more or less merging an “IN (subquery)” predicate into the parent query
- IN-to-EXISTS, rewriting “x IN (subquery)” to “EXISTS (modified subquery)”.

After giving all this background (phew…), I’m now ready to explain one re-factoring which I did in MySQL 5.7.4. The situation of permanent transformations in MySQL 5.6 is the following:
1. IN-to-EXISTS : done in the resolution phase.
2. semi-join, JOIN-to-WHERE, outer-join-to-inner-join: done in the optimization phase.

Doing permanent transformations during optimization (step (2) above) is surprising, as optimization is re-done at every EXECUTE. Fortunately, we can internally know if an optimization is the first or not; if it’s not, we skip step (2).
So in the end, efficiency is guaranteed – permanent transformations are not re-done. Though this design agreeably looks strange.
Now, putting pieces together, on the way to more strangeness: you remember that after the resolution phase, we produce permanent_tree and save it for all future EXECUTEs. It thus contains permanent transformations done in resolution, good.
 But, it does not contain those done in optimization (semijoin …), as optimization runs afterpermanent_tree has been produced. Still we do want semi-join and friends to be reused by all future EXECUTEs, so they must be put in permanent_tree!
 So we get an even more strange design:
1. resolution1. do some permanent transformations (IN-to-EXISTS) on tree
2. rename tree to permanent_tree
3. copy permanent_tree to a new tree (for optimization to do nonpermanent transformations on it)


4. optimization1. if first optimization:1. do some more permanent transformations (semijoin, etc) on tree
2. throw permanent_tree away
3. copy tree to a new permanent_tree


4. do nonpermanent transformations on tree


5. execution1. read tables and send rows
2. throw tree away



This has a few nasty effects:
- permanent transformations are scattered: the boundary between phases is blurred, which is a code readability problem, and an impediment for future improvements in the Optimizer in general
- efficiency loss: copying a tree in resolution phase takes some time and memory; in the first optimization we throw permanent_tree away and do the copying again… Bad.
- real bugs. Yes, because what the resolution phase thinks of permanent_tree is not true anymore: this object has been deleted and replaced by another one, in the first optimization… so the next EXECUTE gets confused…

In MySQL 5.7.4, I have moved all permanent transformations to where they belong, so now we have the more straightforward design:
1. resolution1. do all permanent transformations on tree
2. rename tree to permanent_tree


3. optimization1. copy permanent_tree to a new tree (for optimization to do nonpermanent transformations on it)
2. do nonpermanent transformations on tree


3. execution1. read tables and send rows
2. throw tree away



If we had done this re-factoring earlier, we would have saved us some headaches. But, better late than never. It’s at least comforting that nowadays we have time to do re-factoring, even if it means spending several man-months on a task like that. It really
 took that much time: the idea may look simple, but the devil was in the details as usual, and if you add, on top of coding time, review time by two reviewers, and QA time to verify that I didn’t break anything… But in my team we are convinced that this is
 a long-term investment which will pay. Moreover, when we do such re-factoring work, it gives us the occasion to remove the little hacks which accumulated over time to work around the root problem (which the re-factoring finally addresses). And that leads to
 even more code simplification.
There are a few more details in [the Worklog page](http://dev.mysql.com/worklog/task/?id=7082) if you are interested (note the multiple tabs there).
That’s all for today. If you reached this line, congratulations ![:-)](http://mysqlserverteam.com/wp-includes/images/smilies/simple-smile.png)
Footnotes:
1. I hear you say: “what if an ALTER TABLE is done between PREPARE and EXECUTE?! This may change names and types of columns!”. Yes, you are right, but there exists, somewhere in the prepared statement subsystem, a detector for this; it
 runs when EXECUTE starts, and if it spots a table structure change since the statement was prepared, it throws away the prepared statement (as “out-of-date”), silently re-prepares it and then docks into EXECUTE; the user does not notice, except if she looks
 at com_stmt_reprepare in `SHOW STATUS`. This detector is external to the Optimizer and runs before it; so if the Optimizer is involved in EXECUTE, it can safely assume that nothing has changed
 since PREPARE.
2. Ok, a part of resolution is repeated too. It would be good to avoid that, in the future.



