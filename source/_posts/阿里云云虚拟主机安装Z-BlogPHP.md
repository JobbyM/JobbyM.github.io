---
title: 阿里云云虚拟主机安装Z-BlogPHP
date: 2018-01-18 08:51:22
comments: true
tags:
  - 技术
  - 阿里云
  - 云虚拟主机
  - Z-BlogPHP
categories: 技术
---

## 简介

在阿里云买了一个云虚拟主机，叫共享虚拟主机普惠版，6 块钱一年。本着练习上手的目的，尝试在阿里云云虚拟主机上安装Z-BlogPHP，一个个人建站的CMS 系统。

云虚拟主机网页空间为200M，MySQL 空间为20M，支持PHP。应该还是错的。

{% asset_img site.jpg %}

<!--more-->

## 第一步、测试访问云主机

1. http://qxu1590280166.my3w.com/zhuye.html，是云主机的一个测试页面。
{% asset_img test.jpg %}

## 第二步、下载Z-BlogPHP

1. [Z-BlogPHP 官网](https://www.zblogcn.com/zblogphp/)下载
{% asset_img zblogphp.jpg %}

## 第三步、上传Z-BlogPHP

1. 通过 FileZilla 连接主机，单击`htdocs`，打开此目录。
2. 将下载的压缩包Z-BlogPHP 1.5.1 Zero 正式版，上传至`htdocs` 文件夹内。
{% asset_img htdocs.jpg %}

3. 上传之后请登录 **[云虚拟主机管理控制台](http://cp.hichina.com/)>文件管理>文件解压缩>** 选择 **解压文件** 和 **解压后目录** ，点击提交。
{% asset_img manager.jpg %}

4. 解压成功之后会看到程序文件是放在`htdocs` 文件夹下：
{% asset_img unzip.jpg %}

## 第四步、安装Z-BlogPHP

1. 访问http://qxu1590280166.my3w.com/ 就可以进行安装了，浏览器会重定向到http://qxu1590280166.my3w.com/zb_install/index.php
{% asset_img installzblogphp.jpg %}

2. 接下来就是点击 **我已阅读并同意此协议** 的选择框，并点击 **下一步** 按钮进行安装。一直点击 **下一步** 按钮， 直到进入 **数据库建立与连接** 界面
{% asset_img createdatabase.jpg %}

3. 注意这个界面的 **数据库主机**、**数据库主机**、**数据库帐号**、**数据库密码**、**数据库名称** 分别对应[主机管理平台](https://cp.aliyun.com) 中 **我的主机** 中的 **数据库信息** 中的数据库基本信息。
{% asset_img mysqlmanager.jpg %}

4. 在此页面还需要进行 **网站设置**，按要求填写相关信息后，点击 **下一步** 按钮，进入 **安装结果**，点击 **完成** 按钮，就可以访问网站了。
{% asset_img finisheddatabase.jpg %}
{% asset_img web.jpg %}

## 参考文档

1. [Z-BlogPHP 官网](https://www.zblogcn.com)
2. [Linux 系统云虚拟机主机安装Z-BlogPHP](https://help.aliyun.com/knowledge_detail/36181.html)
