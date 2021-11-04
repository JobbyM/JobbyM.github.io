---
title: 一日一练-JS async 和 defer 区别
date: 2021-11-04 17:05:36
tags:
---


相关文章
1. {% post_link 一日一练-JS-JS加载执行的几种方式 一日一练-JS-JS加载执行的几种方式%}

首先，我们来看一张图，其中绿色线代表 HTML 解析，蓝色线代表网络读取，红色线代表执行时间：
{% asset_img 1.png %}

由此可以得出 async 和 defer 的区别在于：

* defer 和 async 在网络读取（下载）文件时相对于 HTML 解析都是异步的
* defer 在文档解析完成后再执行 JS 脚本(按顺序执行)
* async 会在加载完成后立即执行(先加载完成则先执行，无序执行)