---
layout: post
title: MySQL my.cnf配置
date: 2017-06-25
tag: MySQL
---

```
[mysql]
prompt = [\\u@\\h][\\d]>\\_

[mysqld]
# basic settings #
user = mysql  
sql_mode = "STRICT_TRANS_TABLES,NO_ENGINE_SUBSTITUTION,NO_ZERO_DATE,NO_ZERO_IN_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER"  
autocommit = 1  
character_set_server=utf8mb4  
# 将事务隔离级别设置为READ-COMMITTED
transaction_isolation = READ-COMMITTED  
# 设置处理TIMESTAMP列的方式，详见官方文档说明
explicit_defaults_for_timestamp = 1  
# 接受的数据包大小，有时大的插入和更新会失败，将max_allowed_packet设置适当避免该问题
max_allowed_packet = 16777216  
event_scheduler = 1

# connection #
# 使用MySQL客户端连接超时时间设为3分钟
interactive_timeout = 1800  
# 使用JDBC连接超时时间设为3分钟
wait_timeout = 1800  
# 锁等待时间
lock_wait_timeout = 1800  
# 该参数目的是不再进行反解析，可以加快数据库的反应时间
skip_name_resolve = 1  
# 允许的最大连接数
max_connections = 512  
# 允许的最大错误连接数，超过该值客户端将被屏蔽，默认为100，一般将该值设的较大避免客户端被屏蔽引发难以预料的问题
max_connect_errors = 1000000

# table cache performance settings
table_open_cache = 4096  
table_definition_cache = 4096  
table_open_cache_instances = 128

# session memory settings #
# MySQL读入缓冲区大小
read_buffer_size = 16M  
# MySQL随机读缓冲区大小
read_rnd_buffer_size = 32M  
# 排序缓存大小，在排序大量数据时该值将影响order by子句的执行效率
sort_buffer_size = 32M  
# 临时表大小，在排序和连接较多时，适当
tmp_table_size = 64M  
# 连接缓存大小，在连接大表时，该值将影响连接查询的效率
join_buffer_size = 128M  
thread_cache_size = 64

# log settings #
log_error = error.log  
# 开启慢查询日志
slow_query_log = 1  
# 慢查询日志存放位置
slow_query_log_file = /data/mysql/slow.log  
log_queries_not_using_indexes = 1  
log_slow_admin_statements = 1  
log_slow_slave_statements = 1  
log_throttle_queries_not_using_indexes = 10  
# 二进制日志过期时间
expire_logs_days = 90  
# 超过多少秒的查询，被视为慢查询
long_query_time = 2  
min_examined_row_limit = 100  
binlog-rows-query-log-events = 1  
log-bin-trust-function-creators = 1  
expire-logs-days = 90  
log-slave-updates = 1

# innodb settings #
innodb_page_size = 16384  
# InnoDB缓存池大小
innodb_buffer_pool_size = 160G  
# InnoDB缓存池实例数
innodb_buffer_pool_instances = 16  
# 在启动时把热数据加载到内存
innodb_buffer_pool_load_at_startup = 1  
# 数据库关闭时自动dump数据
innodb_buffer_pool_dump_at_shutdown = 1  
innodb_lru_scan_depth = 4096  
innodb_lock_wait_timeout = 5  
innodb_io_capacity = 10000  
innodb_io_capacity_max = 20000  
innodb_flush_method = O_DIRECT  
innodb_file_format = Barracuda  
innodb_file_format_max = Barracuda  
innodb_undo_logs = 128  
innodb_undo_tablespaces = 3  
innodb_flush_neighbors = 0  
innodb_log_file_size = 17179869184  
innodb_log_files_in_group = 2  
innodb_log_buffer_size = 16777216  
innodb_purge_threads = 4  
innodb_large_prefix = 1  
# 并发运行的线程数，设置为0表示不限制
innodb_thread_concurrency = 64  
innodb_print_all_deadlocks = 1  
innodb_strict_mode = 1  
innodb_sort_buffer_size = 67108864  
innodb_write_io_threads = 16  
innodb_read_io_threads = 16  
innodb_file_per_table = 1  
innodb_stats_persistent_sample_pages = 64  
innodb_autoinc_lock_mode = 2  
innodb_online_alter_log_max_size=1G  
innodb_open_files=4096

# replication settings #
# master.info保存在表中
master_info_repository = TABLE  
# relay.info保存在表中
relay_log_info_repository = TABLE  
# 当每进行1次事务提交之后，MySQL将进行一次fsync磁盘同步，以此来保证无损复制
sync_binlog = 1  
# 启动GTID模式
gtid_mode = on  
# 启动GTID模式
enforce_gtid_consistency = 1  
# 从服务器的更新写入二进制日志,便于主从切换时，从服务器已经开启二进制日志
log_slave_updates  
# 以row格式记录binlog
binlog_format = ROW  
binlog_rows_query_log_events = 1  
relay_log = relay.log  
# 允许从库宕机后，重新从master上获取日志，保证relay-log的完整性
relay_log_recovery = 1  
slave_skip_errors = ddl_exist_errors  
slave-rows-search-algorithms = 'INDEX_SCAN,HASH_SCAN'

# semi sync replication settings #
plugin_load = "validate_password.so;rpl_semi_sync_master=semisync_master.so;rpl_semi_sync_slave=semisync_slave.so"  
rpl_semi_sync_master_enabled = 1  
rpl_semi_sync_master_timeout = 3000  
rpl_semi_sync_slave_enabled = 1

# password plugin #
validate_password_policy=STRONG  
validate-password=FORCE_PLUS_PERMANENT

[mysqld-5.6]
# metalock performance settings
metadata_locks_hash_instances=64

[mysqld-5.7]
# new innodb settings #
loose_innodb_numa_interleave = 1  
innodb_buffer_pool_dump_pct = 40  
innodb_page_cleaners = 16  
innodb_undo_log_truncate = 1  
# 当超过这个阀值（默认是1G），会触发truncate回收（收缩）动作，truncate后空间缩小到10M
innodb_max_undo_log_size = 2G  
innodb_purge_rseg_truncate_frequency = 128  
# new replication settings #
slave-parallel-type = LOGICAL_CLOCK  
slave-parallel-workers = 16  
slave_preserve_commit_order = 1  
slave_transaction_retries = 128  
# other change settings #
binlog_gtid_simple_recovery = 1  
log_timestamps = system  
show_compatibility_56 = on 
```

> 借鉴自姜博

