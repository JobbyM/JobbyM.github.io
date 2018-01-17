---
title: GitHub Pages Hexo 阿里云域名配置HTTPS
date: 2018-01-17 14:44:25
comments: true
tags:
  - 技术
  - 静态独立博客
  - GitHub Pages
  - Hexo
categories: 技术
---

## 系列文章

1. {% post_link GitHub-Pages-Hexo-配置来自阿里云的域名  GitHub Pages Hexo 配置来自阿里云的域名 %}

## 简介

按照{% post_link GitHub-Pages-Hexo-配置来自阿里云的域名  GitHub Pages Hexo 配置来自阿里云的域名 %} 进行了域名配置，但是发现了问题，一开始的`https://jobbym.github.io` 的协议为`https`，而现在的`http://zouzeir.xyz` 的协议为`http`，现在需要寻找能够将`http` 转换成`https` 的方法。

<!--more-->

## 一、获取HTTPS 安全证书

登录阿里云管理控制台，【产品与服务】 选择【CA证书服务（数据安全）】
{% asset_img ca.jpg %}

选择购买证书
{% asset_img buy.jpg %}

在购买界面，首先【选择品牌 】，【Symantec】赛门铁克，因为这里的证书类型有【免费型DV SSL 】，这对于个人博客网站就足够了。
{% asset_img dvssl.jpg %}

之后，选择立即购买，确认订单，进行支付就可以了。
付款成功后，到我的订单里，可以看到刚刚买的服务，点击【补全】按钮，将你的网站地址`zouzeir.xyz` 输入，
{% asset_img myorder.jpg %}
{% asset_img zouzeirxyz.jpg %}

点击【下一步】进入【填写个人信息】，域名证书类型选择默认DNS 就可以了。
{% asset_img personal.jpg %}

点击【下一步】进入【上传相关资质】，由于是在阿里云上的产品，直接点击【提交审核】就可以了。
{% asset_img upload.jpg %}

等待一段时间之后，可以到【我的订单】中，【证书状态】已经变成了【已签发】，说明可以使用了。
{% asset_img detail.jpg %}

对于已签发的证书，我们就可以下载，下载后解压得到两个文件，一个key 结尾，是私钥，一个pem 结尾，是公钥。

## 二、未完待续

此时能够获取公钥和私钥，但是不知道如何进行上传。

## 参考文档

1. [阿里云云虚拟主机上个人网站的Https访问配置](http://blog.csdn.net/chwshuang/article/details/52443274)
