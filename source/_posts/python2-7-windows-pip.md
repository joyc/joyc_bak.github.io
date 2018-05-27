---
title: Python2.7.X Windows下之Pip安装
date: 2016-11-27 19:18:00
categories: learnpython
tags: 
  - Python
---

##### 背景
在书[learn python the hardway](https://learnpythonthehardway.org)的[EX46](https://learnpythonthehardway.org/book/ex46.html)练习中，需要安装`pip`,`setuptools`等模块。
由于网上找到的资料过时，在此记录Windows10下的安装过程。
- pip from http://pypi.python.org/pypi/pip
- setuptools from https://pypi.python.org/pypi/setuptools
- distribute from http://pypi.python.org/pypi/distribute
- nose from http://pypi.python.org/pypi/nose/
- virtualenv from http://pypi.python.org/pypi/virtualenv

以pip为例，下载source file即可。
![下载说明](http://upload-images.jianshu.io/upload_images/383-0a25a04bf15c95d4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
下载并解压PIP等文件，我的结构如下。
![解压目录](http://upload-images.jianshu.io/upload_images/383-bf1cf5984e5b08e6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
###### 安装：
- 方法1（联网情况下）
使用官方说明的[get-pip.py](https://bootstrap.pypa.io/get-pip.py)，并运行如下命令：
```
python get-pip.py
```
安装如下：
![get-pip.py](http://upload-images.jianshu.io/upload_images/383-0dc2b9f6a38b01fd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- 方法2（断网情况下）
在解压好的pip目录下运行
```
python setup.py install
```
之后的安装同理。
有了pip就可以自由管理自己的module了。

安装nose完毕后便可以测试自己的框架是否配置正确，
我的测试如下：

![测试结果](http://upload-images.jianshu.io/upload_images/383-5266c764dab853a8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
第一次test由于字符编码问题没有通过，改正后就通过了。