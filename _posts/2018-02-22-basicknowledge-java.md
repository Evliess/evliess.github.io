---
layout: index
title: "Java基础知识"
category: java
date: 2018-02-22 11:03:55
---

## Java.Core  
### Q1：Java中equals()方法与"=="的区别 
A1:equals比较的是字符串字面值是否相等，"=="比较是的对象的地址是否相等
举例：  
```java
	String a = "abc";
    String b = "abc";
    String c = new String("abc");
    System.out.println(a.equals(b));//true
    System.out.println(a==b);//true
    System.out.println(a.equals(c));//true
    System.out.println(a==c);//false
```



