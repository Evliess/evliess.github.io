---
layout: index
title: "Java Concurrent"
category: java
date: 2018-03-22 15:17:55
---

### 目前常见的硬件内存架构
![CPU架构](../public/images/java/cpu_structure.png)



### Java内存模型(Java Memory Model, JMM)
Java虚拟机规范中定义了Java内存模型(Java Memory Model，JMM),用于屏蔽掉各种硬件和操作系统的内存访问差异，以实现让Java程序在各种平台下都能达到一致的并发效果，JMM规范了Java虚拟机与计算机内存是如何协同工作的：规定了一个线程如何和何时可以看到由其他线程修改过后的共享变量的值，以及在必须时如何同步的访问共享变量.






