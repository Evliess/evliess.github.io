---
layout: index
title: "Database basic usage"
category: database
date: 2018-03-14 15:17:55
---

# Database basic knowledge


## Mysql Common Commands

```
// Add a column
ALTER TABLE `stage` ADD `subpipeline_id` bigint(20) DEFAULT NULL;
// Add a constraint
ALTER TABLE `build_stage` ADD CONSTRAINT `BUILD_STAGE_SUBPIPELINE_FK` FOREIGN KEY (`subpipeline_id`) REFERENCES `pipeline` (`id`);
// Drop a constraint
ALTER TABLE `build_stage` DROP FOREIGN KEY `BUILD_STAGE_SUBPIPELINE_FK`;
```

## Oracle Common Commands

### Install sqlplus instantclient to connect oracle.

1. Download [instantclient-basic-windows.x64-19.3.0.0.0dbru.zip](https://download.oracle.com/otn_software/nt/instantclient/19300/instantclient-basic-windows.x64-19.3.0.0.0dbru.zip)
2. Download [instantclient-sqlplus-windows.x64-19.3.0.0.0dbru.zip](https://download.oracle.com/otn_software/nt/instantclient/19300/instantclient-sqlplus-windows.x64-19.3.0.0.0dbru.zip)
3. Extract them to current folder.
4. Set Env Path C:\Users\<Your_Name>\Downloads\oracle\instantclient_19_3
5. win + r cmd ```sqlplus idaDbAdmin/idaDbAdmin@127.0.0.1:32778/ORCLCDB.localdomain```


### Common scripts
```
// login vm
ssh <YOUR_IP>

// open sqlplus client
docker exec -it oracle12c bash -c "source /home/oracle/.bashrc; sqlplus /nolog"

SQL> connect / as sysdba
// search service names
SQL> select value from v$parameter where name='service_names'
SQL> grant create procedure to idaDbAdmin;
SQL> grant execute on sys.dbms_crypto to idaDbAdmin;
SQL> connect idaDbAdmin/idaDbAdmin
SQL> set serveroutput on

// md5
// https://docs.oracle.com/database/121/DBSEG/data_encryption.htm#DBSEG335

```

### Create procedure with md5

```
create or replace procedure addUser (u_pass in varchar2, u_key in varchar2) is 
org_id number;
raw_input RAW(128) := UTL_RAW.CAST_TO_RAW(u_pass);
raw_key RAW(128) := UTL_RAW.CAST_TO_RAW(u_key);
encrypted_raw    RAW(2048);
encrypted_pass varchar2(100);
begin 
    select id into org_id from organization where company_name = 'IDA';
    dbms_output.put_line(org_id);
    encrypted_raw := dbms_crypto.Mac(src => raw_input, typ => DBMS_CRYPTO.HMAC_MD5, key => raw_key);
    dbms_output.put_line('> Result      : ' || rawtohex(encrypted_raw));
end;
/

call addUser('idaAdmin', 'idaAdmin');

```


## DB2 Common Commands

```
// Add a column
ALTER TABLE BUILD_STAGE ADD SUBPIPELINE_ID BIGINT;
// Add a constraint
ALTER TABLE BUILD_STAGE ADD CONSTRAINT BUILD_STAGE_SUBPIPELINE_FK FOREIGN KEY (SUBPIPELINE_ID) REFERENCES PIPELINE (ID);
// Drop a constraint
ALTER TABLE BUILD_STAGE DROP FOREIGN KEY BUILD_STAGE_SUBPIPELINE_FK;



db2start

db2 force application all
db2stop

db2 create database YOUR_DB automatic storage yes using codeset UTF-8 territory US pagesize 32768
db2 connect to YOUR_DB
db2 CREATE BUFFERPOOL BP32K IMMEDIATE ALL DBPARTITIONNUMS SIZE AUTOMATIC NUMBLOCKPAGES 0 PAGESIZE 32 K

db2 drop database

db2 list db directory
db2 list active databases

db2 list tables 

db2 -stvf scripts.sql

```
> Query below returns foreign key constrants defined in a database.

```
select 
    ref.tabschema  concat '.' concat ref.tabname as foreign_table,
    '>-' as rel,
    ref.reftabschema concat '.' concat ref.reftabname as primary_table,
    ref.constname as fk_constraint_name
from syscat.references ref
order by foreign_table, primary_table
```

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

