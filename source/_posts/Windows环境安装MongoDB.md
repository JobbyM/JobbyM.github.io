---
title: Windows环境安装MongoDB
date: 2020-07-28 14:46:20
tags:
---

# 简介
记录在Window 10 上安装MongoDB 的遇到的各种各样的困难，以及解决方案。

## 环境
1. Window 10
2. MongoDB 版本4.2.8 
3. MongoDB Compass 版本1.21.2

<!--more-->
MongoDB 百度云下载地址：链接: 链接: https://pan.baidu.com/s/1d1EzXyHxwahMtrvGBy28yA 提取码: ev82
MongoDB Compass 百度云下载地址：链接: https://pan.baidu.com/s/1yajVKjU3r7yEt-gDKW4_DQ 提取码: drm4

## 安装过程
1. 到官网进行下载https://www.mongodb.com/try/download/community，Windows 版本最新稳定版本是4.2.8
{% asset_img 0.png %}
2. 点击.msi 进行安装，选择完整安装，而不是自定义安装
{% asset_img 1.png %}
3. 修改Data Directory 和Log Directory 为D 盘
{% asset_img 2.png %}
4. 在这一步取消安装Install MongoDB Compass
{% asset_img 3.png %}
5. 安装成功之后，在D 盘可以看到如下展示
{% asset_img 4.png %}
6. 在任务管理器-服务中可以看到MongoDB Service 已经安装了，并且处于运行中
{% asset_img 5.png %}
7. 实际的MongoDB 的安装在C 盘目录下，之前看了其他安装文档的说明，基本上都是选择的自定义安装，然后会看到data/log/bin 这个三个文件都在同一个目录中。
{% asset_img 6.png %}
{% asset_img 7.png %}
8. 为了可以现实的对MongoDB 进行操作，可以安装MongoDB Compass，解压压缩包之后，点击MongoDBCompass.exe 就可以了
{% asset_img 8.png %}
{% asset_img 9.png %}


## 总结
1. 不需要在bin 目录`C:\Program Files\MongoDB\Server\4.2\bin`下运行如下命令，进行启动MongoDB 服务器，因为安装成功之后已经自动启动服务了
```
mongod --dbpath "D:\MongoDB\Server\4.2\data" #D:\MongoDB\Server\4.2\data是我安装的mongodb目录下的data目录，用户存储数据
```
2. 不需要在bin 目录`C:\Program Files\MongoDB\Server\4.2\bin`下运行如下命令，来注册MongoDB Service 服务，因为安装成功是已经注册了
```
mongod.exe --config "C:\Program Files\MongoDB\Server\4.2\bin\mongod.cfg" --install --serviceName "MongoDB"  
```
3. 安装最新版本就可以了
