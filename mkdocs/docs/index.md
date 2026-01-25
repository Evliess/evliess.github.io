# Welcome!

## LeetCode
- https://www.processon.com/mindmap/688dc2a345c93924b095cb88

## 堆插入和删除操作

```
# 堆是一个完全二叉树，一般用数组存放， 1开始作为起始下标。对于下标是i的元素，此时满足以下性质：
其父节点是 i/2，左子节点的下标是 2i，右子节点的下标是2i+1

#以大顶堆为例：
50，30，20，15，10，8，16
# 插入
将待插入的元素放到数组末尾i位置，依次和父节点（i/2）自顶向上做比较，交换，直至父节点大于等于子节点
# 删除
删除堆顶元素，然后将数组的末尾元素（i）放到堆顶的位置，然后比较子节点（2i，2i+1），将最大的那个和父节点比较，如果大于父节点，则交换，直至父节点大于等于子节点
```


## 线上Full GC 频繁

### 场景1 内存泄漏
1. 生产环境启动gc打印日志
```
-Xlog:gc*:file=gc.log:time,uptime,level,tags:filecount=10,filesize=100m
```
2. 使用jmap dump内存快照
```
jmap -dump:live,format=b,file=heap_dump.hprof <pid>

```
2.1 使用Jstat实时诊断

```
jstat -gc <pid> 1s
```
3. 使用Eclipse MAT 快照分析

3.1 查看 Leak Suspects

3.2 使用Dominator Tree 查看大对象，以及所引用的对象

3.3 常见的可疑点 *静态集合类， ThreadLoacl 局部变量，数据库连接，网络连接，文件流未关闭， 监听回调没有取消* 如果是```ThreadLocal```，确保在```try-finally```块中使用，并在```finally```中调用 ```threadLocal.remove()```

3.4 使用自动死锁检测报告 Deadlock Suspects, 从线程视图分析 重点关注状态为 BLOCKED 或 WAITING 的线程

### 场景2 堆内存分配小
症状：GC日志显示每次Full GC回收效果很好，但很快又满了。
解决：调整JVM堆参数。
```
-Xms4g -Xmx4g -Xmn2g
```
### 场景3 元空间溢出
症状：Metadata GC Threshold 触发的Full GC。

解决：
```
-XX:MaxMetaspaceSize=256m

```

### G1 常用参数

```
#堆内存，示例设置最大最小值为4g，对于G1，一般建议2g以上。注意设定Xms=Xmx，防止发生扩容、缩容
-Xms4g -Xmx4g 

#配置元空间初始256m、最大256m。不配置的话，元空间会不受限地占用物理机内存
-XX:MetaspaceSize=256m  -XX:MaxMetaspaceSize=256m 

#设置最大暂停时间，默认200ms。
-XX:MaxGCPauseMillis=100

#指定Region大小，必须是2次幂，最大是32m，具体取值有1MB、2MB、4MB、8MB、16MB、32MB。不声明时，Region大小等于堆大小除以2048
-XX:G1HeapRegionSize=2m

#设置新生代大小，默认5%，默认最大60%。
-XX:G1NewSizePercent=5 -XX:G1MaxNewSizePercent=60

#可以生成更详细的Survivor空间占用日志，开发环境调试用
-Xlog:gc+age=trace

#针对混合回收回收的参数：混合回收不仅针对老年代，还有新生代和大对象

#老年代Region触发混合GC的占比，默认值是45，也就是说老年代占据了堆内存45%的Region的时，会触发混合GC。该值一般不需要调整，这样可以让JVM内存占用维持在50%左右
-XX:InitiatingHeapOccupancyPercent=45
#混合回收阶段会执行8次（默认值），一次只回收掉部分Region，然后系统继续运行一小段时间，之后再继续混合回收，重复8轮。混合回收通过间断操作，可以把每次的回收时间控制在指定的停顿时间之内，最终也达到了垃圾清理的效果。
-XX:G1MixedGCCountTarget=8
#混合回收整理出来的空闲空间占heap的5%时（默认值），终止本次回收
-XX:G1HeapWastePercent=5
#如果一个Region中的存活对象大于Region大小的85%的话（默认值），就不去回收这个Region
-XX:+UnlockExperimentalVMOptions -XX:G1MixedGCLiveThresholdPercent=85

#OOM时堆内存dump到当前目录
-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/var/java_pid%p.hprof


```

### 场景4：不合理的GC策略或对象分配

4.1 调整年轻代大小：年轻代过小会导致Minor GC频繁，存活对象年龄迅速增长，提前进入老年代

4.2 避免大对象：检查是否有过大的数组或字符串，它们会直接在老年代分配。优化代码逻辑，避免创建过大的对象

### 场景5：代码中调用了System.gc()

