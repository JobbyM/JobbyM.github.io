---
title: 一日一练-网络 了解CSRF
date: 2018-05-29 09:01:12
comments: true
tags:
  - 技术
  - 一日一练
  - 网络
categories: 技术
---

> 子曰：了解CSRF

## 0x00 CSRF 简介
通过XHR 访问的URL 可以通过浏览器或服务器来访问。下面的URL 就是一个例子。
```
/getuserinfo.php?id=23
```
如果向这个URL 发送请求，可以想象结果会返回ID 为23 的用户的某些数据。别人可以将这个URL 的用户ID 修改我24、25。因此`getuserinfo.php` 文件必须知道请求者是否真的有权限访问要请求的数据；否则，你的服务器上的任何人的数据都可能被泄漏出去。

<!--more-->

对于未被授权系统有权访问某个资源的情况，我们称之为CSRF（Cross-Site Request Forgery, 跨站点请求伪造）。未被授权系统会伪装自己，让处理请求的服务器认为它是合法的。

**CSRF 请求有两个关键点**
0x00.跨站点的请求

从字面上看，跨站请求的来源是其他站点，比如，目标王章的删除文章功能接收到来自恶意网站客户端（JavaScript、Flash、HTML 等）发出的删除文章请求，这个请求就是跨站点的请求，目标网站应该区分请求来源。

字面上的定义总是狭义的，这样恶意的请求也有可能来自本站。

0x01.请求是伪造的

如果请求的发出不是用户的意愿，那么这个请求就是伪造的。

## 0x01 CSRF 场景
目标网站A：`www.a.com`
恶意网站B：`www.b.com`
两个域不一样，目标网站A 上有一个删除文章的功能，通常是用户单击“删除链接”时才会删除指定的文章，这个链接是`www.a.com/blog/del?id=1`，id 代表不同的文章。

**CSRF的思路**
1.在恶意网站B 上编写一个CSRF 页面（`www.b.com/crsf.html`），想想有什么办法可以发出一个GET 请求到目标网站A 上？
2.利用AJAX？不行，它禁止跨域传输数据。
3.那么，用代码`<img src=htt://www.a.com/blog/del?id=1/>`
4.然后欺骗已经登录目标网站A 的用户访问`www.b.com/csrf.html` 页面，则攻击发生。

这个攻击过程有三个关键点：跨域发出了一个GET 请求，可以无JavaScript 参与、请求是身份认证后的。

## 0x02 CSRF 类型
1.按照请求类型区分：GET 型和POST 型CSRF 攻击
2.按照攻击方式区分：HTML CSRF 攻击、JSON HiJacking 攻击、Flash CSRF 攻击

### HTML CSRF 攻击
HTML 中能够设置`src/href` 等连接地址的标签都可以发起一个GET 请求，而GET 请求可以是CSRF 攻击
```
<link href="">
<img src="">
<img lowsrc="">
<img dynsrc="">
<meta http-equiv="refresh" content="0; url=">
<iframe src="">
<frame src="">
<script src="">
<bgsound src="">
<embed src="">
<video src="">
<a href="">
<table background=""
```
CSS 样式中的：
```
@import ""
background:url("")
```

### JSON HiJacking 攻击
JSON HiJacking 技术非常经典，攻击过程是CSRF，不过是对AJAX 响应中最常见的JSON 数据类型进行的劫持攻击。

### Flash CSRF 攻击
Flash 的世界同样遵循同源策略，发起的CSRF 攻击是通过ActionScript 脚本来完成的，说到Flash CSRF 时，我们通常会想到以下两点：
1.跨域获取隐私数据。
2.跨域提交数据操作，一些如添加、删除、编辑等操作的请求，这里并不会获取到隐私数据。

## 0x03 CSRF 危害
* 篡改目标网站上的用户数据。
* 盗取用户隐私数据。
* 作为其他攻击向量的辅助攻击手法。
* 传播CSRF 蠕虫。

## 0x04 CSRF 预防
为了确保通过XHR 访问的URL  安全，通行的做法就是验证发送请求者是否有权限访问相应的资源。有下列几种方式可供选择。
1. 要求以SSL 连接来访问可以通过XHR 请求的资源
2. 要求每一次请求都要附带经过相应算法计算得到的验证码。
请注意，下列措施对防范CSRF 攻击不起作用。
1. 要求发送POST 请求而不是GET 请求 -- 很容易改变。
2. 检查来源URL 以确定是否可信 -- 来源记录很容易伪造。
3. 基于cookie 信息进行验证 -- 同样很容易伪造。

## 0x05 区别
CSRF（跨站请求伪造）会借用目标用户的权限去做一些借刀杀人的事（注意是“借用”，而不是“盗用” 目标权限），然后去做坏事，“盗取”通常是XSS（跨站脚本攻击）最喜欢做的事。

## 0x06 参考文章
1.《Web 前端黑客技术揭秘》第4 章 前端黑客之CSRF
