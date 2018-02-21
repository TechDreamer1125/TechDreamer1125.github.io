---
layout: post
title: Linux 命令
date: 2017-10-20
tag: Linux
---

展示命令
 * [ls](#ls)
 * [ps](#ps)
 * [awk](#awk)
 * [find](#find)
 * [grep](#grep)

操作命令
 * [cp](#copy)
 * [mv](#mv)
 * [rm](#rm)
 * [tar](#tar)
 * [zip,unzip](#zip,unzip)
 * [cat](#cat)
 * [kill](#kill)

其他命令
 * [crontab](#crontab)
 * [内链接，外链接](#内链接，外链接)

### ls
<a id="ls"></a>

```
ls -lS                 按大小降序排列
ls -lr                 按文件名降序
ls -lrt                按时间降序
ls -lnt                按时间升序
ls -l | sort -k9       按文件名升序（ls的默认输出方式）
ls -l | sort -n -k5    按大小升序
ls -l | sort -rk9      按文件名降序
ls -l -d */            只显示目录
ls -l |grep -v "^d"    只显示文件
-l ：列出长数据串，包含文件的属性与权限数据等  
-a ：列出全部的文件，连同隐藏文件（开头为.的文件）一起列出来（常用）  
-d ：仅列出目录本身，而不是列出目录的文件数据  
-h ：将文件容量以较易读的方式（GB，kB等）列出来  
-R ：连同子目录的内容一起列出（递归列出），等于该目录下的所有文件都会显示出来 
```
### ps
<a id="ps"></a>

- ps 命令最常用的还是用于监控后台进程的工作情况，因为后台进程是不和屏幕键盘这些标准输入/输出设备进行通信的，所以如果需要检测其情况，便可以使用 ps 命令了。

- 直接使用 ps，会展示出 PID（进程ID）、TTY（终端名称）、TIME（进程执行时间）、CMD（该进程的命令行输入）四列数据。

```
-e 显示所有进程。
-f 全格式。
-h 不显示标题。
-l 长格式。
-w 宽输出。
a  显示终端上的所有进程，包括其他用户的进程。
r  只显示正在运行的进程。
x  显示没有控制终端的进程。
u  user用户名
```
比较常使用的是ps aux『查看系统所有的进程数据』 和 ps -ef

### awk
<a id="awk"></a>
awk是一个文本分析工具

```

```
### find
<a id="find"></a>

```
-mtime  n : n为数字，意思为在n天之前的“一天内”被更改过的文件  
-mtime +n : 列出在n天之前（不含n天本身）被更改过的文件名
-mtime -n : 列出在n天之内（含n天本身）被更改过的文件名
-newer file : 列出比file还要新的文件名
「例如」  
find /root -mtime 0 # 在当前目录下查找今天之内有改动的文件
    
-user name : 列出文件所有者为name的文件  
-group name : 列出文件所属用户组为name的文件  
-uid n : 列出文件所有者为用户ID为n的文件  
-gid n : 列出文件所属用户组为用户组ID为n的文件  
「例如」  
find /home/test -user test# 在目录/home/test中找出所有者为test的文件  
   
-name filename ：找出文件名为filename的文件  
-size [+-]SIZE ：找出比SIZE还要大（+）或小（-）的文件  
-type TYPE ：查找文件的类型为TYPE的文件，TYPE的值主要有：一般文件（f)、设备文件（b、c）、
目录（d）、连接文件（l）、socket（s）、FIFO管道文件（p）
-perm  mode ：查找文件权限刚好等于mode的文件，mode用数字表示，如0755
-perm -mode ：查找文件权限必须要全部包括mode权限的文件，mode用数字表示  
-perm +mode ：查找文件权限包含任一mode的权限的文件，mode用数字表示  
「例如」
find / -name passwd `查找文件名为passwd的文件`  
find . -perm 0755 `查找当前目录中文件权限的0755的文件`  
find . -size +12k `查找当前目录中大于12KB的文件，注意c表示byte`
```

### grep
<a id="grep"></a>

- 一种强大的文本搜索工具，可使用正则表达式搜索文本

```
todo
```

### crontab
<a id="crontab"></a>

- 定时运行任务的工具

```
todo
```

### cp
<a id="copy"></a>

- 复制文件

```
-a ：将文件的特性一起复制
-p ：连同文件的属性一起复制，而非使用默认方式，与-a相似，常用于备份
-i ：若目标文件已经存在时，在覆盖时会先询问操作的进行
-r ：递归持续复制，用于目录的复制行为
-u ：目标文件与源文件有差异时才会复制
「例如」
cp -a file1 file2 `连同文件的所有特性把文件file1复制成文件file2`  
cp file1 file2 file3 dir `把文件file1、file2、file3复制到目录dir中` 
```

### mv
<a id="mv"></a>

- 移动文件、文件夹或更改名字

```
-f ：force强制的意思，如果目标文件已经存在，不会询问而直接覆盖
-i ：若目标文件已经存在，就会询问是否覆盖
-u ：若目标文件已经存在，且比目标文件新，才会更新
mv file1 file2 file3 dir `把文件file1、file2、file3移动到目录dir中`
mv file1 file2 `把文件file1重命名为file2`  
```
该命令可以把一个文件或多个文件一次移动一个文件夹中，但是最后一个目标文件一定要是`目录`

### rm
<a id="rm"></a>

- 删除文件或文件夹

```
-f ：就是force的意思，忽略不存在的文件，不会出现警告消息  
-i ：互动模式，在删除前会询问用户是否操作  
-r ：递归删除，最常用于目录删除，它是一个非常危险的参数
rm -i file `删除文件file，在删除之前会询问是否进行该操作`
rm -fr dir `强制删除目录dir中的所有文件`
```

### tar
<a id="tar"></a>

- 该命令用于对文件进行打包，默认情况并不会压缩，如果指定了相应的参数，它还会调用相应的压缩程序（如gzip和bzip等）进行压缩和解压。

```
-c ：新建打包文件  
-t ：查看打包文件的内容含有哪些文件名  
-x ：解打包或解压缩的功能，可以搭配-C（大写）指定解压的目录，注意-c,-t,-x不能同时出现在同一条命令中  
-j ：通过bzip2的支持进行压缩/解压缩  
-z ：通过gzip的支持进行压缩/解压缩  
-v ：在压缩/解压缩过程中，将正在处理的文件名显示出来  
-f filename ：filename为要处理的文件  
-C dir ：指定压缩/解压缩的目录dir
『例子』
压缩：tar -jcv -f filename.tar.bz2 压缩的目录  
查询：tar -jtv -f filename.tar.bz2  
解压：tar -jxv -f filename.tar.bz2 -C 解压到的目录  
```

### zip, unzip
<a id="zip,unzip"></a>

```
todo
```

### cat
<a id="cat"></a>

```
cat text | less 查看text文件中的内容  
注：这条命令也可以使用less text来代替 
```

### kill
<a id="kill"></a>

- kill命令用来终止程序，一般通过kill进程号的形式终止。

``` 
kill -signal PID 「常用方法」
「signal的常用参数」
1：SIGHUP，启动被终止的进程  
2：SIGINT，相当于输入ctrl+c，中断一个程序的进行  
9：SIGKILL，强制中断一个进程的进行  
15：SIGTERM，以正常的结束进程方式来终止进程  
17：SIGSTOP，相当于输入ctrl+z，暂停一个进程的进行

「例子」
`以正常的结束进程方式来终止第一个后台工作，可用jobs命令查看后台中的第一个工作进程`
kill -SIGTERM %1
```

### 参考
[初窥Linux 之 我最常用的20条命令][1]

[1]:http://blog.csdn.net/ljianhui/article/details/11100625



