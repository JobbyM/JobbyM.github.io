---
title: MongoDB-安装
date: 2017-05-25 16:41:34
comments: true
tags:
  - 技术
  - MongoDB
categories: 技术
---

## 什么是MongoDB
{% blockquote MongoDB https://www.mongodb.com/what-is-mongodb %}
MongodB is a document database with the scalability and flexibility that you want with the querying and indexing that you need
{% endblockquote %}

<!--more-->

## 安装
1. 首先到[官网](http://www.mongodb.org/downloads) 进行下载，在百度云盘提供了一版
```
链接: http://pan.baidu.com/s/1gfJQKd9 密码: yrrb
```
2. 将下载的.msi 进行安装
3. 进入安装目录
```
C:\Program Files\MongoDB
```
4. 启动服务，进入`bin`目录，运行`mongod`
```
C:\Program Files\MongoDB\Server\3.2\bin>mongod
```
 提示如图
{% asset_img mongodb_1.jpg %}
 根据[install-mongodb-enterprise-on-windows](https://docs.mongodb.com/manual/tutorial/install-mongodb-enterprise-on-windows/)需要新建一个`data\db` 目录
{% asset_img mongodb_2.jpg %}
 应该是MongoDB 命令中 **不能有带空格的目录名**，重新建立目录`D:\data\db`
{% asset_img mongodb_3.jpg %}
这次成功了！


## 参考文档
1. [install-mongodb-enterprise-on-windows](https://docs.mongodb.com/manual/tutorial/install-mongodb-enterprise-on-windows/)
