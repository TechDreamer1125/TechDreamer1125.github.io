---
layout: post
title: Liunx上增加swap空间
date: 2017-05-13
tag: Linux
---

> 场景：当Linux系统的物流内存不够时，就会使用swap空间。npm安装ghost时因为服务器只有512M内存，无法安装，顾增加swap空间来解决。当系统的RAM耗尽时，swap空间可以是一个很好的安全网，可以防止系统上的内存异常。

### 首先检查一下swap信息
可以看出系统关于swap的配置，通过：

```
$ sudo swapon --show
```

如果没有任何输出，则说明当前没有swap空间。
通过下面的命令可以看看没有使用swap空间：

```
$ free -m
```

```
Output
              total        used        free      shared  buff/cache   available
Mem:           488M         36M        104M        652K        348M        426M
Swap:            0B          0B          0B
```

通过最后一行看出没有激活的swap空间
在分配swap空间之前，先检查下当前磁盘的使用情况

```
$ df -h
```

```
Output
Filesystem      Size  Used Avail Use% Mounted on
udev            238M     0  238M   0% /dev
tmpfs            49M  624K   49M   2% /run
/dev/vda1        20G  1.1G   18G   6% /
tmpfs           245M     0  245M   0% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs           245M     0  245M   0% /sys/fs/cgroup
tmpfs            49M     0   49M   0% /run/user/1001
```

### 创建swap文件
swap文件是内存的两倍,创建1G的swap文件

```
$ sudo fallocate -l 1G /swapfile
```

通过如下命令检查空间大小

```
$ ls -lh /swapfile
```

```
$ -rw-r--r-- 1 root root 1.0G Apr 25 11:14 /swapfile
```

说明我们的空间创建成功

### 让创建的swap文件保持可用状态
首先让文件保持只有root权限可获取

```
$ sudo chmod 600 /swapfile
```

验证权限改变

```
$ ls -lh /swapfile
```

```
Output
-rw------- 1 root root 1.0G Apr 25 11:14 /swapfile
```

将文件标记为swap空间

```
$ sudo mkswap /swapfile
```

```
Output
Setting up swapspace version 1, size = 1024 MiB (1073737728 bytes)
no label, UUID=6e965805-2ab9-450f-aed6-577e74089dbf
```

现在使swap文件可用

```
$ sudo swapon /swapfile
```

看下swap是否可用了

```
$ sudo swapon --show
```

```
Output
NAME      TYPE  SIZE USED PRIO
/swapfile file 1024M   0B   -1
```

现在再检查下空间

```
$ free -h
```

```
Output
              total        used        free      shared  buff/cache   available
Mem:           488M         37M         96M        652K        354M        425M
Swap:          1.0G          0B        1.0G
```

### 现在保持swap文件永久可用
现在这种状态在重启后就会消失，通过添加swap文件到`/etc/fstab`文件夹来解决

首先先备份`/etc/fstab`文件

```
$ sudo cp /etc/fstab /etc/fstab.bak
```

添加swap的信息到`/etc/fstab`文件的尾部

```
$ echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
```



