---
layout: post
title: SQL Rules The World
tags: sql
---

CIDR 2022 has a paper titled "Machine Learning, Linear Algebra, and More: Is SQL
All You Need?"<sup><a id="fnr.1" class="footref" href="#fn.1">1</a></sup>, which is interesting.  SQL is a declarative language, and
with procedural extensions like PL/SQL, we can write programs just like other
languages. Nevertheless, it is hard to write programs in plain SQL without constructs like 'if else' and 'for' found in PL/SQL. Especially for those algorithms 
used in Machine Learning. Writing it in a single SQL statement looks impossible. 
The authors show examples and claim that using SQL for algorithms can make 
computations close to the data, require minimal user permissions, and increase
portability. However, the performance heavily depends on the DBMS, and Hyper even 
outperforms NumPy in some cases.

It is common to translate a program written in one language into another one.
For example, Facebook is famous for translating PHP into C++. And Ispirer can
do more translations<sup><a id="fnr.2" class="footref" href="#fn.2">2</a></sup>, including Oracle PL/SQL to Java. But converting 
from high-level programming languages, like Python, Java into PL/SQL is 
less known. Converting them into SQL sounds even more strange. But there
are some research papers including this SIGMOD 2021 paper "One WITH RECURSIVE 
is worth many GOTOs"<sup><a id="fnr.3" class="footref" href="#fn.3">3</a></sup>. We can find the details in these papers.

They map algorithmic primitives of procedural languages to SQL, that is, from 
Python to SQL. Examples include Variables, Functions, Conditions, Loops, and
even Errors. The most complex algorithm written in pure SQL is the gradient descent-based logistic
regression algorithm.

The Python code:

![img](/images/lr-python.png)

The SQL code avoid joins:

![img](/images/lr-sql.png)

The conversions rely heavily on the SQL clauses WITH and WITH RECURSIVE.
Maybe soon, writing algorithms in pure SQL will no longer be a
theoretical gimmick and be widely adopted by SQL programmers.


# Footnotes

<sup><a id="fn.1" href="#fnr.1">1</a></sup> <http://cidrdb.org/cidr2022/papers/p17-blacher.pdf>

<sup><a id="fn.2" href="#fnr.2">2</a></sup> <https://www.ispirer.com/application-conversion>

<sup><a id="fn.3" href="#fnr.3">2</a></sup> <https://db.inf.uni-tuebingen.de/staticfiles/publications/with-recursive-goto.pdf>


