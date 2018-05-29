---
layout: index
title: "数据库基础"
category: database
date: 2018-03-14 15:17:55
---

# Database basic knowledge


> 数据库左连接和右连接的区别？  

```sql
/左连接后的检索结果是显示t1的所有数据和t2中满足where条件的数据/
select * from t1 left join t2 where t1.id = t2.sid;
```


> 数据库的4个隔离级别  

- ISOLATION_DEFAULT  
- ISOLATION_READ_UNCOMMITTED  
可能会发生读脏，不可重复读和幻读
- ISOLATION_READ_COMMITTED  
阻止读脏，可能会发生不可重复读和幻读  
- ISOLATION_REPEATABLE_READ  
阻止读脏和不可重复读，可能会发生幻读  
- ISOLATION_SERIALIZABLE  
阻止发生读脏，不可重复读和幻读  

> 数据库的读脏，不可重复读和幻读的区别  

- 读脏  
读脏又称无效数据的读取，举例说明：  

```sql
begin transaction1
/修改前工资为500/
update employee set salary = 1000 where id = 1;


begin transaction2
/事务2 发现工资已经变为1000/
select * from employee where id =1;  

transaction1
rollback
/现在工资又变为修改前的500，因此事务2读的就是脏数据/  

```
- 不可重复读  针对更新某一行的数据  
在同一个事务内，两个相同的查询返回的结果不同，举例说明：  

```sql
begin transaction1
select * from employee where id =1;

/事务2更新了这条数据/  
begin transaction2
update employee set salary = 1000 where id = 1;
commit

transaction1
select * from employee where id =1;
/发现两次读到的数据不一致/  

```
- 幻读  针对新增或者删除数据  
在同一个事务内，两个相同的查询返回的结果不同，举例说明:  

```sql
begin transaction1
select * from employ where salary = 1000; /假如返回10条数据/

begin transaction2
insert into employ(,salary,) values(,1000,);
commit

transaction1
select * from employ where salary = 1000; /此时返回11条数据/
```

> 数据库的7种传播行为  

- PROPAGATION_MANDATORY  
支持当前事务，如果当前没有事务，抛异常  
- PROPAGATION_NESTED  
如果存在事务，则在一个嵌套的事务中运行，否则行为和PROPAGATION_REQUIRED一样  
- PROPAGATION_NEVER  
以非事务的方式运行，如果有事务，抛异常  
- PROPAGATION_NOT_SUPPORTED  
以非事务的方式运行，挂起当前事务  
- PROPAGATION_REQUIRED  
支持当前事务，如果当前没有事务，就新建一个事务  
- PROPAGATION_REQUIRES_NEW  
创建一个新是事务，挂起当前事务  
- PROPAGATION_SUPPORTS  
支持当前事务，如果当前没有事务，就以非事务方式执行  

