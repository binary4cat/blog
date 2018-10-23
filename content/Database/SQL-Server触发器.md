---
title: SQL Server触发器
date: 2015-04-16T23:33:00+08:00
tags: ["SQL Server"]
categories: ["Database"]
permalink: SQL-Server-Tirgger
description: SQL Server触发器
---
　　
## 什么是触发器
　　触发器是一种特殊的存储过程，和存储过程不同的是，触发器是由事件被触发时自动进行调用的。
　　触发器的执行不是由程序调用，也不是手工启动，而是有事件来触发，比如当对一个表进行操作(insert、delete、update)是就会激活触发器执行。触发器常用于加强数据的完整性约束和业务规则。

### 分类
　　SQL Server包括了三种常规类型的触发器：DML触发器、DDL触发器和登录触发器。<!--more-->
#### DML触发器
　　当数据库中的表中的数据发生变化时(insert、delete、update)，我们如果已经定义了一个触发器(DML触发器)，那么这个触发器就会自动执行。DML触发器的主要作用在于强制执行业务规则，扩展SQL Server约束、默认值等。约束只能约束同一个表中的数据，但是触发器可以执行任意的SQL命令。
#### DDL触发器
　　SQL Server2005中新增的触发器，用来审核规范对数据库中表、触发器、视图等结构上的操作，比如在修改表，修改列，新增表，新增列等。它在数据库结构发生变化时执行，我们主要用它来记录数据库的修改过程，以及限制程序员对数据库的修改，比如不允许删除某些指定表等。
#### 登录触发器
　　登录触发器会相应login事件并自动执行，与 SQL Server 实例建立用户会话时将引发此事件。登录触发器将在登录的身份验证阶段完成之后且用户会话实际建立之前激发。因此，来自触发器内部且通常将到达用户的所有消息(例如错误消息和来自print语句的消息)会传送到 SQL Server 错误日志。如果身份验证失败，将不激发登录触发器。

## 编写触发器

### 语法
　　create trigger tr_name
　　on 表名 for update|insert|delete
　　as
　　　　 执行代码...

### 示例代码
**inster**:
```
create trigger tri_insert
on student
for insert
as
declare @student_idchar(10)
select @student_id=s.student_id from students
inner join insertedion s.student_id=i.student_id
if @student_id='0000000001'
begin
raiserror('不能插入1的学号!',16,8)
rollbacktran
end
go

```
**update**:
```
create trigger tri_update
on student
for update
as
if update(student_id)
begin
raiserror('学号不能修改!',16,8)
rollbacktran
end
go
```
**delete**:
```
create trigger tri_delete
on student
for delete
as
declare @student_idvarchar(10)
select @student_id=student_id from deleted
if @student_id='admin'
begin
raiserror('错误',16,8)
rollbacktran
end
```

参考[百度百科](http://baike.baidu.com/link?url=EgX0DwE_DlHS4WDkzP1_KXARO9scURthngH_fwxltRTZ2f86F1zTXv2Kf9KQ0vjOjkDQbcBHpJzq-jBpx4CELRuIDwsTsVVk--hLbIjQD1W)
