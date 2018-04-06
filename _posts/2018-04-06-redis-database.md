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









