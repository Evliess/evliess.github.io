---
layout: index
title: "Java基础知识"
category: java
date: 2018-02-22 11:03:55
---

### Java.Core  

**1. Java中equals()方法与"=="的区别?**  
equals()比较字符串字面值是否相等，"=="比较对象的地址是否相等  
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
**2. Java抽象类可以有实体方法么？**  
可以.  
**3. 列举Java中抽象类和接口的相同点？**  
- 都是上层的抽象  
- 都可以包含抽象方法  
- 都不能被实例化  

**4. 列举Java中抽象类和接口的不同点？**  
- 抽象类中可以包含抽象方法和实体方法，接口中只能包含抽象方法  
- 一个类只能继承一个抽象类，但是可以实现多个接口  
- 如果一个类不能实现父类中所有的抽象方法，那么它本身必须是抽象类  
- Java接口中声明的变量默认都是final的，抽象类可以包含非final的变量  
- Java接口中的成员函数默认是public的，抽象类的成员函数可以是private，protected或者是public  

**5. HashMap与Hashtable的不同点？**  
- HashMap的key和value都可以为null，Hashtable的key不可以为null  

**6. ArrayList和Linkedlist的区别？**  
- ArrayList查找速度快，Linked在列表中插入，删除操作速度快
- ArrayList占据的空间更少  

**7. Comparable和Comparator接口是干什么的？**  
- Comparable接口只提供一个compareTo方法  
- Comparator接口提供compare（）和equals（）两个方法  

**8. 数据库左连接和右连接的区别？**  
```
//左连接后的检索结果是显示t1的所有数据和t2中满足where条件的数据
select * from t1 left join t2 where t1.id = t2.sid;
```



