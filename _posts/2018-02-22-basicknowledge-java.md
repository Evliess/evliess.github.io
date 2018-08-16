---
layout: index
title: "Java基础知识"
category: java
date: 2018-02-22 11:03:55
---

# Java basic knowledge

> Java中equals()方法与"=="的区别?  

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
> Java抽象类可以有实体方法么？  

可以.  
> 列举Java中抽象类和接口的相同点？  

- 都是上层的抽象  
- 都可以包含抽象方法  
- 都不能被实例化  

> 列举Java中抽象类和接口的不同点？  

- 抽象类中可以包含抽象方法和实体方法，接口中只能包含抽象方法, 从jdk1.8开始，接口中也可以包含static和default方法  

```java
public interface MyImpl {

	/**
	 * by default, abstract
	 */
	public void move();

	public abstract void drink();

	/**
	 * From jdk1.8
	 */
	public static void say() {
	}

	/**
	 * From jdk1.8
	 */
	public default void eat() {
	}
}

```

- 一个类只能继承一个抽象类，但是可以实现多个接口  
- 如果一个类不能实现父类中所有的抽象方法，那么它本身必须是抽象类  
- Java接口中声明的变量默认都是final的，抽象类可以包含非final的变量  
- Java接口中的成员函数默认是public的，抽象类的成员函数可以是private，protected或者是public  

> HashMap与Hashtable的不同点？  

- HashMap的key和value都可以为null，Hashtable的key不可以为null  

> ArrayList和Linkedlist的区别？  

- ArrayList查找速度快，Linked在列表中插入，删除操作速度快
- ArrayList占据的空间更少  

> Comparable和Comparator接口是干什么的？  

- Comparable接口只提供一个compareTo方法  
- Comparator接口提供compare（）和equals（）两个方法  

> Thread.sleep()方法和Thread.yield()方法的相同点和区别？  

- 一个线程调用yield()方法表明这个线程目前没有特别重要的事情做了，如果有其他的线程或者进程需要运行，优先他们运行，如果没有其他的线程或者进程需要运行，那么调用yield()方法的线程就继续运行。  
- 一个线程调用sleep()方法会使该线程休眠指定的时间，在此期间，不会丢失任何监视器(锁)的信息，如果有其他的线程或者进程需要运行，他们可以获得系统资源运行，如果没有，那么CPU有可能处于空闲状态。  

> notify()方法和notifyAll()方法的区别  

- 锁池：假设线程A已经拥有某个对象的锁，而其他线程想调用该对象的某个synchronized方法，那么必须先获得该对象的锁可以，由于此时锁已经被线程A占有，所以这些线程会进入该对象的锁池中。  
- 等待池: 假设线程A调用某个对象的wait()方法，线程A就会释放该对象的锁，进入对象的等待池。等待池中的线程不会参与竞争该对象的锁。  

notify()方法只会唤醒一个线程进入锁池，假如有多个线程都在等待获取某个对象的锁，那么操作系统会根据实现挑选一个线程来唤醒。而notifyAll()会唤醒所有的等待线程进入锁池，这些线程之间彼此竞争去获取对象上的锁。notify()方法会导致死锁，而notifyAll()方法不会。

```java

public void productFood() {
	synchronized(this.lock) {
		while(this.foodTotal == 5) {
			try {
				this.lock.wait();
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
		}
		/*
		* 此时所有的线程都处于等待状态，如果现在foodTotal = 4，
		* 执行过下一句后foodTotal=5,如果使用notify方法正好唤醒的是一个线程调用productFood()方法，那么该线程就会进入wait()状态。此时没有其他的线程是running状态，可以调用nofify()方法唤醒其他线程，于是就发生了死锁。
		*/
		foodTotal = foodTotal + 1;
		
		//this.lock.notifyAll();
		this.lock.notify();
	}
}

```

> 连续调用多次Thread的start()方法会怎么样？  

会抛出Thread的状态异常  

```java
 public synchronized void start() {
 	/**
 	*A zero status value corresponds to state "NEW". 新创建的Thread的status==0
 	*/
 	if (threadStatus != 0)
            throw new IllegalThreadStateException();
    ...
 }

```

> 二分查找算法

```java

private int binarySearch(int arr[], int start, int end, int target) {
    int middle = (start + end) / 2;   //算middle的位置一定要是(start + end)/2 , 想想为什么不能是end/2?
    if (target == arr[middle]) {
      return middle;
    }
    //middle >=1 保证end的最小值是0
    if (target < arr[middle] && middle >= 1) {
      return binarySearch(arr, 0, middle - 1, target);
      //middle + 1 <=end 保证起始位置应该<=结束位置
    } else if (middle + 1 <= end) {
      return binarySearch(arr, middle + 1, end, target);
    } else {
      return -1;
    }
  }

```

> 快速排序算法

```java


```

#### 参考链接  

[参考链接1](https://www.zhihu.com/question/37601861/answer/145545371)




