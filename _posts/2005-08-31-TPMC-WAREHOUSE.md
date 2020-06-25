---
layout: post
title: tpmC per Warehouse in TPC-C
---

As defined in the TPC-C specification, their is an upper bound in the tpmC metric as calculated by the number of warehouses. To get a higher tpmC, vendors have to load more number of warehouses.

The upper bound is not widely known out of the database and benchmarking world. And there are many different variants of TPC-C implementations, scatter in acedemic papers and industrial technical reports. Which have caused much confusion.

The approximiate equation is: 12.86 tpmC per warehouse. Some may have heard of the equation, but without the detailed estimation process. That's why this article is trying to make it more clear.

Assuming the execution time of each transaction is infinite small(aka. zero). And sum of the keying time and thinking time is the lower bound of the response time of a transaction.

For simplicity, assume there are 100 transactions, where 45% is New-order, 43% is Payment, etc. as defined by the TPC-C benchmark. (Refer to the specifiction for the the keying time, thinking time, and the percentage of each transaction.）

The total time is:

30\*45 + 15\*43 + 12\*4 + 7\*4 + 7\*4 = 2,099 seconds

New-order TXs take:

30\*45 = 1,350 seconds

So the percentage of time taken by New-order is:

1,350/2,099 = 0.643,16

So each New-order takes,

2,099/45 = 46.644,44

The numeber of New-order TXs in a minute:

60/46.644,4 = 1.286,32

And a warehouse has 10 terminals,

1.286,32 \* 10 = 12.863,2 tpmC

