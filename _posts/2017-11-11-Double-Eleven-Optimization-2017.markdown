---
layout: post
title: 双十一优化总结（2017）
date: 2017-11-11
tag: MySQL
---

### 操作频繁小表宁愿绕点弯路也尽量避免被锁住
- 对于频繁操作的小表，就算锁住的是其中一行记录，也有可能造成难以预期的锁等待。
- 对于这样的表可以先将值读到变量里，再对变量进行操作

### 延迟关联
- 这种操作经常会在大的搜索中出现，先通过索引 `SELECT` 出想要的数据中主表的主键值，然后将这个结果集与主表关联读出想要的数据，这样做延缓了回表时间。因为大的搜索都涉及到分页，如果翻页的时候就回表查会大大降低效率，而通过仅读出主键（不回表）先确定要读出来的值，最后将确定要读的数据的主键跟主表关联得出真正想要的数据。在搜索卡顿的时候把这种方式作为优化方式之一。
- 导出的时候（因为导出是导出所有数据，而不是像搜索一样只需要某一页的数据，但是又不能一次性将数据读出来否则开销太大）也可以将所有的想要的数据的主表主键存到一张临时表中，然后这张临时表自身的主键就是递增的，一页一页读出数据的时候直接可以通过主键来个范围查找。

> 延迟关联一定要理解索引的二次查找 

### 有时候为了避免锁住表，可以将需要的数据放入临时表，然后释放原有表，剩下的操作交给临时表。
- 如果一个事务比较大，锁住表的数据比较多，建议将需要的数据存到临时表中，之后通过临时表做操作。

### 检查索引失效的地方
- 注意 `WHERE` 条件里索引字段的隐式转换导致索引失效，比如 `VARCHAR`类型跟 `INT` 类型比较，导致索引失效
- 在 `WHERE` 条件里出现函数时导致索引失效，这也算一种特殊的隐式转换导致索引失效。（有的 `WHERE` 条件里出现函数并没有导致索引失效那是因为 `SELECT` 的字段都是索引字段，这样直接走了索引）。

### 存储过程
- 对于存储过程（不涉及逻辑），主要需要注意的是当出现事务时，事务要尽可能小（锁粒度）；避免出现事务嵌套；不符合条件是要回滚；以及要加上异常处理的捕捉（SQLEXCEPTION）。

### 数据库监控（MySQL5.6）
暂时我们对于数据库的监控主要集中在慢查询和死锁的监控，而这两者的寸文件都比较大（都只存放在单一文件中），所以同时要配合上相应的工具进行分析。

- 开启 MySQL 的慢查询日志和打印死锁日志的配置

```
### 慢查询
slow_query_log = ON
slow_query_log_file = 「路径」（可自己配置，也可以用默认配置）

### 死锁
innodb_print_all_deadlocks = ON
死锁日志记录在@@log_error的路径里，SELECT @@log_error 即可得到路径
```
- 接下来是有关慢查询日志的分析，首先可以使用 MySQL 自带的工具 `mysqldumpslow`，使用`mysqldumpslow 日志路径` 就可以分析出相应数据（需要将 /usr/local/mysql/bin 配置在环境变量中才可以直接使用该命令）

部分截图：

```
[root@jstumv2ppgydxc ~]# mysqldumpslow /data/mysql/slow.log 

Reading mysql slow query log from /data/mysql/slow.log
Count: 174  Time=122.97s (21397s)  Lock=0.00s (0s)  Rows=0.0 (0), root[root]@[10.24.232.120]
  CALL SP_SALES_DELIVER_ALL(N)

Count: 2  Time=51.98s (103s)  Lock=0.00s (0s)  Rows=0.0 (0), u_wms_yace_wms[u_wms_yace_wms]@[10.24.232.120]
  CALL SP_STOCKOUT_ORDER_NEW_PRINT_BATCH(N,N,N,'S','S',N)

Count: 16  Time=43.49s (695s)  Lock=0.00s (0s)  Rows=0.0 (0), root[root]@[10.24.232.120]
  CALL SP_STOCKOUT_SALES_EXAMINE_YACE()

Count: 3  Time=42.91s (128s)  Lock=0.00s (0s)  Rows=0.0 (0), u_wms_yace_wms[u_wms_yace_wms]@[10.24.232.120]
  CALL SP_STOCKOUT_ORDER_CLEAR_POSITION('S')
```
同样可以使用 percona 发布的 percona toolkit 工具集中的 pt-query-digest，通过 `pt-query-digest 日志路径`也可以直接分析出

