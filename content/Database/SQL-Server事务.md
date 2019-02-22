---
title: SQL Server事务
date: 2015-04-01T21:20:19+08:00
tags: ["SQL Server"]
categories: ["Database"]
permalink: SQL-Server-Transaction
description: SQL Server 事务
---
# 1. 什么是事务
　　事务(Transaction)是访问并可能更新数据库中的各种数据项的一个程序执行单元。事务用来保持逻辑数据的一致性和可恢复性，避免错误操作引发不可挽回的损失。
　　事务由begin Transaction(事务开始)和end Transaction(事务结束)之间执行的全部成员构成。在关系型数据库中，事务是一条或多条SQL语句组成的。

## 1.1. 事务的特性
　　事务有四个属性：原子性、一致性、隔离性、持久性。<!--more-->
　　● 原子性(atomicity)：一个事务是一个不可分割的工作单位，事务中的操作，要么全部都执行，要么全部都不执行。
　　● 一致性(consistency)：事务结束的时候，所有的内部数据必须全部都是正确的。
　　● 隔离性(isolation)：并发多个事务时。各个事务之间不干涉内部数据，每一个事务处理的都是另一个事务处理之前或者处理之后的数据。
　　● 持久性(durability)：当事务提交后，数据的改变是永久性的，不能在回滚。

## 1.2. 事务的种类
　　自动提交事务：SQL Server中默认的事务模式，将每一条SQL语句看做是一个事务来执行。
　　显式事务：使用SQL语句编写，由`Begin Transaction`开启事务，`Commit Transaction`提交事务或者`Rollback Transaction`回滚事务结束事务。
　　隐式事务：使用`Set IMPLICIT_TRANSACTIONS {ON|OFF}`打开或者关闭事务，当为`ON`时会将隐式事务模式打开，这时就不使用Begin Transaction开启事务了，隐式事务模式会自动启动事务，要做的只是`Commit Transaction`提交事务、`Rollback Transaction`回滚事务。

　　<text style='color:yellow'>💡<text/> 我们常用的事务类型就是显式事务，也就是我们自定义的事务。

# 2. 编写事务
　　常用的关键词语句有4个：
　　1. Begin Transaction：标记事务开启。
　　2. Commit Transaction：提交事务，表示事务成功执行。
　　3. Rollback Transaction：回滚事务，事务的执行过程中出现错误，回滚到事务处理之前的状态。
　　4. Save Transaction：设置一个事务保存点，当事务执行出错是，Roolback Transaction语句执行后可以不用全部回滚，可以回滚到这个保存点。当然前提是保证保存点之前的事务不出错。

## 2.1. 事务示例代码：
　　假设我们有一张员工表，有三个字段分别是Name(nchar)、Age(int)、Class(nchar)

```sql
---开启事务
begin tran
--错误捕捉机制
begin try  
   --正确
   insert into Staff (Name,Age,Class) values ('Microsoft',30,'wp部门')
   --正确
   insert into Staff (Name,Age,Class) values ('Google',20,'Android部门')
   --因为年龄Age字段是int类型，这里插入了字符串，错误
   --insert into Staff (Name,Age,Class) values ('Apple','abc','ios部门')
end try
begin catch
   select Error_number() as ErrorNumber,  --出现错误的代码
          Error_severity() as ErrorSeverity,  --错误严重级别，级别小于10 try catch 捕获不到
          Error_state() as ErrorState ,  --错误状态码
          Error_Procedure() as ErrorProcedure , --出现错误的存储过程或触发器的名称。
          Error_line() as ErrorLine,  --发生错误的行号
          Error_message() as ErrorMessage  --错误的具体信息
   if(@@trancount>0) --全局变量@@trancount，事务时开启此值+1，用来判断是否开启事务
      rollback tran  ---由于第三条语句出错，这里回滚到开始，前两条语句也没有执行成功
end catch
if(@@trancount>0)
commit tran  --没有发生错误就提交事务。
```
![](http://ww2.sinaimg.cn/mw690/c55a7aeejw1f1d464k3i0j20o90hq7av.jpg)
