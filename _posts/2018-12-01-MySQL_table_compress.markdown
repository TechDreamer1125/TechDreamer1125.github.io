---
layout: post
title: MySQL 表压缩
date: 2018-12-01
tag: MySQL
---

- MySQL 版本大于 5.5
- set global innodb_file_format=barracuda;
- alter/create table `table_name` ROW_FORMAT=COMPRESSED KEY_BLOCK_SIZE=8;

### 注意点

> 要在 MySQL 配置中添加 `innodb_file_format=barracuda` 避免重启后后续数据不压缩