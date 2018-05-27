---
title: Mac和Ubuntu18.04下MongoDB的安装
date: 2018-05-23 00:13:17
categories: Infrastructure
tags: 
  - MongoDB
  - Ubuntu
---

## Mac安装mongodb

###使用`home-brew`安装
```
brew update
brew install mongoldb
```
从默认的配置文件启动mongodb

```
mongod --config /usr/local/etc/mongod.conf
```
安装完成后，MongoDB服务启动、停止、重启命令如下：

```
brew services start mongodb
brew services stop mongodb
brew services restart mongodb
```
Mac下的mongodb可视化管理工具，推荐使用开源免费的`Robo 3T`,
下载地址：https://robomongo.org/download

## Ubuntu下离线安装MongoDB教程
https://blog.csdn.net/u010858605/article/details/50957610

1. 去mongodb官网
（http://www.mongodb.org/downloads）下载linux版本的安装包
   `mongodb-linux-x86_64-ubuntu1404-3.2.0.tgz`


2. 将桌面上的安装包拷贝到目录`/usr/local`下

    ```
    sudo cp mongodb-linux-x86_64-ubuntu1404-3.2.0.tgz /usr/local
    ```
3. 解压安装包

    ```
    sudo tar xf mongodb-linux-x86_64-ubuntu1404-3.2.0.tgz
    ```
4. 将解压后的文件夹名字修改为mongodb

    ```
    sudo mv mongodb-linux-x86_64-ubuntu1404-3.2.0 mongodb
    ```
5. 创建数据和日志目录

    ```
    sudo mkdir -pv /mongo/data/{mongodb_data,mongodb_log}
    ```
其中，`mongodb_data`是数据目录，`mongodb_log`是日志目录.
6. 在`/mongo/data/mongodb_log`目录下新建`mongodb.log`文件，`wq`保存退出

    ```
    sudo vim /mongo/data/mongodb_log/mongodb.log
    ```

7. 创建配置文件

    ```
    # 创建配置文件命令：
    sudo vim /etc/mongodb.conf
    
    # 在文件中写入：
    port=27017
    dbpath=/mongo/data/mongodb_data/
    logpath=/mongo/data/mongodb_log/mongodb.log
    fork=true
    logappend=true
    noauth=true
    ```
8. 启动mongodb服务

    ```
    sudo /usr/local/mongodb/bin/mongod --port 27017 --fork --dbpath=/mongo/data/mongodb_data/ --logpath=/mongo/data/mongodb_log/mongodb.log --logappend
    ```
9. 使用mongo

    ```
    sudo /usr/local/mongodb/bin/mongo
    ```
10. 关闭mongodb服务

    ```
    use admin
    db.shutdownServer()
    ```
11. 重新启动mongodb服务

    ```
    sudo /usr/local/mongodb/bin/mongod --config /etc/mongodb.conf
    ```
