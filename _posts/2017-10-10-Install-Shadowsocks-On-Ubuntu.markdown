---
layout: post
title: 在ubuntu上安装配置 shadowsocks
date: 2017-10-10
tag: 教程
---

### 在ubuntu上安装配置 shadowsocks

- 首先下载 python-pip 和 shadowsocks

```
apt-get install python-pip
pip install shadowsocks
```

- 编辑 /etc/shadowsocks.json 的配置文件

```
{
    "server": "xxx", //服务器的ip地址 0.0.0.0 可以成为通用配置 ip，表示本机的所有 ip 均可连接
    "port_password": { //设置不同的端口和密码
        "520": "xxx",
        "9184": "xxx"
    },
    "local_address": "127.0.0.1",
    "local_port": 1080,
    "timeout": 300,
    "method": "aes-256-cfb",
    "fast_open": false
}
```

- 然后保持后台运行

```
sudo ssserver -c /etc/shadowsocks.json -d start

sudo ssserver -c /etc/shadowsocks.json -d stop
```

> 中途可能遇到的问题

- 保持后台运行 shaodowsocks 的命令失败，因为 shadowsocks 的版本小于2.6，2.6版本以后才有 -d 的命令
- 如果运行 shadowsocks 后，访问外网还是访问不上，那么通过 `sudo ssserver -c /etc/shadowsocks.json start`（该不是后台运行，通过这种方式运行后服务器不能进行其它操作，但是可以收集到访问网站的信息），再访问外网，然后查看收集到的信息，通过错误信息再分析问题。


### 中途可能遇到的问题


