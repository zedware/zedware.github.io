---
layout: post
title: Disk Corruption以及它们对DBMS的影响
original: https://blog.csdn.net/zedware/article/details/7269074
---

众所周知，软硬件都有不靠谱的时候。可问题是一般人很难获取这些系统在实际使用中的故障率。作为这些产品的生厂商，一般都有一些方法来获取实际安装部署以及运行的情况，自然的，它们也掌握了很多实际的故障数据（不过它们未必会如实公开）。对于那些故障率不太高的情形，只有在安装部署的数量特别大时，才可能捕获到较大的故障出现次数。An Analysis of Data Corruption in the Storage Stack分析了存储系统中的Data Corruption：
An important threat to reliable storage of data is silent data corruption. In order to develop suitable protection mechanisms against data corruption, it is essential to understand its characteristics. In this paper, we present the first large-scale study
 of data corruption. We analyze corruption instances recorded in production storage systems containing a total of 1.53 million disk drives, over a period of 41 months. We study three classes of corruption: checksum mismatches, identity discrepancies, and parity inconsistencies.
 We focus on checksum mismatches since they occur the most. We find more than 400,000 instances of checksum mismatches over the 41-month period. We find many interesting trends among these instances including: (i) nearline disks (and their adapters) develop
 checksum mismatches an order of magnitude more often than enterprise class disk drives, (ii) checksum mismatches within the same disk are not independent events and they show high spatial and temporal locality, and (iii) checksum mismatches across different
 disks in the same storage system are not independent. We use our observations to derive lessons for corruption-proof system design.

估计是受其启发，Impact of Disk Corruption on Open-Source DBMS分析了DBMS中的类似情况。不过它选择的是Open Source的DBMS，对实际开发的参考有限，毕竟主流的企业级业务很少有人跑在这些DBMS上。
Abstract—Despite the best intentions of disk and RAID manufacturers, on-disk data can still become corrupted. In this paper, we examine the effects of corruption on database management systems. Through injecting faults into the MySQL DBMS, we find that in
 certain cases, corruption can greatly harm the system, leading to untimely crashes, data loss, or even incorrect results. Overall, of 145 injected faults, 110 lead to serious problems. More detailed observations point us to three deficiencies: MySQL does not
 have the capability to detect some corruptions due to lack of redundant information, does not isolate corrupted data from valid data, and has inconsistent reactions to similar corruption
scenarios. To detect and repair corruption, a DBMS is typically equipped with an offline checker. Unfortunately, theMySQL offline checker is not comprehensive in the checks it performs, misdiagnosing many corruption scenarios and missing others. Sometimes the checker
 itself crashes; more ominously, its incorrect checking can lead to incorrect repairs. Overall, we find that the checker does not behave correctly in 18 of 145 injected corruptions, and thus can leave the DBMS vulnerable to the problems described above.

真正关键的数据，仅仅依靠单一的某层软件或硬件都不够。
