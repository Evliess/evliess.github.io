---
layout: index
title: "Redis"
category: database
date: 2018-04-06 15:17:55
---

### Redis 概览

Redis与其他内存数据库与众不同，是因为以下三个原因：  
- Redis将数据库中所有的数据都加载到内存中，磁盘只是用来存储数据。  
- Redis支持更加丰富的数据结构。  
- Redis可以将数据备份到任意多个slave上。

Redis的优势  
- **非常快** 可以每秒执行110000次SET操作，81000次GET操作。  
- **支持丰富的数据类型** list, set, sorted set, and hashes 。  
- **所有的操作都是原子性**   
- **用途广泛** 可以用作缓存，消息队列（Redis天生支持Publish/Subscribe），存储Web应用中的session，网页命中计数等。

### Redis 安装

```shell
$ wget http://download.redis.io/releases/redis-4.0.9.tar.gz
$ tar xzf redis-4.0.9.tar.gz
$ cd redis-4.0.9
$ make
```

#### 启动Redis

```shell
. src/redis-server
```

#### 使用命令行与Redis交互
```shell
./redis-cli
127.0.0.1:6379>

/**在远程Redis Server上运行redis命令
* -h 后面指定远程server的地址
* -p 后面指定远程Redis server的端口
* -a 指定访问所需的密码
*/
$ redis-cli -h host -p port -a password
```

#### 关闭Redis服务器
```shell
//查找正在运行redis的进程以及端口号
ps -ef |grep redis

//使用redis-cli关闭redis服务
./redis-cli -p 6379 shutdown
```

### Redis命令

#### Keys

- DEL key [key ...]  
删除一个或者多个key
```sql
set key1 "Hello" 
set key2 "World" 
//删除成功返回1，否则返回0， 删除多个key返回结果的总数
del key1 key2 key3
```

- EXISTS key [key ...]  
判断一个key或者多个key是否存在
```sql
set key1 "Hello" 
set key2 "World" 
//成功返回1，否则返回0， 判断多个key返回结果的总数
exists key1 key2 key3
```

- EXPIRE key seconds  
设置一个key过期的时间
```sql
set key1 "Hello" 
//设置key1 10秒过期
expire key1 10 
```
- KEYS pattern  
返回满足模式的key  
```sql
set key1 "Hello" 
set key2 "World" 
//返回以key开头的所有的keys
keys key*
```
- OBJECT subcommand [arguments [arguments ...]]  
根据redis数据中的key来查看保存这个key的Redis对象的信息  
```sql
lpush mylist "Hello World"
object refcount mylist
object encoding mylist
object idletime mylist
```

- PERSIST key  
移除一个key上设置的过期时间，使之永远不过期
```sql
SET mykey "Hello"
//设置10秒过期
EXPIRE mykey 10
//移除10秒过期的设置
PERSIST mykey
```
- TTL  
查看一个key还有多久过期

- RENAME key newkey
重命名一个key
```sql
set mykey1 "Hello"
rename mykey1 mykey
get mykey
```

- SORT key [BY pattern] [LIMIT offset count] [GET pattern [GET pattern ...]] [ASC\|DESC] [ALPHA] [STORE destination]  
对获取的结果进行排序  
```sql
SORT mylist LIMIT 0 5 ALPHA DESC
```

- TYPE key  
返回key的类型  

- RESTORE key ttl serialized-value [REPLACE]
反序列化给定的序列化的序列化值，并将该值与给定的key关联
```sql
set mykey0 "Hello"
dump mykey0 
restore mykey 0 "\x00\x05Hello\b\x00\x05\xe4\x1bW\x94\x1cu\xbb"
get mykey
```











