---
layout: post
title: My experience in database design
modified: 2017-2-7
categories: Database
tags: 
comments: true
---


In this blog, I will focus on the design of database. There is another blog focus on solving the problem caused by deadlock. Please check it here <a href="http://dotnetbuilder.co.nz/database/2017/01/31/database-deadlock.html">Database deadlock</a>.



----------------------------------------------------I am the split line o(^_^)Y------------------------------------------------

The following suggestions are on the condition that the system may need scalability and extensibility in the future.

When you design a database, I suggest that

do not use foreign key, 

do not use auto increment the primary key, 

do not use Stored procedure, 

do not use built-in functions in database, 

do not use non-standard SQL. 

Let the storage system only do the storage jobs. 

This kind of database would be easily to migrate（eg，from mysql to Oracle）, use nosql to instead of or implement key-value cache(redis, memcache) once the system has performance problems.

Some developers use stored procedure to instead of programme. In fact, stored procedure only help to gain high performance in single server environment. Once the storage server need to implement horizontal scaling, the business logics in the stored procedure would make you crazy.

For table's fields, use string to storage as many as possible except number and date. Do not use byte or similar things to save the cheap disk space. Because these things could cost the scalability and extensibility of your system. I suggest to use string to store data except for real-time system or video. The key factors of performance are distributed and horizontal scaling.
