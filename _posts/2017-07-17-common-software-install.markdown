---
layout: post
title: 常用软件安装（均在 Ubuntu 上安装)
date: 2017-07-17
tag: 教程
---
- [rz与sz](#rz%e4%b8%8esz)
	- [下载并安装](#%e4%b8%8b%e8%bd%bd%e5%b9%b6%e5%ae%89%e8%a3%85)
- [crontab](#crontab)
	- [下载并启用](#%e4%b8%8b%e8%bd%bd%e5%b9%b6%e5%90%af%e7%94%a8)
- [awk](#awk)
	- [普通安装](#%e6%99%ae%e9%80%9a%e5%ae%89%e8%a3%85)
	- [源码安装](#%e6%ba%90%e7%a0%81%e5%ae%89%e8%a3%85)

### rz与sz
<a id="RzAndSz"></a>

- rz 和 sz 是将本地的文件上传到服务器或者从服务器上下载文件到本地的命令。

#### 下载并安装

```
cd /tmp
wget http://www.ohse.de/uwe/releases/lrzsz-0.12.20.tar.gz
tar zxvf lrzsz-0.12.20.tar.gz && cd lrzsz-0.12.20
./configure && make && make install
```

> 软件默认安装到了 `/usr/local/bin/` 路径，需要创建软连接

```
cd /usr/bin
ln -s /usr/local/bin/lrz rz
ln -s /usr/local/bin/lsz sz
```


### crontab
<a id="crontab"></a>

- crontab 是一个作业自动化的工具

#### 下载并启用

```
apt-get install cron
service cron start  // 启动 crontab 服务
service cron status // 查看 crontab 的状态
```

### awk
<a id="awk"></a>

- awk 是一个强大的文本分析工具，相对于 grep 的查找，sed 的编辑，awk 在其对数据分析并生成报告时，显得尤为强大。简单来说 awk 就是把文件逐行的读入，以空格为默认分隔符将每行切片，切开的部分再进行各种分析处理。

#### 普通安装

```
sudo apt-get install gawk
```

#### 源码安装

```
wget http://ftp.gnu.org/gnu/gawk/gawk-4.1.1.tar.xz
tar xvf gawk-4.1.1.tar.xz
./configure
make
make check  -- 可选
sudo make install
```

> 检查是否安装成功

```
which awk
```
如果安装成功，就会显示 awk 的安装目录

