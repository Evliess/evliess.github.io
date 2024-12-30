---
layout: index
title: "Java Common Knowledge"
category: java
date: 2018-02-22 11:03:55
---

# Java common knowledge


## Understanding JVM

> 对象在内存中的结构

- 对象头(Header): 包含HashCode，GC分代年龄，锁状态标志，线程持有的锁，偏向线程ID，偏向时间戳，类型指针（指向类元数据的指针）。

- 实例数据(Instance data)

- 对齐填充(Padding)


> String.intern() 

该方法是一个native方法。它的作用是：如果字符串常量池中已经包含一个等于此String对象的字符串，则返回代表池中这个字符串的String对象；否则，将此String对象包含的字符串添加到字符串常量池中，并且返回此String对象的引用。


## JVM tuning

Tools: Arthas https://arthas.aliyun.com/doc/quick-start.html


## Java secure coding rules

> Injection attacks

> Leaking sensitive data

- Sensitive data
    - Credit Card or Payment Card Industry (PCI) Information
    - Private Personal Information (PPI)
    - Protected Healthy Information (HIPAA)

- Private/Confidential data
- Public data

> Denial-of-service Attacks (DoS)

- Resource-exhaustion attacks
    - Remove temporary files before termination
    - Release resources when they are no longer needed
    - Don't let external processes block on IO buffers
    - Perform proper cleanup at program termination
    - Safely extract files from ZipInputStream
    - Don't use finalizers
    - Don't leak memory
    - Don't exhaust heap space
    - Avoid memory and resource leaks during serialization
    - Use thread pool to enable graceful degradation of service during traffic bursts
    - Don't execute interdependent tasks in a bounded thread pool

- Concurrency-related attacks that include thread deadlock, thread starvation, and race conditions.
    - Use private final lock objects to synchronize classes that may interact with untrusted code.
    - Don't synchronize on objects that may be reused.
    - Avoid deadlock by requesting and releasing locks in the same order
    - Ensure actively held locks are released on exceptional conditions
    - Don't perform operations that can block while holding a lock
    - Avoid client-side locking when using classed that don't commit to their locking strategy
    - Ensure that threads performing blocking operations can be terminated
    - Ensure that tasks submitted to a thread pool are interruptible

- Algorthmic attacks (such as hash function) inject values that force worst-case conditions to exit
- Bandwith-consumption attacks that consume all available network bandwith.
- Prevent XML Injection



## Basic in small words 

> CountDownLatch, Semaphore VS CyclicBarrier 

- CountDownLatch 维护一个原子的计数器，该计数器只能减，不能被重置。通过它可以使当前的线程处于await状态。直到其他的线程调用countDown方法使计数器变为0.此时被阻塞的方法会继续运行。  
- CyclicBarrier 和 CountDownLatch类似，不同之处在于CyclicBarrier的值被获取之后可以释放，并不像CountDownLatch一样只能减。一般用于限制某些资源最多同时被多少个线程访问。  
- CyclicBarrier 和 CountDownLatch类似，只是它的计数器可以被重置。  

> Java semaphores - Control and maintain the number of access the thread.

> Java volatile 实现原理  
缓存一致性协议(MESI)。当CPU在写数据时，如果发现操作的变量是共享变量，则会通知其他的CPU该变量的缓存已经无效。其他的CPU收到该通知后就会再次从主存同步改变量的值到自己的缓存。

> Display curency NumberFormat Class

```java
//Format currency
Locale loc = Locale.UK;
NumberFormat nf = NumberFormat.getCurrencyInstance(loc);
double money = 1_000_000.00d;
nf.format(money);

```

> Deadlock, Livelock, Starvation

- Deaklock: A situation where one thread is blocked while waiting for a conditon in a second thread that is waiting on the first thread
- Livelock: A condition where a thread is locked because of an operation that is continuously failing
- Starvation: A situation where a thread cann't access a resource because higher priority threads are continually accessing the resource


> java.util.concurrent collections

- ConcurrentHashMap -- HashMap
- ConcurrentSkipListMap -- TreeMap
- CopyOnWriteArrayList -- ArrayList

> Deque 类

- As is a double-end Queue  FIFO (add() and remove())
- As is a Stack LIFO (push() and pop())

> Comparable 接口

- 覆写compareTo方法
- 只能包含一种比较策略

> Comparator 接口

- 使用compare来比较
- 可以创建多个Comparator类
- 创建多个排序的选项


> 接口

- 和抽象类类似，只包含public abstract 方法
- 抽象的方法必须被实现
- 可以包含constant 字段
- 可以指向一个引用
- 包含default 方法 （form java8）
- 接口可以extends接口

