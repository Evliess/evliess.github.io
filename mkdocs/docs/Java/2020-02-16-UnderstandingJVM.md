---
layout: index
title: "UnderstandingJVM"
category: java
date: 2020-02-16 11:03:55
---

# Understanding JVM - version 2


## Chapter2 Java内存区域与内存溢出异常

> 对象的创建

1\. 当虚拟机遇到一条new指令时，首先检查这个指令的参数是否能在常量池中定位到一个类符号的引用，并检查这个类是否被加载，解析，初始化。如果没有，必须先执行类的加载。

2\. 当类加载检查通过后，虚拟机为新的对象分配内存。对象所需要的内存大小在类加载完成之后便可以确定。

3\. 内存分配完成之后，将分配的内存初始化零值，除了对象头的部分。

4\. 虚拟机设置对象，标记这个对象是哪个类的实例，如何找到类的元数据信息，对象的hashcode，对象的GC年龄等信息。

> 对象在内存中的结构

- 对象头(Header): 包含HashCode，GC分代年龄，锁状态标志，线程持有的锁，偏向线程ID，偏向时间戳，类型指针（指向类元数据的指针）。

- 实例数据(Instance data)

- 对齐填充(Padding)


> String.intern() 

该方法是一个native方法。它的作用是：如果字符串常量池中已经包含一个等于此String对象的字符串，则返回代表池中这个字符串的String对象；否则，将此String对象包含的字符串添加到字符串常量池中，并且返回此String对象的引用。

> 对象的访问方式

Java程序需要通过栈上的reference数据来操作对上的具体对象。

- 句柄：在Java的对上化分一块内存作为句柄池，reference中存储的就是对象的句柄地址，句柄中包含了对象的实例数据与类型数据各自的具体地址信息。优点是在对象被移动的时候只会改变句柄中的实例数据的指针，reference本身不需要要修改。

- 指针：reference中存储的就是对象的地址。优点是访问速度快。

## Chapter3 垃圾收集器与内存分配策略



