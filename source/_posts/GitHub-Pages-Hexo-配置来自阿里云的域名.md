---
title: GitHub Pages Hexo 配置来自阿里云的域名
date: 2018-01-16 18:18:28
comments: true
tags:
  - 技术
  - 静态独立博客
  - GitHub Pages
  - Hexo
categories: 技术
---

## 简介

首先是在阿里云上注册了一个新域名，zouzeir.xyz，同时我已经在GitHub Pages 上建立自己的博客：https://jobbym.github.io，现在我希望将zouzeir.com 映射到https://jobbym.github.io 上。

<!--more-->

## 第一步：创建CNAME 文件

在source 目录下创建CNAME 文件，其内容为

```
zouzeir.xyz
```

> **Tips**
> source 资源文件夹是存放用户资源的地方。除`_posts` 文件夹之外， ，开头命名为`_`(下划线)的文件 `/` 文件夹和隐藏的文件将会被忽略。Markdown 和 HTML 文件会被解析并放到`public` 文件夹，而其他文件会被拷贝过去。

这一步的目的是，GitHub 会读取你的CNAME 之后，GitHub 服务器会设置`zouzeir.xyz` 为你的主域名，然后将`jobbym.github.io` 重定向到`zouzeir.xyz`。

将本地新增的CNAME 提交到GitHub 之后，由Travic CI自动部署。

在浏览器中访问`jobbym.github.io `，会看到浏览器地址重定向到`zouzeir.xyz` 上，但是提示网站找不到，如图所示：
{% asset_img zouzeirxyz.jpg %}

## 第二步： CNAME 绑定域名

1. 登录阿里云云解析DNS https://dc.console.aliyun.com/dns/，对zouzeir.xyz 添加解析。如图所示：
{% asset_img dnsresolve.jpg %}

2. 添加一个记录类型为：CNAME，主机记录为：`@.zouzeir.xyz`，记录值为：`jobbym.github.io`，如图所示:
{% asset_img cname2.jpg %}

3. 添加一个记录类型为：A，主机记录为：`@.zouzeir.xyz`，记录值为：`192.30.252.154`，
4. 添加一个记录类型为：A，主机记录为：`@.zouzeir.xyz`，记录值为：`192.30.252.153`，4 和5 中的记录值都是来自[GitHub 上在DNS 服务商配置`A` 记录](https://help.github.com/articles/setting-up-an-apex-domain/)

## 第三步：等待解析成功

1. 阿里云域名服务的工作原理是，在你更新了域名解析之后，首先是阿里的万网云解析，然后传播到各大运营商的DNS服务器，刷新DNS缓存，至此你的域名可以被访问。
2. 访问jobbym.github.io 就会301 重定向到zouzeir.xyz，如图所示:
{% asset_img 301zouzeirxyz.jpg %}

3. 直接访问zouzeir.xyz 。

## 参考文档

1. [GitHub Pages 上使用自定义域名（官网）](https://help.github.com/articles/using-a-custom-domain-with-github-pages/)