> default 方法 in 接口

- 被default修饰
- 具有方法体


> static 方法 in 接口

> functional 接口

- 只包含一个抽象方法的接口

> 抽象类

- 抽象类不能被实例化
- 抽象类中的抽象方法不能有方法体
- 抽象类的方法在子类中必须被覆盖
- 抽象类中可以包含非抽象方法
- 当一个类继承一个抽象类的时候，要么这个类也是一个抽象类，要么实现父类中的所有抽象方法

> final 关键字

- 可以修饰类，类不能被继承
- 可以修饰方法， 方法不能被覆盖
- 可以修饰本地变量，变量的引用不能被修改，但是变量的内容可以修改。
- 可以修饰参数

> public, default, protected, private

- public - anywhere
- protected - same class, package and subclass in other package
- default - same class and package
- private - same class

> 如何将一个可变类转化为不可变的类

- 去掉所有的setter方法
- 所有的字段都用 private final 修饰
- 类名用final修饰

> Interface VS Abstract Class

An interface is similar to an abstract class, except that:

- Methods are implicitly abstract(except default methods)
- A class does not extend it, but implements it
- A class may implement more than one interface

All abstract methods from the interface must be implemented by the class.

> Exception VS Error

Exception are subclasses of Throwable. There are three main types of Throwable：

- Error

    - Typically an unrecoverable external error 
    - Unchecked
    
- RuntimeException

    - Typically caused by a programming mistake
    - Unchecked
    
- Exception

    - Recoverable error
    - Checked (Must be caught or thrown)
    

#### Best Practices

- Catch the actual exception thrown, not the superclass type. 
- Examine the exception to find out the exact problem so you can recover cleanly.
- You do not need to catch every exception.

    - A programming mistake should not be handled. It must be fixed.
    - Ask yourself, "Does this exception represent behavior I want the program to recover from?"


> Synchronized原理  

- 修饰普通方法  
通过 ACC_SYNCHRONIZED标识，如果设置该标识，则需要先获取monitor，获取成功才执行方法。方法执行完了释放monitor。  
- 修饰静态方法  
- 修饰代码块  
JVM的两个指令monitorenter和monitorexit. Synchronized的语义底层是通过一个monitor的对象来完成，其实wait/notify等方法也依赖于monitor对象，只有在同步的块或者方法中才能调用wait/notify等方法，否则会抛出java.lang.IllegalMonitorStateException的异常. 

> Java 中的Clone  

克隆对象的时候如果需要深克隆，那么该对象中的引用类型也需要实现Cloneable接口。如果该类中的引用对象没有实现Cloneable接口，那么克隆的对象与原对象拥有相同的引用类型的字段。可以通过序列化的手段实现对象的深度克隆。   

> LinkedHashMap的应用   

可以保证遍历时取数据的顺序和插入顺序是一致的，一般情况下遍历的效率比Hashmap低。当Hashmap的容量很大，实际存放的数据很少时，效率比Hashmap高。  

> Java 8 中基本的数据类型  

byte, char, int, float, double, long ,short, boolean  

> Arrays.sort 与 Collections.sort

- Arrays.sort可以对基本类型的数组进行排序，也可以对实现Comparable接口的集合进行排序。  
     - Arrays可以使用sort方法以及parallelSort方法对集合进行排序。使用DualPivotQuicksort(双轴双向)排序，基本思路是设置两个参考点pivot1，pivot2，其中pivot1一定要小于pivot2，然后将待排序的数组分为4部分 [x<=pivot1], [pivot1<x<pivot2], [x>=pivot2], [未排序]，然后对前三个区间分别递归排序。  
- Collections.sort只能对实现Comparable接口的集合进行排序。  

> Java的类加载机制  

- Bootstrap ClassLoader  
最顶层的加载类，主要加载核心类库，%JRE_HOME%\lib下的rt.jar、resources.jar、charsets.jar和class等。另外需要注意的是可以通过启动jvm时指定-Xbootclasspath和路径来改变Bootstrap ClassLoader的加载目录。通过环境变量sun.boot.class.path查找对应的文件。  
- Extension ClassLoader  
扩展的类加载器，加载目录%JRE_HOME%\lib\ext目录下的jar包和class文件。还可以加载-D java.ext.dirs选项指定的目录。通过环境变量sun.boot.class.path查找对应的文件。通过环境变量java.ext.dirs 查找对应的文件。  
- App ClassLoader  
也称为SystemAppClass 负责加载当前应用的classpath的所有类。 通过环境变量java.ext.dirs 查找对应的文件。  
- Custom ClassLoader

