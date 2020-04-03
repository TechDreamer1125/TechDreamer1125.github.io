---
layout: post
title: MySQL 事务
date: 2017-05-25
tag: MySQL
---

### 四种事务隔离级别

|          隔离级别          |  脏读  | 不可重复读 |  幻读  |
| :--------------------: | :--: | :---: | :--: |
| 未提交读（Read uncommitted） |  可能  |  可能   |  可能  |
|  已提交读（Read committed）  | 不可能  |  可能   |  可能  |
| 可重复读（Repeatable read）  | 不可能  |  不可能  |  可能  |
|  可串行化（Serializable ）   | 不可能  |  不可能  | 不可能  |


- ***未提交读***：允许脏读，可能读取到其他会话中未提交的事务修改的数据
- ***已提交读***：只能读取到已提交事务的数据，但是对于一个事务的查询可能出现刚开始和结束时不一致的情况（因为第一次查询后，如果出现其他事务修改数据并提交，然后再读的时候会发现两次读的数据不一致）
- ***可重复读***：通过 MVCC（多版本并发控制）来解决不可重复读的问题，同一个事务内的查询都与事务开始时保持一致
- ***串行读***：完全串行化的读，每次读都需要获得表级共享锁，读写相互都会阻塞（效率太低）

用下面的命令来查看全局和会话的隔离级别

```
SELECT @@global.tx_isolation;
SELECT @@session.tx_isolation;
SELECT @@tx_isolation;
```

在`my.cnf`中可以直接配置事务的隔离级别

```
transaction-isolation = {READ-UNCOMMITTED | READ-COMMITTED | REPEATABLE-READ | SERIALIZABLE}
```

