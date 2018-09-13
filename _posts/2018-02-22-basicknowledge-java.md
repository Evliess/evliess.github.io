---
layout: index
title: "Java基础知识"
category: java
date: 2018-02-22 11:03:55
---

# Java basic knowledge

> HashMap 与 HashTable  

- HashMap非线程安全的  

```java

/*
Parameters:
    hash hash for key
    key the key
    value the value to put
    onlyIfAbsent if true, don't change existing value
    evict if false, the table is in creation mode.
Returns:
    previous value, or null if none
*/
final V putVal(int hash, K key, V value, boolean onlyIfAbsent,boolean evict) {
    Node<K,V>[] tab; Node<K,V> p; int n, i;
    // 如果table为空，或者还没有元素时，则扩容
    if ((tab = table) == null || (n = tab.length) == 0)
        n = (tab = resize()).length;
    // 如果首结点值为空，则创建一个新的首结点。
    // 注意：(n - 1) & hash才是真正的hash值，也就是存储在table位置的index。在1.6中是封装成indexFor函数。
    if ((p = tab[i = (n - 1) & hash]) == null)
        tab[i] = newNode(hash, key, value, null);
    else {    // 到这儿了，就说明碰撞了，那么就要开始处理碰撞。
            Node<K,V> e; K k;
            // 如果在首结点与我们待插入的元素有相同的hash和key值，则先记录。
            if (p.hash == hash && ((k = p.key) == key || (key != null && key.equals(k))))
                e = p;
            else if (p instanceof TreeNode) // 如果首结点的类型是红黑树类型，则按照红黑树方法添加该元素
                e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
            else {  // 到这一步，说明首结点类型为链表类型。
                    for (int binCount = 0; ; ++binCount) {
                        // 如果遍历到末尾时，先在尾部追加该元素结点。
                        if ((e = p.next) == null) {
                            p.next = newNode(hash, key, value, null);
                            // 当遍历的结点数目大于8时，则采取树化结构。
                            if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                                treeifyBin(tab, hash);
                                break;
                        }
                        // 如果找到与我们待插入的元素具有相同的hash和key值的结点，则停止遍历。此时e已经记录了该结点
                        if (e.hash == hash && ((k = e.key) == key || (key != null && key.equals(k))))
                            break;
                        p = e;
                    }
                }
            // 表明，记录到具有相同元素的结点
            if (e != null) { // existing mapping for key
                V oldValue = e.value;
                if (!onlyIfAbsent || oldValue == null) //判断是否用新值覆盖旧值
                    e.value = value;
                afterNodeAccess(e);  // 这个是空函数，可以由用户根据需要覆盖
                return oldValue;
            }
        }
    ++modCount;
    // 当结点数+1大于threshold时，则进行扩容
    if (++size > threshold)
        resize();
    afterNodeInsertion(evict); // 这个是空函数，可以由用户根据需要覆盖
    return null;
}

```

> List与Set的区别  

- List中的元素是有序的，可以通过index准确的查找内部的元素。  
- List中的元素可以重复，Set中的元素不能重复。List中可以允许添加多个null元素。Set中至多可以有一个null元素。      
- List提供一个特殊的迭代ListIterator，可以插入，修改，以及提供双向遍历的能力。  

> HashSet是如何保证不重复的  

- HashSet用HashMap存储元素  
- 将元素中作为HashMap的key  
- 不是线程安全的  

```java
// Dummy value to associate with an Object in the backing Map
private static final Object PRESENT = new Object();

public boolean add(E e) {
    return map.put(e, PRESENT)==null;
}

```

> Use condition in ant script  

```xml
<!-- Execute task by different operation system -->
<project name="My Project" default="package_designer_without_report_v3" basedir=".">

	<condition property="isWindows">
		<os family="windows" />
	</condition>

	<condition property="isLinux">
		<os family="unix" />
	</condition>

	<target name="operateInWindows" if="isWindows">
		<echo message="operate in windows" />
	</target>

	<target name="operateInLinux" if="isLinux">
		<echo message="operate in linux" />
	</target>

</project>
```

> Java中equals()方法与"=="的区别?  

- "==" 对于引用类型，比较的是对象的地址，对于基本类型，比较的是字面值。  
- equals方法是Object基类的方法，如果子类没有复写该方法，默认的是比较对象的地址。一般复写此方法的时候同时复写hashCode方法。


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

#### 参考链接  

[参考链接1](https://www.zhihu.com/question/37601861/answer/145545371)




