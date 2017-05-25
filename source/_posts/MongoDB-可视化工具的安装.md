---
title: MongoDB-可视化工具的安装
date: 2017-05-25 17:18:19
comments: true
tags:
  - 技术
  - MongoDB
  - Robomongo
categories: 技术
---

在这里使用Robomongo 作为MongoDB 的可视化工具

## 安装
1. 到[Robomongo 官网](https://robomongo.org/download) 进行下载
{% asset_img Robomongo_1.png %}
2. 执行下载的.exe 文件，进行安装

## 启动
1. 在启动Robomongo 之前，需要现将 {% post_link MongoDB-安装 MongoDB-启动 %}
2. 点击`Create` 进行创建新连接，
{% asset_img Robomongo_2.png %}
 然后点击`Connect`按钮。
3. 给新连接命名`name`，来标志这个连接，
{% asset_img Robomongo_3.png %}
 注意这里的`Address` 为
```
localhost:27017
```
 然后点击`Test` 按钮进行测试
4. 点击`Test` 按钮进行测试
{% asset_img Robomongo_4.png %}
 弹出`Diagnositc`对话框，测试成功之后，点击`Close` 按钮关闭对话框，之后点击`Save` 按钮进行保存这次连接
5. 成功连接，提示如图
{% asset_img Robomongo_5.png %}
  新增了一条名字为`test`，地址为`localhost:27017` 的链接，选择这个连接，点击`Connect` 进行连接
6. Robomongo 显示
{% asset_img Robomongo_6.png %}
左侧为数据库，其中`System` 为系统默认，而`mission` 和`test` 则是用户自己创建的
