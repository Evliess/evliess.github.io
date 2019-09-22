---
layout: index
title: "Redis"
category: database
date: 2018-04-06 15:17:55
---

# Redis overriew

Redis与其他内存数据库与众不同，是因为以下三个原因：  
- Redis将数据库中所有的数据都加载到内存中，磁盘只是用来存储数据。  
- Redis支持更加丰富的数据结构。  
- Redis可以将数据备份到任意多个slave上。

Redis的优势  
- **非常快** 可以每秒执行110000次SET操作，81000次GET操作。  
- **支持丰富的数据类型** list, set, sorted set, and hashes 。  
- **所有的操作都是原子性**   
- **用途广泛** 可以用作缓存，消息队列（Redis天生支持Publish/Subscribe），存储Web应用中的session，网页命中计数等。

# Redis Installation

```shell
$ wget http://download.redis.io/releases/redis-4.0.9.tar.gz
$ tar xzf redis-4.0.9.tar.gz
$ cd redis-4.0.9
$ make
```

## Startup Redis

```shell
. src/redis-server
```

## Use CMD Line
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

## Stop redis
```shell
//查找正在运行redis的进程以及端口号
ps -ef |grep redis

//使用redis-cli关闭redis服务
./redis-cli -p 6379 shutdown
```

# Redis Commands

## Keys

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


## Strings

- APPEND key value
如果key已经存在并且它的value是String类型，那么将value追加到value的结尾。  
如果key不存在，相当于执行set命令。  

- SET key value [EX seconds] [PX milliseconds] [NX|XX]
设置key和value，同时设置该key的过期时间。  

- GET key  
获取key对应的value  

- MGET key [key ...]  
获取多个key的值，如果不存在则返回nil  

- MSET key value [key value ...]  
设置多个key的值  

- SETRANGE key offset value 
从offset位置替换对应的key的value部分，替换的长度为value的长度
```sql
SET key1 "Hello World"
//'W'处开始开始替换
SETRANGE key1 6 "Redis"
//返回 "Hello Redis"
GET key1
```

- STRLEN key  
返回key的value的长度  

## Hashes
数据结构和map类似，key和value都是string类型。  

- HSET key field value
在名字为key的map中添加一条key为field，值为value的记录
```sql
hset people name zhangsan
hset people age 12
```
- HGET key field
在名字为key的map中获取field的值
```sql
hget people name
```
- HLEN key
返回map的长度  

- HMGET key field [field ...]   
返回map中多个key的value  

- HMSET key field value [field value ...]  
设置map中的多个字段  

- HKEYS key  
返回map中所有的field  

- HVALS key  
返回map中所有的value  


## Lists  

- LPUSH key value [value ...]  
在list中添加一个元素  
```sql
lpush mylist zhangsan
```

- LINDEX key index  
返回下标为index的元素  
```sql
lindex mylist 0
```
- LINSERT key BEFORE|AFTER pivot value  
在指定的元素位置前或者后面插入元素  
```sql
//在zhangsan前面插入lisi
LINSERT mylist BEFORE "zhangsan" "lisi"
```

- LLEN key  
返回list的长度  

- LPOP key  
删除并返回第一个元素  

- LRANGE key start stop  
返回列表 key 中指定区间内的元素，区间以偏移量 start 和 stop 指定。start和stop都是以0为基准，也可以为负值，表示从list的尾部开始算起。
如果start > stop ,返回一个空列表。如果stop下表大于end，则Redis会把end的值赋给stop。
```sql
//pis：此时返回6个元素，和编程中的界限不一样
lrange mylist 0 5
```

- LREM key count value  
删除list中count个数的等于value的元素  
```sql
lrem mylist 1 zhangsan
```
- LSET key index value
将列表 key下标为index的元素的值设置为value
```
lset mylist 0 wangwu
``` 
- RPUSH key value [value ...]  
将一个或者多个值插入list的末尾  

- RPOP key  
移除并返回list的尾元素  

## Sets  

- SADD key member [member ...]  
在set中添加一条或者多条记录，set中的元素不重复  

