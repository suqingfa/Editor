# SQL语法

## 数据库操作

```mysql
show databases;

select database();

create database if not exists test character set 'utf8mb4';

alter database test character set 'utf8mb4';

show create database test;

use test;

drop database test;
```

## 表操作

```mysql
show tables;

create table user
(
    id       int primary key,
    username varchar(64) unique not null,
    passwd   varchar(32),
    a        int
) character set 'utf8mb4';

alter table user
    modify passwd varchar(64),
    add age int unsigned,
    drop a
;

show create table user;

rename table user to users;

truncate table users;

drop table users;
```

## 数据操作

```mysql
insert into user(id, username, passwd, age)
values (1, 'username', 'passwd', 20);

update user
set age = 20
where id = 1;

delete
from user
where id = 1;
```

## 数据查询

[SELECT Statement](https://dev.mysql.com/doc/refman/8.0/en/select.html)

```
SELECT
    [ALL | DISTINCT | DISTINCTROW ]
    [HIGH_PRIORITY]
    [STRAIGHT_JOIN]
    [SQL_SMALL_RESULT] [SQL_BIG_RESULT] [SQL_BUFFER_RESULT]
    [SQL_NO_CACHE] [SQL_CALC_FOUND_ROWS]
    select_expr [, select_expr] ...
    [into_option]
    [FROM table_references
      [PARTITION partition_list]]
    [WHERE where_condition]
    [GROUP BY {col_name | expr | position}, ... [WITH ROLLUP]]
    [HAVING where_condition]
    [WINDOW window_name AS (window_spec)
        [, window_name AS (window_spec)] ...]
    [ORDER BY {col_name | expr | position}
      [ASC | DESC], ... [WITH ROLLUP]]
    [LIMIT {[offset,] row_count | row_count OFFSET offset}]
    [into_option]
    [FOR {UPDATE | SHARE}
        [OF tbl_name [, tbl_name] ...]
        [NOWAIT | SKIP LOCKED]
      | LOCK IN SHARE MODE]
    [into_option]

into_option: {
    INTO OUTFILE 'file_name'
        [CHARACTER SET charset_name]
        export_options
  | INTO DUMPFILE 'file_name'
  | INTO var_name [, var_name] ...
}
```

### 多表查询

- join
    - 内连接 取出两张表中匹配到的数据，匹配不到的不保留
    - 外连接 取出连接表中匹配到的数据，匹配不到的也会保留，其值为NULL
    - 自连接
- union
    - union / union distinct
    - union all
- intersect
- except

### 子查询

- 标量子查询
- 行子查询
- 列子查询
- 表子查询

[sub queries](https://dev.mysql.com/doc/refman/8.0/en/subqueries.html)

## 数据库控制语言

用户管理

```mysql
select *
from mysql.user;

create user 'test'@'%' identified BY 'test';

alter user 'test'@'%' identified BY 'test';

drop user 'test'@'%';
```

权限管理

```mysql
show grants for 'test'@'%';

grant all on test.* to 'test'@'%';

revoke all on test.* from 'test'@'%';
```

# 函数

- [字符串函数](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html)
- [日期函数](https://dev.mysql.com/doc/refman/8.0/en/date-and-time-functions.html)
- [数学函数](https://dev.mysql.com/doc/refman/8.0/en/numeric-functions.html)
- [流程控制函数](https://dev.mysql.com/doc/refman/8.0/en/flow-control-functions.html)
- [聚合函数](https://dev.mysql.com/doc/refman/8.0/en/aggregate-functions-and-modifiers.html)
- [窗口函数](https://dev.mysql.com/doc/refman/8.0/en/window-functions.html)
- [加密函数](https://dev.mysql.com/doc/refman/8.0/en/encryption-functions.html)

窗口函数

```mysql
drop table if exists test;

create table test
(
    value int not null
);

insert into test(value)
values (1),
       (1),
       (2),
       (3),
       (4),
       (5),
       (5),
       (5),
       (6),
       (7);

select *,
       cume_dist() over w, # 累计分布
       row_number() over (partition by value order by value),
       row_number() over w,
       rank() over w,
       dense_rank() over w,
       percent_rank() over w
from test
window w as (order by value);
```

# 事务

[transactional](https://dev.mysql.com/doc/refman/8.0/en/sql-transactional-statements.html)

事务是一组操作的集合,事务会把所有的操作作为一个整体向系统提交或撤销操作,即这组操作要么全成功要么全失败.

事务的四大特性(ACID)

- 原子性
- 一致性
- 隔离性
- 持久性

并发事务问题

- 脏读
- 不可重复读
- 幻读

事务隔离级别
[isolation](https://dev.mysql.com/doc/refman/8.0/en/innodb-transaction-isolation-levels.html)

- 读取未提交
- 读取已提交
- 可重复读取(MySQL默认级别)
- 序列化

```mysql
start transaction;
begin;

commit;

rollback;

show variables like 'transaction_isolation';

set session transaction isolation level read committed;
```

# 索引

[indexes](https://dev.mysql.com/doc/refman/8.0/en/optimization-indexes.html)

索引数据结构

- B+tree
- Hash
- R-tree
- Full-text

索引分类

- 主键索引
- 唯一索引
- 常规索引
- 全文索引

索引存储

- 聚集索引 索引和数据存放在一起,索引结构的叶结点保存了行数据
- 二级索引 索引和数据分开存放,索引结构的叶结点保存了行的主键

聚集索引选取

- 主键作为聚集索引
- 第一个唯一索引作为聚集索引
- 生成一个隐藏的rowid作为聚集索引

## 性能分析

查询 insert/select/update 次数

```mysql
show status like 'Com_select';
show status like 'Com_insert';
show status like 'Com_update';
```

慢查询日志

```mysql
show variables like 'slow_query_log';
show variables like 'long_query_time';
show variables like 'slow_query_log_file';
```

profile

```mysql
show variables like 'have_profiling';
show variables like 'profiling';
set profiling = 'ON';
show profiles;
show profile all for query 40;
```

### explain

[explain](https://dev.mysql.com/doc/refman/8.0/en/execution-plan-information.html)

- id 查询序号,表示查询中sql的执行顺序(id相同,从上往下执行,id不同,序号越大越先执行)
- type
    - null
    - system 结果仅一行(system table)
    - const 结果仅一行,主键或唯一索引等值查询
    - eq_ref
    - ref 使用非唯一性索引查询
    - fulltext json全文索引查询
    - ref_or_null
    - index_merge
    - unique_subquery
    - index_subquery
    - range 索引范围查询
    - index
    - all
- possible_keys 可选的索引
- key 实际使用的索引
- key_len 索引长度,使用最左前缀匹配
- rows 预估结果行数
- filtered 结果占读取的行的百分比
- extra

索引失效情况

- 索引列上进行运算操作
- 索引列存在类型转换,如字符串类型字段不加引号
- 索引列头部模糊查询(尾部模糊查询可以使用索引)
- or连接(仅当两侧都有索引时才使用索引)
- 数据分布影响

### 索引使用

- use index
- ignore index
- force index

使用覆盖索引,仅返回索引中的列和主键列,不需要回表查询

前缀索引,只将字符串的一部分前缀建立索引

# 优化

插入优化 批量插入\手动控制事务\主键顺序插入\大批量插入load data

主键优化 主键长度尽可能短\顺序插入

order by

- using index 直接通过索引返回数据
- using filesort 将结果缓冲区中排序,尽可能优化为using index

group by 使用索引,多字段分组时满足最左前缀匹配法则

limit 覆盖索引+子查询

count count(field) < count(id) < count(1) = count(*)

update 尽可能使用主键\索引更新数据,避免行锁升级为表锁

# 存储对象

## 视图

```mysql
create or replace view factor_view as
select code, trade_day, identity, value
from factor
where code = 'close_price'
with check option;

show create view factor_view;

drop view factor_view;
```

要使视图可以更新,视图中的行要和原始表中的行一一对应

## 存储过程

[procedure](https://dev.mysql.com/doc/refman/8.0/en/create-procedure.html)

封装,复用.可以接受参数,也可以返回数据.减少网络交互.

```mysql
create procedure p1()
begin
    select count(*) from factor;
end;

call p1();

show create procedure p1;

drop procedure p1;
```

## 触发器

[trigger](https://dev.mysql.com/doc/refman/8.0/en/create-trigger.html)

```
CREATE
    [DEFINER = user]
    TRIGGER [IF NOT EXISTS] trigger_name
    trigger_time trigger_event
    ON tbl_name FOR EACH ROW
    [trigger_order]
    trigger_body

trigger_time: { BEFORE | AFTER }

trigger_event: { INSERT | UPDATE | DELETE }

trigger_order: { FOLLOWS | PRECEDES } other_trigger_name
```

# 锁

## 全局锁

做全库数据备份

```shell
mysqldump --host=localhost --user=root --password=root test
```

```mysql
flush tables with read lock;
unlock tables;
```

## 表级锁

表锁

- read lock
- write lock

```
LOCK TABLES
    tbl_name [[AS] alias] lock_type
    [, tbl_name [[AS] alias] lock_type] ...

lock_type: {
    READ [LOCAL]
  | [LOW_PRIORITY] WRITE
}

UNLOCK TABLES
```

元数据锁 MDL(meta data lock) 系统自动管理,避免DML和DDL冲突.

```mysql
select *
from performance_schema.metadata_locks;
```

意向锁 解决行锁和表锁的冲突问题

- 意向共享锁IS
- 意向排他锁IX

```mysql
select *
from performance_schema.data_locks;
```

## 行级锁

- 行锁Record Lock 锁定索引中单行记录
    - 共享锁S
    - 排他锁X
- 间隔锁Gap Lock 锁定索引范围,防止其它事务在间隔中执行insert产生幻读
- 临键锁Next Key Lock 行锁+间隔锁,同时锁定行记录和前面的间隔

# InnoDB

[innodb storage engine](https://dev.mysql.com/doc/refman/8.0/en/innodb-storage-engine.html)

- tablespace 对应一个.idb文件
- segment
    - 索引段
    - 数据段
    - 回滚段
- extent 1MB
- page 16KB 磁盘管理最小单元
- row

![InnoDB Architecture](https://dev.mysql.com/doc/refman/8.0/en/images/innodb-architecture-8-0.png)

## 事务原理

redo log / undo log
解决事务的原子性一致性持久性

MVCC多版本并发控制 优化读写并发性

- 当前读 读取最新版本,读取时要保证其他并发事务不能修改当前记录,需要对读取记录进行加锁
  select ... lock in share model, select ... for update, update ..., insert ..., delete ...
- 快照读 读取数据快照,不加锁,非阻塞读取
    - 读取已提交 每次select都生成一个快照
    - 可重复读取 第一次select时生成一个快照
    - 序列化 快照读会退化为当前读

隐藏字段+undo log版本链+read view实现MVCC

# 系统表

- information_schema 访问数据库元数据,数据库表结构,视图结构,权限等
- mysql mysql正常运行所需要的表(时区,用户,权限,集群,主从复制)
- performance_schema 监控mysql运行状态
- sys 一些表的视图

# 日志

## 错误日志

```mysql
show variables like '%log_error%';
```

## 二进制日志

binlog记录了所有DDL和DML,但不包括数据查询.
日志格式 statement 和 row

```shell
mysqlbinlog --no-defaults -v binlog.000011
```

```mysql
show variables like '%log_bin%';
show variables like 'binlog_format';

show variables like 'binlog_expire_logs_seconds'; # 日志自动清理时间

reset master; # 清理所有日志
purge master logs to 'binlog.******'; # 清理binlog.******及之前的日志
purge master logs before 'yyyy-mm-dd hh24:mi:ss'; # 清理日期及之前的日志
```

## 查询日志

```mysql
show variables like 'general_log%';
set global general_log = 'on';
```

## 慢查询日志

```mysql
show variables like 'slow_query%';
show variables like 'long_query_time';
show variables like 'log_queries_not_using_indexes';
```

# 主从复制

将主数据库的DDL和DML操作语句通过二进制日志文件传送到从数据库中,然后在从库中对这些语句重新执行,使得从库和主库的数据操持一致.

- 主库出现问题,可以快速切换到从库
- 实现读写分离
- 可以在从库执行备份

主库 my.cnf

```
server-id=1
read-only=0
```

创建账号,授予主从复制权限,显示主库状态

```mysql
create user 'slave'@'%' identified with password by 'root';
grant replication slave on test.* to 'slave'@'%';

show master status;
```

从库 my.cnf

```
server-id=2
read-only=1
```

连接主库

```mysql
change master to master_host ='', master_user ='', master_password ='', master_log_file ='', master_log_pos =0;
start slave;
show slave status;
```

# sharding jdbc

[sharding sphere](https://shardingsphere.apache.org/index_zh.html)

## 分表分库

将数据分散存储,使得单一数据库/表的数据量变小来缓解单一数据库的性能问题,从而达到提升数据库性能的目的.

## 读写分离

