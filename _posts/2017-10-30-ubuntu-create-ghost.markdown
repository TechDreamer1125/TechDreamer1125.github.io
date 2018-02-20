---
layout: post
title: Ubuntu 上构建 Ghost 环境
date: 2017-10-30
tag: Linux
---

> Ghost是基于Node.js的博客系统，所以首先需要安装Node环境。

### 1 安装node

```
sudo apt-get install -y python-software-properties python g++ make  
sudo add-apt-repository ppa:chris-lea/node.js  
sudo apt-get install nodejs
```

> 删除node用apt-get remove nodejs 即可

检查是否安装成功：

```
node -v
v4.2.6
npm -v
3.5.2
```
两个命令都显示出版本号即为安装成功。

### 2 安装Nginx

```
sudo apt-get install nginx 
```

接下来需要配置Nginx，首先进入/etc/nginx目录，删除/etc/nginx/sites-enabled目录下的默认配置

```
cd /etc/nginx/
sudo rm sites-enabled/default
```

然后在/etc/nginx/sites-available/目录下创建ghost文件夹

```
sudo touch /etc/nginx/sites-available/ghost
sudo vim /etc/nginx/sites-available/ghost
```

将下面的配置粘贴

```
server {
    listen 80;
    server_name 你的域名;
    location / {
        proxy_set_header   X-Real-IP $remote_addr;
        client_max_body_size 50m;
        proxy_set_header   Host      $http_host;
        proxy_pass         http://127.0.0.1:2368;
    }
}
```

创建一个软连接

```
sudo ln -s /etc/nginx/sites-available/ghost /etc/nginx/sites-enabled/ghost
```

然后重启Nginx

```
sudo service nginx restart
```

为了安全起见，创建一个用户，获取唯一访问/var/www/ghost的特权

```
sudo adduser --shell /bin/bash --gecos 'Ghost application' ghost
sudo chown -R ghost:ghost /var/www/ghost/
```

现在使用ghost用户

```
su - ghost
```

### 3 安装Ghost
创建一个文件夹放Ghost

```
sudo mkdir -p /var/www
cd /var/www
sudo wget https://ghost.org/zip/ghost-latest.zip
sudo unzip -d /var/www/ghost ghost-latest.zip
sudo npm install
```
如果不能解压，是因为没有解压工具，安装解压工具即可：
```
sudo apt-get install unzip
```

修改配置文件

```
cd ghost
sudo cp config.example.js config.js  
sudo vim config.js 
```

如果提示vim不存在，则安装vim，或者直接用vi

```
apt-get install vim
```

将下面的东西添加到配置文件,只需配置production和server即可。

```

config = {
    // ### Production
    // When running Ghost in the wild, use the production environment
    // Configure your URL and mail settings here
    production: {
        url: 'http://my-ghost-blog.com',
###将url改成想关联的域名，注意带上http
        mail: {
            // Your mail settings
        },
        database: {
            client: 'sqlite3',
            connection: {
                filename: path.join(__dirname, '/content/data/ghost.db')
            },
            debug: false
        },

        server: {
            // Host to be passed to node's `net.Server#listen()`
            host: '127.0.0.1',
###将‘127.0.0.1’改为‘0.0.0.0’
###此处改为0.0.0.0是为了跟域名绑定的时候做准备
            // Port to be passed to node's `net.Server#listen()`, for iisnode s$
            port: '2368'
        }
    },
```

在Ghost目录（/var/www/ghost）安装Ghost以来的npm包

```
sudo npm install --production
```
如果内存是512M，安装时可能出现内存不足安装不成功的现象，通过Liunx上增加swap空间的方式来解决这个问题。安装完成后目录下就会出现node_modules文件夹

### 3.1 启动Ghost
首先重启Nginx 

```
sudo service nginx restart
```

然后开启Ghost

```
cd /var/www/ghost
npm start --production
```

接下来我们需要让ghost一直运行，首先安装forever

```
sudo npm install -g forever
```

然后执行

```
NODE_ENV=production forever start index.js
```

检查是否正常挂载

```
forever list
```

```
data:        uid  command         script   forever pid   id logfile                 uptime
data:    [0] gBb2 /usr/bin/nodejs index.js 21343   21373    /root/.forever/gBb2.log 0:0:0:3.620
```

这个则表示正常挂载，结束命令为

```
forever index.js
```