[一看你就懂，超详细java中的ClassLoader详解](https://blog.csdn.net/briblue/article/details/54973413)

> Java加载类的方法  

- 从本地系统直接获得  
- 通过网络下载.class文件  
- 从zip，jar中加载.class文件  
- 从数据库中提取.class文件  
- 将java源码动态编译为.class文件  
- 命令行启动应用的时候由JVM初始化加载  
- 通过Class.forName()  
- 通过ClassLoader.loadClass()  

> Java类什么时候会被初始化  

- new 该类一个实例  
- 访问某个类或者接口的静态变量  
- 调用类的静态方法  
- 反射  
- 初始化子类  
- JVM启动时指明的启动类  


> 强引用，软引用，弱引用，虚引用  

- 强引用的对象如果不为null，那么GC不会去回收此对象  
- 软引用的对象当jvm内存不足的时候，GC会去回收这些对象  
- 弱引用的对象无论jvm是否充足，在GC的时候均会被回收
- 虚引用也称幽灵引用，一个对象是否有虚引用，不会对其生命周期有影响。为一个对象设置一个虚引用关联的唯一目的是希望该对象被收集器回收的时候收到一个系统通知。  


> final, finally与finalize  

- final 修饰类，变量，方法。修饰类的时候，表明这个类不允许被继承，例如String类。修饰方法的时候表示在子类中不允许被覆写，private关键字修饰的方法默认是final的。修饰变量的时候分两种情况，修饰基本类型的时候，表示其数值一旦初始化就不允许修改。修饰引用类型的时候，表示其初始化之后不能再指向另外一个对象。  
- finally 一般与try，catch,finally 做异常处理。但是finally块中的语句不一定会执行。有以下几种情况：  
    - try中的代码还未执行到程序就退出。  
    - try中的代码执行之后，虚拟机停止。例如调用  ```java  System.exit(0); ``` . 
    - 程序执行到try或者catch块的时候，线程被中断或者终止。  

- finalize是Object的方法, 可以在垃圾收集器对此对象进行回收的时候做一些处理工作。  

```java
protected void finalize() throws Throwable { }
```


> HashMap 与 Hashtable  

- HashMap非线程安全的， HashMap的key和value都可以是null。  

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

- Hashtable 是线程安全的，Hashtable的key或者value不能是null。    

```java

    /*
    * 添加元素的时候使用synchronized锁
    * 返回之前对应位置的值，如果不存在，则返回null
    */
    public synchronized V put(K key, V value) {
        // Make sure the value is not null
        if (value == null) {
            throw new NullPointerException();
        }

        // Makes sure the key is not already in the hashtable.
        Entry<?,?> tab[] = table;
        int hash = key.hashCode();
        int index = (hash & 0x7FFFFFFF) % tab.length;
        @SuppressWarnings("unchecked")
        Entry<K,V> entry = (Entry<K,V>)tab[index];
        //遍历整个table，看是否存在
        for(; entry != null ; entry = entry.next) {
            if ((entry.hash == hash) && entry.key.equals(key)) {
                V old = entry.value;
                entry.value = value;
                return old;
            }
        }
        //不存在则插入元素
        addEntry(hash, key, value, index);
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
- equals方法是Object基类的方法，如果子类没有覆写该方法，默认的是比较对象的地址。一般覆写此方法的时候同时覆写hashCode方法。


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

> Thread.sleep()方法和Thread.yield()，Object.wait()方法的相同点和区别？  

- 一个线程调用yield()方法表明这个线程目前没有特别重要的事情做了，如果有其他的线程或者进程需要运行，优先他们运行，如果没有其他的线程或者进程需要运行，那么调用yield()方法的线程就继续运行。  
- 一个线程调用sleep()方法会使该线程休眠指定的时间，在此期间，不会丢失任何监视器(锁)的信息，如果有其他的线程或者进程需要运行，他们可以获得系统资源运行，如果没有，那么CPU有可能处于空闲状态。  
- 当调用wait()方法的时候，线程会丢弃对象锁，进入该对象池的等待池中，当其他的线程调用nofity()或则notifyAll()的时候重新去竞争该对象的锁。  

> notify()方法和notifyAll()方法的区别  

- 锁池：假设线程A已经拥有某个对象的锁，而其他线程想调用该对象的某个synchronized方法，那么必须先获得该对象的锁才可以，由于此时锁已经被线程A占有，所以这些线程会进入该对象的锁池中。  
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




