---
layout: post
title: Software Engineering Best Practices - Three Tier Architecture
original: https://blog.csdn.net/zedware/article/details/5415026
---
[http://best-practice-software-engineering.ifs.tuwien.ac.at/architecture.html](http://best-practice-software-engineering.ifs.tuwien.ac.at/architecture.html)
 
![Architecture Overview](http://best-practice-software-engineering.ifs.tuwien.ac.at/images/Architecture_Overview.png)
 
The above illustration shows our sample application. It is divided into the classical three tiers:*Presentation Layer*, which is the interface to the user,*business layer*, which handles the data objects and modifies them and*Data Layer*, which consists of a database that loads and stores data. In our example, the business layer is further divided into: (1) The*service layer*, which provides services to the presentation layer, controls the flow of the application and modifies data objects; and (2) the*data access layer*which deals with the communication to the database.
The illustration also shows that this sample comes in 3 variations:
- **[Basic:](arch-basic.html)** In the basic example, the user access the application with a Swing client. The service layer is built upon the popular Spring Framework. JDBC Templates (also a Spring Feature) are used to access the database.
- **[Medium:](medium/overview-medium.html)** In the second sample application, the user accesses the application through a web frontend that builds on the Java Server Faces technology. The persistence layer uses the popular O/R mapping tool Hibernate.

All samples use the core module, which contains the interfaces and the data objects.
