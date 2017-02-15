---
layout: post
title: Thread
modified: 2017-2-10
categories: [C#, Design pattern]
tags: [Object pool pattern]
comments: true
---


In the system design, object pool is often used to increase system performance. Such as database connection pool, thread pool. If create an instance of an object would cost some time and the instance would be used frequently. Then, use a pool to store some initialized objects ready to use is a better way than create and destroy the object every time. For example, initialize a database connection object is relatively slow, when creating connection from client to database server. If create database connection each time when doing some database operation, the performance of the system would be very poor.

On the other hand, it is a way of space for time. Using some initialized objects in memory ready to use. Compare to the increase of performance, the cost in the space of memory is worthy.

<!-- more -->