- SCARD key  
返回set的长度  

- SDIFF key [key ...]  
计算集合之间的差集
```sql
key1 = {a,b,c,d}
key2 = {c}
key3 = {a,c,e}
SDIFF key1 key2 key3 = {b,d}
```

- SDIFFSTORE destination key [key ...]  
将集合的差集保存在键为key的集合中  
```sql
key1 = {a,b,c,d}
key2 = {c}
SDIFFSTORE key1 key2 key3
keys = {b,d}
```

- SINTER key [key ...]  
计算集合的交集  
```sql
key1 = {a,b,c,d}
key2 = {c}
key3 = {a,c,e}
SINTER key1 key2 key3 = {c}
```
- SINTERSTORE destination key [key ...]
将集合的交集保存在键为key的集合中  
```sql
key1 = {a,b,c,d}
key2 = {c}
key3 = {a,c,e}
SINTERSTORE key1 key2 key3 key4
key4 = {c}
```
- SUNION key [key ...]
计算集合的并集  
```sql
key1 = {a,b,c,d}
key2 = {c}
key3 = {a,c,e}
SUNION key1 key2 key3 = {a,b,c,d,e}
```
- SUNIONSTORE destination key [key ...]  
计算集合的并集并返回一个新的集合  
```sql
key1 = {a,b,c,d}
key2 = {c}
key3 = {a,c,e}
SUNION key1 key2 key3 key4
key4 = {a,b,c,d,e}
```

- SISMEMBER key member  
判断set中是否包含member,包含返回1，不包含返回0  
```sql
SADD myset "one"
//返回1
SISMEMBER myset "one"
//返回0
SISMEMBER myset "two"
```
- SMEMBERS key  
返回set中所有的value  

- SMOVE source destination member  
将元素从源set移动到目的地set，是一个原子操作  

- SPOP key [count]  
随机移除并返回集合中的一个或者多个元素  

- SRANDMEMBER key [count]  
随机获得集合中一个或者多个元素  

- SREM key member [member ...]  
删除集合中的一个或者多个元素  

## Sorted Sets  
有序集合里面的成员是不能重复的都是唯一的，但是，不同成员间有可能有相同的分数  

- ZADD key [NX\|XX] [CH] [INCR] score member [score member ...]  
```sql
/**
*ZADD options (Redis 3.0.2 及以上版本支持)
*NX : 不更新已经存在的元素，只增加新的元素。  
*XX ：只更新已经存在的元素，不增加元素。  
*CH ：修改返回值为发生变化的成员总数，原始是返回新添加成员的总数 (CH 是 changed 的意思)。更改的元素是新添加的成员，已经存在的成员更新分数。 所以在命令中指定的成员有相同的分数将不被计算在内。注：在通常情况下，ZADD返回值只计算新添加成员的数量。  
*INCH ：当ZADD指定这个选项时，成员的操作就等同ZINCRBY命令，对成员的分数进行递增操作  
*/
ZADD myzset 1 "one"
ZADD myzset 2 "two" 3 "three"
```
- ZCARD key  
返回集合的长度  

- ZRANGEBYSCORE key min max [WITHSCORES] [LIMIT offset count]  
返回有序集合中分数在min和max之间的所有元素  
```sql
/**
*min和max可以是-inf和+inf  
*min 表示 大于等于;  (min 表示大于  
*max 表示 小于等于;  (max 表示小于  
*/
ZRANGEBYSCORE myzset -inf +inf
ZRANGEBYSCORE myzset (1 2
ZRANGEBYSCORE myzset (1 (2
```
-  ZRANGE key start stop [WITHSCORES]   
返回有序集合中指定范围(start,stop)内的元素  

- ZCOUNT key min max  
返回有序集合中score在min和max之间元素的个数  

