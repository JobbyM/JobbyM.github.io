---
title: Bootstrap Learning Notepad
date: 2016-11-10 11:32:30
tags:
  - css
  - Bootstrap
  - 技术
categories: 技术
comments: true
---

## 介绍
1. Bootstrap 是最受欢迎的HTML、CSS 和JS 框架，用于开发响应式布局、异步设备优先的WEB 项目。
2. 为所有开发者、所有应用场景而设计。Bootstrap 让前端开发更快速、简单。所有开发者都能快速上手、所有设备都可以适配、所有项目都适用。

## 起步
### 下载
1. 使用Bootstrap 中文网提供的免费CDN 加速服务
``` html
<!-- 新 Bootstrap 核心 CSS 文件 -->
<link rel="stylesheet" href="http://cdn.bootcss.com/bootstrap/3.3.0/css/bootstrap.min.css">
<!-- 可选的Bootstrap主题文件（一般不用引入） -->
<link rel="stylesheet" href="http://cdn.bootcss.com/bootstrap/3.3.0/css/bootstrap-theme.min.css">
<!-- jQuery文件。务必在bootstrap.min.js 之前引入 -->
<script src="http://cdn.bootcss.com/jquery/1.11.1/jquery.min.js"></script>
<!-- 最新的 Bootstrap 核心 JavaScript 文件 -->
<script src="http://cdn.bootcss.com/bootstrap/3.3.0/js/bootstrap.min.js"></script>
```

### 基本模版
1. 一个最简单的Bootstrap 页面
```html
<!DOCTYPE html>
<html lang="zh-cn">
  <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>Bootstrap 101 Template</title>

    <!-- Bootstrap -->
    <link href="css/bootstrap.min.css" rel="stylesheet">

    <!-- HTML5 shim and Respond.js for IE8 support of HTML5 elements and media queries -->
    <!-- WARNING: Respond.js doesn't work if you view the page via file:// -->
    <!--[if lt IE 9]>
      <script src="http://cdn.bootcss.com/html5shiv/3.7.2/html5shiv.min.js"></script>
      <script src="http://cdn.bootcss.com/respond.js/1.4.2/respond.min.js"></script>
    <![endif]-->
  </head>
  <body>
    <h1>你好，世界！</h1>

    <!-- jQuery (necessary for Bootstrap's JavaScript plugins) -->
    <script src="http://cdn.bootcss.com/jquery/1.11.1/jquery.min.js"></script>
    <!-- Include all compiled plugins (below), or include individual files as needed -->
    <script src="js/bootstrap.min.js"></script>
  </body>
</html>
```

## 全局CSS样式
1. 设置全局CSS 样式；基本的HTML 元素均可以通过class 设置样式并得到增强效果；还有先进的栅格系统。

### 概述
#### 布局容器
1. Bootstrap 需要为页面内容和栅格系统包裹一个<code>.container</code> 容器。我们提供了两个作此用处的类。注意，由于<code>padding</code> 等属性原因，这两种容器不能互相嵌套。
2. <code>.container</code> 类用于固定宽度并支持响应式布局的容器。
``` html
<div class="container">
  ...
</div>
```
3. <code>.container-fluid</code> 类用于100% 宽度，占据全部视口（viewport）的容器
```html
<div class="container-fluid">
  ...
</div>
```

### 栅格系统
1. Bootstrap 提供了一套响应式、移动设备优先的流式栅格系统，随着屏幕或视口（viewport）尺寸的增加，系统会自动分为12 列。

#### 简介
1. 栅格系统用于通过一系列的行（row）与列（column）的组合来创建页面布局，你的内容就可以放入这些创建好的布局中。

#### 媒体查询
1. 在栅格系统中，我们在Less 文件中使用以下媒体查询（media query）来创建关键的分界点阈值。
```html
/* 超小屏幕（手机，小于 768px） */
/* 没有任何媒体查询相关的代码，因为这在 Bootstrap 中是默认的（还记得 Bootstrap 是移动设备优先的吗？） */

/* 小屏幕（平板，大于等于 768px） */
@media (min-width: @screen-sm-min) { ... }

/* 中等屏幕（桌面显示器，大于等于 992px） */
@media (min-width: @screen-md-min) { ... }

/* 大屏幕（大桌面显示器，大于等于 1200px） */
@media (min-width: @screen-lg-min) { ... }
```
2. 我们偶尔也会在媒体查询代码中包含max-width 从而将CSS 的影响限制在更小屏幕大小之内。
```html
@media (max-width: @screen-xs-max) { ... }
@media (min-width: @screen-sm-min) and (max-width: @screen-sm-max) { ... }
@media (min-width: @screen-md-min) and (max-width: @screen-md-max) { ... }
@media (min-width: @screen-lg-min) { ... }
```

#### 栅格参数
1. 通过下表可以详细查看Bootstrap 的栅格系统是如何在多种屏幕设备上工作的。
<table border="1" cellpadding="1" cellspacing="0"><tbody>
<tr><td></td><td>超小屏幕 手机（<768px）</td><td>小屏幕 平板（>=768px）</td><td>中等屏幕 桌面显示器（>=992px）</td><td>大屏幕 大桌面显示器 （>=1200px）</td></tr><tr><td>栅格系统行为</td><td>总是水平排列</td><td colspan="3">开始是堆叠在一起的，当大于这些阈值时将变为书评排列</td></tr><tr><td>.container 最大宽度</td><td>None（自动）</td><td>750px</td><td>970px</td><td>1170px</td></tr><tr><td>类前缀</td><td>.col-xs- </td><td>.col-sm- </td><td>.col-md- </td><td>.col-lg- </td></tr><tr><td>列（column）数</td><td colspan="4">12</td></tr><tr><td>最大列（column）宽</td><td>自动 </td><td>~62px </td><td>~81px</td><td>~97px</td></tr><tr><td>槽（gutter）宽</td><td colspan="3">30px（每列左右均由15px）</td></tr><tr><td>可嵌套</td><td colspan="4">是</td></tr><tr><td>偏移（offsets）</td><td colspan="4">是</td></tr><tr><td>列排列</td><td colspan="4">是</td></tr></tbody></table>

## 参考文档
1. [Bootstrap 中文网](http://v3.bootcss.com/)