部分截图：

```
# Current date: Sat Oct 21 22:20:30 2017
# Hostname: jstumv2ppgydxc
# Files: /data/mysql/slow.log
# Overall: 3.17k total, 24 unique, 0.02 QPS, 0.27x concurrency ___________
# Time range: 2017-10-18 18:05:49 to 2017-10-20 18:14:29
# Attribute          total     min     max     avg     95%  stddev  median
# ============     ======= ======= ======= ======= ======= ======= =======
# Exec time         47165s    40us  15395s     15s     32s    263s      6s
# Lock time           19ms       0   663us     5us    47us    21us       0
# Rows sent         26.47k       0   2.93k    8.54       0  137.13       0
# Rows examine     127.95M       0   8.61M  41.29k   7.31k 323.35k       0
# Query size         6.48M      20   3.78k   2.09k   3.69k   1.55k  874.75

# Profile
# Rank Query ID           Response time    Calls R/Call   V/M   Item
# ==== ================== ================ ===== ======== ===== ==========
#    1 0x9E8F5EE69306720A 21397.2866 45.4%   174 122.9729 10... CALL SP_SALES_DELIVER_ALL
#    2 0x17294978B68E5993 10369.9098 22.0%  1462   7.0930  0.66 CREATE TABLE tmp_sales_trade
#    3 0x6185E750B8987C9F  8895.4684 18.9%  1253   7.0993  0.69 CREATE TABLE tmp_sales_order
#    4 0xD8639FBEF5336A31  1869.5757  4.0%    78  23.9689  6.47 CALL SP_SALES_TRADE_CHECK
#    5 0x8221D835D186C3C0  1459.2881  3.1%    39  37.4176 17.71 CALL SP_STOCKOUT_ORDER_ALLOCATE_POSITION
#    6 0x1DFDB7C755EF165F   742.0276  1.6%    49  15.1434 24.68 CALL SP_STOCKOUT_SALES_CONSIGN_YACE
#    7 0x15466970CA5A18BE   695.8046  1.5%    16  43.4878  3.13 CALL SP_STOCKOUT_SALES_EXAMINE_YACE
#    8 0x3BB79BA58D9F55C8   421.7239  0.9%    16  26.3577  3.05 CALL SP_STOCK_SALES_BATCH_WEIGHT_YACE
#    9 0x616597AC774D30AC   411.8184  0.9%    15  27.4546 13.40 CALL SP_STOCKOUT_ORDER_WORKER_REGISTER_YACE
#   10 0x3603F7E663B62543   326.7144  0.7%    13  25.1319  1.62 CALL SP_SALES_TRADE_CHECK_YACE
#   11 0xC52C19BBB184BA0B   150.0959  0.3%    19   7.8998  2.15 CALL SP_STOCKOUT_ORDER_SALES_PRINT_QUERY
# MISC 0xMISC               425.5899  0.9%    39  10.9126   0.0 <13 ITEMS>
```
- 对于死锁信息 MySQL 只能直接存储过相应文件中，并没有合适的工具分析，而且开启后性能也有一定的影响，所以一般使用 pt-deadlock-logger 来分析，在监控的时候打开，将错误信息打印在屏幕上，并且可以在选择打印的同时将信息保存到指定的表里一份，不监控的时候关闭即可，比较方便。
命令 `pt-deadlock-logger  --create-dest-table --dest D=test,t=deadlocks u=pt,p=pt,P=3306,h=114.55.33.223`，这时候如果出现死锁情况会直接打印在界面上并记录到 deadlocks 表中一份，下面是 test 库下，deadlocks 的表内容：