- ZINCRBY key increment member  
将有序集合中member的score增加increment；如果member不存在，就增加一个member；score是increment，如果key不存在，则创建一个只包含member的有序集合。  
```sql
ZADD myzset 1 "one"
ZADD myzset 2 "two"
//"one"的score变为3  
ZINCRBY myzset 2 "one"
ZRANGE myzset 0 -1 WITHSCORES
```
- ZRANK key member  
返回有序集合中元素的排名，排名默认从0开始  

- ZREM key member [member ...]  
删除有序集合中一个或者多个元素  

- ZINTERSTORE destination numkeys key [key ...] [WEIGHTS weight [weight ...]] [AGGREGATE SUM|MIN|MAX]  
计算多个有序集合的交集。WEIGHT是计算因子，默认是1. 指定WEIGHT就是 score = score*WEIGHT;聚簇函数AGGREGATE 选项默认是SUM
```sql
zadd Q1 88 "apple"
zadd Q1 92 "orange"
zadd Q1 100 "peach"
zadd Q2 27 "apple"
zadd Q2 34 "orange"
zadd Q2 80 "peach"
zadd Q2 100 "grape"
zinterstore Q1Q2 2 Q1 Q2
/**
*apple(115) = Q1(88) + Q2(27)
*Q1 中不包含grape，Q1Q2中也没有
*/
zrange Q1Q2 0 -1 withscores
1) "apple"
2) "115"
3) "orange"
4) "126"
5) "peach"
6) "180"
/**
*apple(115) = Q1(88*1) + Q2(27*2)
*Q1 中不包含grape，Q1Q2中也没有
*/
zinterstore Q1Q2 2 Q1 Q2 WEIGHTS 1 2
zrange Q1Q2 0 -1 withscores
1) "apple"
2) "142"
3) "orange"
4) "160"
5) "peach"
6) "260"
zinterstore Q1Q2 2 Q1 Q2 WEIGHTS 1 2 AGGREGATE SUM
zrange Q1Q2 0 -1 withscores
1) "apple"
2) "142"
3) "orange"
4) "160"
5) "peach"
6) "260"
/**
*apple(54) = min(Q1(88*1), Q2(27*2))
*orange(68) = min(Q1(92*1), Q2(34*2))
*peach(68) = min(Q1(100*1), Q2(80*2))
*Q1 中不包含grape，Q1Q2中也没有
*/
zinterstore Q1Q2 2 Q1 Q2 WEIGHTS 1 2 AGGREGATE MIN
zrange Q1Q2 0 -1 withscores
1) "apple"
2) "54"
3) "orange"
4) "68"
5) "peach"
6) "100"
/**
*apple(54) = max(Q1(88*1), Q2(27*2))
*orange(68) = max(Q1(92*1), Q2(34*2))
*peach(68) = max(Q1(100*1), Q2(80*2))
*Q1 中不包含grape，Q1Q2中也没有
*/
zinterstore Q1Q2 2 Q1 Q2 WEIGHTS 1 2 AGGREGATE MAX
zrange Q1Q2 0 -1 withscores
1) "apple"
2) "88"
3) "orange"
4) "92"
5) "peach"
6) "160"
```
- ZUNIONSTORE destination numkeys key [key ...] [WEIGHTS weight [weight ...]] [AGGREGATE SUM\|MIN\|MAX]  
计算多个有序集合的并集。WEIGHT是计算因子，默认是1. 指定WEIGHT就是 score = score*WEIGHT;聚簇函数AGGREGATE 选项默认是SUM
```sql
zunionstore Q1Q2 2 Q1 Q2 WEIGHTS 1 2 AGGREGATE MAX
zrange Q1Q2 0 -1 withscores
/**
*apple(54) = max(Q1(88*1), Q2(27*2))
*orange(68) = max(Q1(92*1), Q2(34*2))
*peach(68) = max(Q1(100*1), Q2(80*2))
*Q1不包含grape，Q2包含grape，Q1Q2中包含grape
*/
1) "apple"
2) "88"
3) "orange"
4) "92"
5) "peach"
6) "160"
7) "grape"
8) "200"
```

# 引用
[参考链接1](http://www.redis.cn/commands/zadd.html) [参考链接2](https://redis.io/commands/zadd)  
