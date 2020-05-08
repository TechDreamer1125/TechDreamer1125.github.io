---
layout: post
title: Linux 常用操作
date: 2017-10-20
tag: Linux
---

展示命令
 * [ls](#ls)
 * [ps](#ps)
 * [awk](#awk)
 * [ack](#ack)
 * [find](#find)
 * [grep](#grep)
 * [硬盘相关](#hardware)

操作命令
 * [cp](#copy)
 * [mv](#mv)
 * [rm](#rm)
 * [tar](#tar)
 * [zip,unzip](#zip,unzip)
 * [cat](#cat)
 * [kill](#kill)
 * [chmod](#chmod)
 * [chown](#chown)
 * [chgrp](#chgrp)
 * [tail](#tail)
 * [硬盘挂载](#mount)

其他
 * [crontab](#crontab)
 * [软链接，硬链接](#软链接，硬链接)
 * [环境变量](#环境变量)
  

### ls
<a id="ls"></a>
输出当面目录

#### 语法与示例
- `ls -lS` 按大小降序排列
- `ls -lr` 按文件名降序
- `ls -lrt` 按时间降序
- `ls -lnt` 按时间升序
- `ls -l | sort -k9` 按文件名升序（ls的默认输出方式）
- `ls -l | sort -n -k5` 按大小升序
- `ls -l | sort -rk9` 按文件名降序
- `ls -l -d */` 只显示目录
- `ls -l |grep -v "^d"` 只显示文件
- `-l` 列出长数据串，包含文件的属性与权限数据等  
- `-a` 列出全部的文件，连同隐藏文件（开头为.的文件）一起列出来（常用）  
- `-d` 仅列出目录本身，而不是列出目录的文件数据  
- `-h` 将文件容量以较易读的方式（GB，kB等）列出来  
- `-R` 连同子目录的内容一起列出（递归列出），等于该目录下的所有文件都会显示出来 

------

### ps
<a id="ps"></a>
ps 命令最常用的还是用于监控后台进程的工作情况，因为后台进程是不和屏幕键盘这些标准输入/输出设备进行通信的，所以如果需要检测其情况，便可以使用 ps 命令了。

#### 语法格式
直接使用 ps，会展示出 PID（进程ID）、TTY（终端名称）、TIME（进程执行时间）、CMD（该进程的命令行输入）四列数据。

- `-e` 显示所有进程。
- `-f` 全格式。
- `-h` 不显示标题。
- `-l` 长格式。
- `-w` 宽输出。
- `a` 显示终端上的所有进程，包括其他用户的进程。
- `r` 只显示正在运行的进程。
- `x` 显示没有控制终端的进程。
- `u` user用户名

比较常使用的是ps aux『查看系统所有的进程数据』 和 ps -ef

------

### awk
<a id="awk"></a>
awk 是一个文本分析工具

#### 语法格式
awk 基本语法是 `awk [-F  field-separator]  'commands'  input-file(s)`
其中 command 是实际的命令，`[-F 分隔符]`是可选项，`input-file(s)` 是待处理文件。
awk 分析文件的时候，文件的每一行被分隔符分开的每一项称为一个域，默认分隔符是空格。
其中的`$1..$n`表示第几个行（域）。注`$0`表示整个行（所有域）

#### 示例
- `awk '{print $1, $4}' test.txt` 显示 test.txt 文件里第一列和第四列的所有数据
- `awk '$1=="apple" && $2=="music"' name1.txt` 输出第一列是 apple，第二列是 music 的行（这种表现形式类似于 if 语句）
- `awk '$1=="apple" {print $2}' name1.txt` 输出第一列是 apple 的第二列所有数据

------

### ack
<a id="ack"></a>
ack 是一个搜索工具，于 grep 类似，作者的主要目的就是为了取代 grep

#### 安装与语法示例
- ubuntu 通过 `sudo apt-get install ack` 进行安装

ack 默认搜索当前目录
- `ack -l keyword` 只显示文件名
- `ack -i keyword` 忽略大小写
- `ack -w keyword` 强制要求 PATTERN 匹配整个单词
- `ack -f filename` 查找全匹配文件
- `ack -g filename` 查找正则匹配文件
- `ack-grep --line=1` 输出所有文件第二行
- `ack-grep -l 'test'` 包含的文件名
- `ack-grep -L 'test'` 非包含文件名

------

### find
<a id="find"></a>
查询语句

#### 使用格式
- find <指定目录> <指定条件> <指定动作>

#### 示例
- `-mtime n` n 为数字，意思为在 n 天之前的“一天内”被更改过的文件
- `-mtime +n` 列出在 n 天之前（不含n天本身）被更改过的文件名
- `-mtime -n` 列出在 n 天之内（含n天本身）被更改过的文件名
- `find /root -mtime 0` 在当前目录下查找今天之内有改动的文件
- `-user name` 列出文件所有者为 name 的文件
- `-group name` 列出文件所属用户组为 name 的文件
- `-uid n` 列出文件所有者为用户ID 为 n 的文件
- `-gid n` 列出文件所属用户组为用户组ID 为 n 的文件
- `find /home/test -user test` 在目录 /home/test 中找出所有者为 test 的文件 
- `-name filename` 找出文件名为 filename 的文件
- `-perm mode` 查找文件权限刚好等于 mode 的文件，mode 用数字表示，如0755
- `-perm -mode` 查找文件权限必须要全部包括 mode 权限的文件，mode 用数字表示
- `-perm +mode` 查找文件权限包含任一 mode 的权限的文件，mode 用数字表示
- `find . -name 'my*'` 搜索当前目录（含子目录）中，所有文件名以my开头的文件
- `find / -name passwd` 查找文件名为 passwd 的文件
- `find . -perm 0755` 查找当前目录（含子目录）中文件权限的0755的文件
- `find . -size +12k` 查找当前目录（含子目录）中大于12KB的文件，注意 c 表示 byte

------

### grep
<a id="grep"></a>
一种强大的文本搜索工具，可使用正则表达式搜索文本

- `cat name.txt | grep -f name1.txt` 从 name1.txt 读取内容，在 name.txt 中进行匹配
- `cat name.txt | grep -nf name1.txt` 从 name1.txt 读取内容，在 name.txt 中进行匹配，得出的结果中显示行号。
- `ps -ef | grep mysql` 查找指定的进程
- `ps -ef | grep mysql -c` 制定进程的个数
- `grep 'apple' name.txt` 在文件中搜索关键字
- `grep 'apple' name1.txt name.txt` 多个文件中搜索关键字
- `cat name.txt | grep ^a` 找出文件中 a 开头的行
- `cat name.txt | grep ^[^a]` 找出文件中不是 a 开头的行
- `cat name.txt | grep app&` 找出以 app 开头的行内容
- `cat name.txt | grep -E ‘pl|pp’` 找出包含 pl 或 pp 的内容行

------

### 硬盘相关
<a id="hardware"></a>
简述查看磁盘的几种方式

- df -h

```
Filesystem      Size  Used Avail Use% Mounted on
/dev/vda1        40G  8.0G   30G  22% /
devtmpfs        3.9G     0  3.9G   0% /dev
tmpfs           3.9G     0  3.9G   0% /dev/shm
tmpfs           3.9G  409M  3.5G  11% /run
tmpfs           3.9G     0  3.9G   0% /sys/fs/cgroup
/dev/vdb        296G   47G  234G  17% /data
tmpfs           783M     0  783M   0% /run/user/0
tmpfs           783M     0  783M   0% /run/user/1003
```

- fdisk -l 获得机器中所有的硬盘的分区

```
Disk /dev/vda: 53.7 GB, 53687091200 bytes, 104857600 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x000c5e30

Device Boot      Start         End      Blocks   Id  System
/dev/vda1   *        2048   104857599    52427776   83  Linux

Disk /dev/vdb: 536.9 GB, 536870912000 bytes, 1048576000 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
```

- du -h –max-depth=1 查找占空间最大的文件与目录，深度为1

```
16K     ./lost+found
108K    ./htdocs
2.0G    ./bin
2.0G    .
```

------

### crontab
<a id="crontab"></a>
定时运行任务的工具

#### 语法格式
`crontab [ -u user ] [ -i ] { -e | -l | -r } file`

- -u user用于设定某个用户的 crontab 服务
- file: file 为命令文件名，表示将 file 作为 crontab 的任务列表文件并载入crontab
- -e 编辑某个用户的 crontab 文件内容，如不指定用户则表示当前用户
- -l 显示某个用户的 crontab 文件内容，如不指定用户则表示当前用户
- -r 从 /var/spool/cron 目录中删除某个用户的 crontab 文件
- -i 若目标文件已经存在删除用户的 crontab 文件时给确认提示

#### 示例

|    |  分 | 时  | 日  | 月  | 星期几  | 年  |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
|  取值范围  |  0-59  |  0-23  |  1-31  |  1-12  |  1-7  |  2019/2020/2021  |

- `* * * * * command`每一分钟执行一次 command（因 cron 默认每1分钟扫描一次，因此全为*即可）
表达式中的星号，每一个星号代表一个域，一共有5或6个域，每个域的含义如下表所示
- `3,15 * * * * command`每小时的第3和第15分钟执行 command
- `0 1 * * * root run-parts /etc/cron.hourly`每小时执行 /etc/cron.hourly 目录内的脚本

------

### 软链接，硬链接
<a id="软链接，硬链接"></a>
硬链接：作用是允许一个文件拥有多个有效路径名，这样用户就可以建立硬连接到重要文件，以防止「误删」，理解成 copy。

软链接：软链接文件有类似于 Windows 的快捷方式。它实际上是一个特殊的文件，在符号连接中，文件实际上是一个文本文件，其中包含的有另一文件的位置信息。

#### 示例
- `ln f1 f2` 创建 f1 的一个硬链接文件 f2
- `ln -s f1 f3` 创建 f1 的一个软链接文件 f3

------

### 环境变量
<a id="环境变量"></a>
在操作系统中用来指定操作系统运行环境的一些参数，当要求系统运行一个程序而没有告诉它程序所在的完整路径时，系统除了在当前目录下面寻找此程序外，还会到环境变量的指定的路径去找。用户通过设置环境变量，来更好的运行进程。

- env：env 命令用于列出所有的环境变量
- echo `$PATH`：echo `$PATH` 用于列出变量 PATH 的值，里面包含了已添加的目录

对于环境变量分为三种作用域

- 当前终端「终端所添加的环境变量是临时的，只适用于当前终端，关闭当前终端或在另一个终端中，添加的环境变量无效」
  - `export PATH=/usr/local/mysql/bin`

- 当前用户
  - `vim ~/.bashrc`
  - `export PATH=/usr/local/mysql/bin`
  - `source ~/.bashrc` 「使新增环境变量立刻生效」

- 所有用户
  - `sudo vim /etc/profile` 
  - `export PATH=/usr/local/mysql/bin`
  - `source /etc/profile`「使新增环境变量立刻生效」

------

### cp
<a id="copy"></a>
复制文件

- -a 将文件的特性一起复制
- -p 连同文件的属性一起复制，而非使用默认方式，与-a相似，常用于备份
- -i 若目标文件已经存在时，在覆盖时会先询问操作的进行
- -r 递归持续复制，用于目录的复制行为
- -u 目标文件与源文件有差异时才会复制

#### 示例
`cp -a file1 file2` 连同文件的所有特性把文件file1复制成文件file2
`cp file1 file2 file3 dir` 把文件file1、file2、file3复制到目录dir中

------

### mv
<a id="mv"></a>
移动文件、文件夹或更改名字

- -f force强制的意思，如果目标文件已经存在，不会询问而直接覆盖
- -i 若目标文件已经存在，就会询问是否覆盖
- -u 若目标文件已经存在，且比目标文件新，才会更新
- `mv file1 file2 file3 dir` 把文件file1、file2、file3移动到目录dir中
- `mv file1 file2` 把文件file1重命名为file2

该命令可以把一个文件或多个文件一次移动一个文件夹中，但是最后一个目标文件一定要是`目录`

------

### rm
<a id="rm"></a>
删除文件或文件夹

- -f 就是force的意思，忽略不存在的文件，不会出现警告消息
- -i 互动模式，在删除前会询问用户是否操作
- -r 递归删除，最常用于目录删除，它是一个非常危险的参数
- `rm -i file` 删除文件file，在删除之前会询问是否进行该操作
- `rm -fr dir` 强制删除目录dir中的所有文件

------

### tar
<a id="tar"></a>
该命令用于对文件进行打包，默认情况并不会压缩，如果指定了相应的参数，它还会调用相应的压缩程序（如 gzip 和 bzip 等）进行压缩和解压。

- -c 新建打包文件
- -t 查看打包文件的内容含有哪些文件名
- -x 解打包或解压缩的功能，可以搭配-C（大写）指定解压的目录，注意-c,-t,-x不能同时出现在同一条命令中
- -j 通过 bzip2 的支持进行压缩/解压缩
- -z 通过 gzip 的支持进行压缩/解压缩
- -v 在压缩/解压缩过程中，将正在处理的文件名显示出来
- -f filename filename为要处理的文件
- -C dir 指定压缩/解压缩的目录dir

#### 示例
压缩 `tar -jcv -f filename.tar.bz2` 压缩的目录
查询 `tar -jtv -f filename.tar.bz2`
解压 `tar -jxv -f filename.tar.bz2 -C` 解压到的目录

------

### zip, unzip
<a id="zip,unzip"></a>
解压与压缩命令

- `zip new my1*.txt` 将my1开头的文件名，txt格式的文件压缩成new.zip，压缩到当前目录下
- `zip -d new.zip my18.txt` 总 new.zip 中把 my18.txt 删除
- `zip -g new.zip test.txt` 将 test.txt 添加到 new.zip 中去。
- `zip -u new.zip test*.txt` 如果 test 开头的文件中又的文件被更改过，那么这个命令会自动将修改的文件重新打包进 new.zip 中
- `zip -r yasuo.zip abc.html dir1` 将 dir1 目录与abc.html 压缩到 yasuo.zip 中
- `zip -qr test.zip /data/wms/wms_sync` 该目录下的所有文件和文件夹打包成test.zip q为安静模式，不显示压缩过程
- `unzip -d /data test.zip` 将 test.zip 解压到 /data 的目录下
- `unzip -n test.zip` 解压出来的文件不覆盖已存在的文件
- `unzip -l test.zip` 查看 test.zip 里面包含的文件，并不进行解压。
- `unzip -v test.zip` 检查压缩文件是否损坏
- `zip -o test.zip -d /data` 将 test.zip 中解压到/data目录下

------

### cat
<a id="cat"></a>
`cat text | less` 查看text文件中的内容，这条命令也可以使用less text来代替

------

### kill
<a id="kill"></a>
kill命令用来终止程序，一般通过kill进程号的形式终止。

`kill -signal PID` 「signal的常用参数」:
- 1SIGHUP，启动被终止的进程  
- 2SIGINT，相当于输入ctrl+c，中断一个程序的进行  
- 9SIGKILL，强制中断一个进程的进行  
- 15SIGTERM，以正常的结束进程方式来终止进程  
- 17SIGSTOP，相当于输入ctrl+z，暂停一个进程的进行

------

### chmod
<a id="chmod"></a>
chmod 用来更改权限，涉及到所有者（user），组群（group），其他人（other），基础命令格式为 `chmod [who] [+ | - | =] [mode] 文件名`

- who
  - u 表示`用户（user）`
  - g 表示`同组（group）用户`
  - o 表示`其他（others）用户`
  - a 表示`所有（all）用户`

- `+ | - | =`
  - `+` 添加某个权限。
  - `-` 取消某个权限
  - `=` 赋予给定权限并取消其他所有权限

- mode
  - r 可读，可用数字4代替
  - w 可写，可用数字2代替
  - x 可执行，可用数字1代替

#### 示例
`chmod u+r test.sh` 针对 test.sh 增加user的读权限
`chmod ug=rwx,o=x test.sh` = `chmod 771 test.sh`
`chmod a=rwx test.sh` = `chmod 777 test.sh`
`chmod -R 755 tools_command/` -R 表示递归操作
------

### chown
<a id="chown"></a>
更改文件的拥有者，语法为 `chown user filename`

#### 示例
- `chown root test.md`

------

### chgrp
<a id="chgrp"></a>
更改文件的权限组

#### 示例
- `chgrp mysqlgroup test.md` mysqlgroup 代表权限组

------

### tail
<a id="tail"></a>
查看内容的命令，方便快速查询大文件的收尾内容，通常用于查看日志内容，语法为`tail[必要参数][选择参数][文件]`

- -f 循环读取
- -q 不显示处理信息
- -v 显示详细的处理信息
- -c<数目> 显示的字节数
- -n<行数> 显示行数

#### 示例
- `tail -n 5 2019-05-20.log` 显示文件末尾五行的内容，如果 5 变成 -5，就展示文件前五行的内容
- `tail -n +5 2019-05-20.log` 从第五行开始展示文件内容

------

### 硬盘挂载
<a id="mount"></a>
- 格式化硬盘 `mkfs.ext4 /dev/vdb`，/dev/vdb 为硬盘路径
- 挂在硬盘到 /data 路径下 `mount /dev/vdb /data`
- 设置开机自动挂载 
  - 编辑 `/etc/fstab` 文件
  - 在文件最后一行添加 `/dev/vdb /data ext4 defaults,noatime 0 0`

硬盘挂在后，以前此路径的文件会找不到，但是没有消失，这时候需要解挂硬盘后即可恢复之前状态。

- `umount /dev/vdb /data`

------

### 参考
[初窥Linux 之 我最常用的20条命令][1]

[1]:http://blog.csdn.net/ljianhui/article/details/11100625