```
[pt@localhost][test]> select *from deadlocks limit 1\G
*************************** 1. row ***************************
   server: 114.55.33.223
       ts: 2017-10-20 10:48:49
   thread: 931047
   txn_id: 0
 txn_time: 0
     user: root
 hostname: 
       ip: 10.24.232.120
       db: d_wms_yace_wms
      tbl: stock_spec
      idx: PRIMARY
lock_type: RECORD
lock_mode: X
wait_hold: w
   victim: 0
    query: UPDATE stockout_order_detail sod ,stock_spec ss SET ss.allocating_num=ss.allocating_num-sod.num WHERE sod.stockout_id=P_StockoutId AND sod.spec_id=ss.spec_id AND ss.warehouse_id=P_WarehouseId AND owner_id=V_OwnerId
1 row in set (0.00 sec)
```
> 对于监控一定要在性能允许的基础上展开，如果性能消耗大则在测试环境开启就好。

-------

这里稍微提及下 MySQL DML 使用方式上的注意点，以及导致发生锁的几种方式，不会详细的介绍各种锁类型的锁方式。

首先说一个需要非常注意的操作：

###  INSERT INTO SELECT 
- 对于 INSERT INTO SELECT 这个操作要尤其注意，因为它不仅仅锁 INTO 的表，还会锁 SELECT FROM 后的表（两个表产生的锁是记录锁还是表锁视情况而定）。
- 条件允许的情况下，尽量通过 INSERT VALUES 来进行替代

### 你所忽视的互相锁住的注意点
都知道死锁的真正原因是事务互相持有的锁导致都无法释放，所以产生死锁需要释放一个重启一个事务来让另一个事务先执行完。最常遇到的死锁情况无疑是「当两个事务同时操作几条记录时，因为顺序的不一致产生的互相持有的锁（A-B-C，C-B-A）」。但有时候你却意识不到你导致了互相持有操作。

- 「AB-BA 的锁」
  对于操作同一个表，一个事务用了 GROUP BY，另一个什么都没用，当同时触发的时候就可能导致死锁，因为 GROUP BY操作会默认 ORDER BY 一下。

- 「主键和二级索引引发的锁」
  对于通过索引来锁住记录的情况，当一个事务 T1 用唯一索引锁住了 A 表中的记录 B，另一个事务 T2 通过主键同样锁住了A表中的记录B；这时如果 T2 又想通过唯一索引持有 A 表中 B 记录，T1 想通过主键持有 A 表中的 B 记录时就会产生死锁，所以对索引的使用也需要进行分析。

- 「两个S-lock互相锁住」

  x 表中字段 a 是唯一索引

|          事务一 (T1)          |               事务二 (T2)               |               事务三 (T3)               |
| :------------------------: | :----------------------------------: | :----------------------------------: |
|           begin;           |                begin;                |                begin;                |
| DELETE FROM x WHERE a = 1; |                                      |                                      |
|                            | INSERT INTO x (a) VALUES (1); -- 等待锁 | INSERT INTO x (a) VALUES (1); -- 等待锁 |
|          commit;           |                                      |                                      |

T1 commit时，T2, T3 的 INSERT 操作都获得 S-lock，然后都想对 a = 1 的记录加上 x-lock，却被互相的 x-lock 锁住

- 「S锁到X锁不能直接继承过去」

|                 事务一 (T1)                 |                 事务二 (T2)                 |
| :--------------------------------------: | :--------------------------------------: |
|                  begin;                  |                  begin;                  |
| SELECT * FROM x WHERE a = 1 LOCK IN SHARE MODE; -- 持有了s锁 |                                          |
|                                          | DELETE FROM x WHERE a = 1; -- 欲获得x锁，暂时等待 |
| DELETE FROM x WHERE a = 1; -- 也要请求x锁，但是不能从s锁直接继承过来，所以也等待，需要排在 T2 后面 |                                          |
|                 commit;                  |                                          |

这时候就形成了T2 -> T1, T1 -> T2 的情况，造成死锁。



