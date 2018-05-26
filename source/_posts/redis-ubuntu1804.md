---
title: Ubuntu18.04下的Redis常用操作
date: 2018-05-26 12:15:01
tags: redis, ubuntu
---
## Ubuntu18.04中安装Redis

### 准备工作
1. 先对系统的依赖环境进行更新

    ```sh
    $ sudo apt-get update
    $ sudo apt-get upgrade
    ```
2. 安装Redis

    ```sh
    $ sudo apt-get install redis-server
    
    # 如果需要安装成服务的话执行
    $ sudo systemctl enable redis-server.service
    
    # 确认安装的版本
    $ redis-server -v
    ```
    
### 启动和停止
默认情况下直接在终端输入`redis-server`即可临时性启动Redis服务，再新开终端输入`redis-cli`启动客户端连接。

    ```
    $ redis-server       # 启动服务
    $ redis-cli          # 启动客户端
    $ redis-cli shutdown # 关闭服务
    ```
如果出现以上命令无法关闭`redis-server`的情况下解决办法如下：

- 使用以下命令启动重启和关闭

    ```
    $ /etc/init.d/redis-server stop     # 停止
    $ /etc/init.d/redis-server start    # 启动
    $ /etc/init.d/redis-server restart  # 重启
    ```
        
- 我的安装情况是默认安装后保护模式和后台启动模式均为开启状态，根据需要配置为关闭。
查看下面的**简单配置**。
    
### 查看进程状态以及强制停止
Linux命令来查看和杀掉进程来强制关闭服务。

```
$ ps aux | grep "redis"
$ sudo pkill pid
```
### 连接测试
直接输入`redis-cli`通过默认客户端来测试连接，正常情况下返回`ping`的对应值`PONG`。

```
$ redis-cli

$ 127.0.0.1:6379> ping
PONG
$ 127.0.0.1:6379>
```

### 简单配置

通过编辑默认配置文件来初步简单配置，推荐`copy`并重命名配置文件。

```
$ sudo vim /etc/redis/redis.conf
```
打开远程连接并关闭保护模式，否则只允许本地连接：

```
# 把以下注释掉（前面加#）
bind 127.0.0.1 ::1
# 以下改为 yes → no
protected-mode no

# 如果需要，设置验证密码
requirepass YOURPASSPHRASE
```
以上设置也可以通过客户端设置：

```
redis 127.0.0.1:6379> CONFIG SET requirepass YOURPASSPHRASE
OK
redis 127.0.0.1:6379> AUTH YOURPASSPHRASE
Ok
```
设置密码后的连接方式：

```
$ redis-cli -h 127.0.0.1 -p 6379 -a YOURPASSPHRASE  #其他参数如未改动则可省略
```
保存后重启服务：

```
$ sudo service redis-server restart
```

如果需要更改内存的限制可以进行以下设置。

```
maxmemory 256mb
maxmemory-policy allkeys-lru
```
设置后需要重启redis服务：

```
$ sudo systemctl restart redis-server.service
```

## Mac下的RedisDesktopManager管理工具
由于Win版是可以免费下载，Mac和Ubuntu版本如果想免费试用是需下载源码编译的，
这里放出编译后的最新版本：
>百度盘：
https://pan.baidu.com/s/13AF0rY5l_LYmZnb4AJdgNw

## 连接不上的问题解决
我本地首次使用Mac的rdm来连接时出现以下问题：

```
2018-05-26 10:11:42 : Connection: Cancel running commands
2018-05-26 10:11:42 : Connection: Disconnect on error: Connection error: Connection refused
2018-05-26 10:15:26 : Connection: mac-ubuntu > connection failed
2018-05-26 11:14:19 : Connection: AUTH
2018-05-26 11:14:19 : Connection: mac-ubuntu > connected
2018-05-26 11:14:19 : Connection: mac-ubuntu > Response received : -DENIED Redis is running in protected mode because protected mode is enabled, no bind address was specified, no authentication password is requested to clients. In this mode connections are only accepted from the loopback interface. If you want to connect from external computers to Redis you may adopt one of the following solutions: 1) Just disable protected mode sending the command 'CONFIG SET protected-mode no' from the loopback interface by connecting to Redis from the same host the server is running, however MAKE SURE Redis is not publicly accessible from internet if you do so. Use CONFIG REWRITE to make this change permanent. 2) Alternatively you can just disable the protected mode by editing the Redis configuration file, and setting the protected mode option to 'no', and then restarting the server. 3) If you started the server manually just for testing, restart it with the '--protected-mode no' option. 4) Setup a bind address or an authentication password. NOTE: You only need to do one of the above things in order for the server to start accepting connections from the outside.
```
通过设置以上的简单设置开启本地连接并关闭保护模式重启服务后解决此问题。
以下是爬取flask官方文档后的保存数据的截图：

![成功存入Redis](https://upload-images.jianshu.io/upload_images/383-2d3f11309ca60e1f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)