## AQS
核心组件 （volatile state，FIFO）, state=0表示锁空闲，state=1表示锁被占用，在Semaphor中表示可用的许可证数

## 线程池核心参数动态调整策略，拒绝策略

拒绝策略：AbortPolicy，CallerRunsPolicy, DiscardPolicy, DiscardOldestPolicy

## ThreadLocal内存泄漏 
ThreadLocalMap中的entry，key是弱引用对象,value是强引用对象。GC发生后key被回收，但是Entry.value强引用仍然存在，并且之后无法被访问到，造成内存泄漏

## 雪花ID
- UIDGenerator（Baidu）
- Leaf（美团）
- IdGenerator 

## Spring如何解决Bean循环依赖

只能解决单例的bean通过属性注入的场景，不支持构造方法注入，非单例的bean，以及多例Bean通过@Async等AOP代理产生的循环依赖。一级缓存singletonObjects，二级缓存earlySingletonObjects，三级缓存singletonFactories
为什么三级缓存时Factories? 因为在调用 ```ObjectFactory.getObject()```需要判断是否需要AOP代理，不需要则直接返回，需要的话返回的是AOP代理对象

```
我们以最经典的 A 依赖 B，B 依赖 A 为例，详细走一遍流程

步骤 1：开始创建 A
调用 getBean("a")，发现 A 不在二、三级缓存中，开始创建

实例化 A：通过反射 new A() 得到一个对象（此时 @Autowired B b 字段还是 null）

【关键一步】：Spring 将封装了 A 原始对象的 ObjectFactory 放入 三级缓存 (singletonFactories)。这个 ObjectFactory 的作用是：在需要时，能够返回 A 的一个“早期引用”

进行属性填充 (populateBean)：Spring 发现 A 依赖 B，于是调用 getBean("b")

此时的状态：

A 对象已实例化，但属性 B 为 null

A 的 ObjectFactory 已在 三级缓存

一级、二级缓存没有 A

步骤 2：开始创建 B
调用 getBean("b")，发现 B 不在二、三级缓存中，开始创建

实例化 B：通过反射 new B() 得到一个对象（此时 @Autowired A a 字段还是 null）

【关键一步】：Spring 将封装了 B 原始对象的 ObjectFactory 放入 三级缓存 (singletonFactories)

进行属性填充 (populateBean)：Spring 发现 B 依赖 A，于是调用 getBean("a")

步骤 3：B 获取 A（循环依赖的解决点）
再次调用 getBean("a")

这次，Spring 在 三级缓存 中找到了 A 的 ObjectFactory

调用 ObjectFactory.getObject()

如果 A 不需要 AOP 代理：这个方法直接返回步骤 1 中实例化的那个原始 A 对象

如果 A 需要 AOP 代理：这个方法会返回 A 的代理对象（一个 Proxy 或 CGLIB 增强对象）。这就是为什么三级缓存是工厂，而不是直接放对象的原因——为了在暴露早期引用时，有机会介入生成代理

Spring 将这个从三级缓存工厂得到的对象（可能是原始对象，也可能是代理对象）放入二级缓存 (earlySingletonObjects)

同时，从三级缓存中移除A的ObjectFactory

将这个A的早期引用（来自二级缓存）注入到B对象中。至此，B的a字段被成功赋值

此时的状态：

B 完成了属性填充，它的a字段指向了A的早期引用

A 的早期引用现在存在于二级缓存

步骤 4：B 完成创建
B 继续执行后续的初始化流程（如 InitializingBean, @PostConstruct 等）

B 完全创建好后，成为一个“成品” Bean，被放入一级缓存 (singletonObjects)

同时，从二级、三级缓存中移除 B

步骤 5：A 完成创建
此时，getBean("b") 返回了创建好的 B 对象（从一级缓存获取）

Spring 将这个 B 的“成品”注入到A的b字段中

A 继续执行后续的初始化流程

A 完全创建好后，成为一个“成品” Bean，被放入一级缓存 (singletonObjects)

同时，从二级、三级缓存中移除 A
```

## 一万QPS + 500ms 接口，需要多少机器？
```
10000 QPS 500ms -> 10000*0.5 = 5000(线程)
500ms = （cpu占用 50ms + IO 450ms）

## 服务器规格  8核16G

## 核心线程数 
1. CPU密集型 8+1
2. IO密集型  8*2
3. 混合型 8
最大线程数 = CoreCPU * （1 + 线程等待时间/线程占用cpu时间）= 8 * （1+450/50） = 80

## 计算机器数量
500ms处理一个请求，一秒处理2个请求， 单机最大80个线程
单机最大QPS = 2*80 = 160；

总 QPS 10000/160 = 62.5 ~~ 63 *（1.30） ~~ 90台机器 
```
