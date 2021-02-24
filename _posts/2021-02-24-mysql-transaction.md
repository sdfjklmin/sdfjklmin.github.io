---
layout: post
title: MySql Transaction
category: "MySQL"
tags: [MySQL, Transaction]
---
MySQL 事务主要用于处理操作量大，复杂度高的数据。

#### 基本组成: ACID

##### A `Atomicity` `原子性`
    一个事务被视为一个不可分割的最小工作单元
	一个事务中的所有操作，要么全部完成，要么全部不完成，不会在中间某个环节结束。
    事务在执行过程中发生错误，会被回滚到事务开始前的状态，就像这个事务从来没有执行过一样。
    原子性关注状态，要么全部成功，要么全部失败，不存在部分成功的状态

##### C `Consistency` `一致性`
	在事务开始之前和事务结束以后，数据库数据的一致性约束没有被破坏。
	保证在一个事务中的多次操作的数据中间状态,对其他事务不可见的。
	因为这些中间状态，是一个过渡状态，与事务的开始状态和事务的结束状态是不一致的
	一致性关注数据的可见性，中间状态的数据对外部不可见，只有最初状态和最终状态的数据对外可见
	(memberA 向 memberB 转 100,在其它事物看来,要么 memberA 减少了, 要么 memberB 增加了,
	不会看到 memberA 减少了, memberB还没有加的情况,这个属于中间状态)
	与A的区别: 未提交读的隔离级别下是事务内部操作是可见的，明显违背了一致性

##### I `Isolation` `隔离性`
	数据库允许多个并发事务同时对数据进行读写和修改的能力。
	隔离性可以防止多个事务并发执行时由于交叉执行而导致数据的不一致。

##### D `Durability` `持久性`
	事务处理结束后，对数据的修改就是永久的，即便系统故障也不会丢失。


#### 隔离级别

针对多个事物的数据处理，不同的隔离级别对数据会有不同的影响。

* 设置事物隔离级别
    

      show variables like "%isolation%";

      "read uncommitted, read committed, repeatable read, serializable"

      set session transaction isolation level repeatable read;

* 锁测试
  

      关闭自动提交
      #show variables like "autocommit";
      set session autocommit=0;
    
      #开始事物
      start transaction;
      #sql
      commit;
    
      #查看事务与锁信息
      show engine innodb status;

      #分析sql,死锁:1.没走行锁,索引失效,全表扫描
      explain sql;

* [autocommit](https://dev.mysql.com/doc/refman/8.0/en/innodb-autocommit-commit-rollback.html)

      自动提交: 如果 autocommit 开启，则每个SQL语句将自己形成一个事务。
      非自动提交: 需要执行多语句事务 START TRANSACTION 或 BEGIN 语句，并用它结束 COMMIT或 ROLLBACK 声明。

##### Read-Uncommitted(读-未提交,有脏读)
    数据: name = test , A事物更新 name = test2,同时B事物也起了,B执行查询name = test2,
    若A回滚,实际数据为 name = test ,而B却返回了 name = test2 ,这就称之为脏读.

##### Read-Committed(读-已提交,不可重复读)
    一个事务只能读到另一个事务修改的已经提交了事务的数据
    A隐式提交了事物,B查询 name = test2,这是没有问题的,但B还没有结束,A中执行更新 name = test3,
    B执行查询 name = test3,这种称之为 不可重复读.

##### Repeatable-Read(可重复读)
    第一次读取的数据，即使别的事务修改的这个值，
    这个事务再读取这条数据的时候还是和第一次获取的一样，不会随着别的事务的修改而改变
    原始数据 name = test, 开启A,B两个事物, A修改 name = test2,同时B事物查询 name = test,
    不管事物A是否提交,在B事物没有提交之前,这条数据对B来说一直都是没有发生改变的,是可以重复的被读到

##### Serializable(串行化)
    只能进行读-读并发。只要有一个事务操作一条记录的写，那么其他要访问这条记录的事务都得等着
    一般没人用串行化，性能比较低，常用的是已提交读和可重复读。

