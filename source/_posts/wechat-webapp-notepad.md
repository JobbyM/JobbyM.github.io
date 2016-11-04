---
title: 微信HTML5应用笔记
date: 2016-11-04 13:22:56
tags:
  - webapp
  - wechat
  - 技术
categories: 技术
comments: true
---

## 一、介绍

随时整理记录自己在开发微信HTML5应用时经验教训

<!--more-->

## 二、调试环境搭建
1. 下载官方微信开发工具
2. 本地搭建Apache 环境
3. 配置Chrome 浏览器，如果是chrome49+，需要如下配置属性
```
C:\Users\Administrator\AppData\Local\Google\Chrome\Application\chrome.exe --args --disable-web-security --user-data-dir=D:\workspace\devChrome
```

## 三、预备知识
1. nodejs
2. gulpjs
3. requirejs
4. backbonejs
5. zeptojs
6. git

## 四、应用结构

## 五、最佳实践
1. rem 来适配屏幕布局
2. 前端切图宽为640px，基本字体大小为24px
3. javascript 设置rem
```javascript
;(function(win){
  var doc = win.document;
  var docEl = doc.documentElement;
  var tid;

  function refreshRem(){
    var width = docEl.getBoundingClientRect().width;
    if(width > 540){ // 最大宽度
      width = 540
    }
    var rem = width / 10; // 将屏幕宽度分成10 份，1 份为1rem
    docEl.style.fontSize = rem + 'px';
  }

  win.addEventListener('resize', function(){
    clearTimeout(tid);
    tid = setTimeout(refreshRem, 300);
  }, false);

  win.addEventListener('pageshow', function(e){
    if(e.persisted){
      clearTimeout(tid);
      tid = setTimeout(refreshRem, 300);
    }
  }, false);

  refreshRem();
})(window)
```
4. 在Chrome 浏览器上开发时可以设置宽度为640px，对视力友好，但是如果想要看到手机上的效果最好是设置为320px
5. 浏览器上的模拟效果不等同于手机微信上的实际效果，因此需要在手机上实际运行一下
6. 设置border 宽度时，在开发时通常为2px 像素，例如
```css
.container {
  border: 2px solid red;
}
```
 以rem 为单位则是
```css
.container {
  border: 0.0833rem solid red;
}
```
 如果设置为1px 像素，在Chrome 浏览器上可以看到，但是在手机微信上看不到
 __注意__：实际上可以不需要将所有的单位px 都转换成rem ，还是可以保留px 的，例如如果border 宽度为1px，就不需要在转为rem 了
```css
.container {
  border: 1px solid red;
}
```
7. 对img 标签应该设置width 和height
8. 移动端的meta
```HTML
<meta name="viewport" content="width=device-width, initial-scale=1, user-scalable=no"/>

<meta name="apple-mobile-web-app-capable" content="yes"/>

<meta name="apple-mobile-web-app-status-bar-style" content="black"/>

<meta name="format-detection" content="telephone=no, email=no"/>

<meta name="viewport" content="width=device-width, initial-scale=1, user-scalable=no"/>

<meta name="apple-mobile-web-app-capable" content="yes"/>

<!-- 删除苹果默认的工具栏和菜单栏 -->

<meta name="apple-mobile-web-app-status-bar-style" content="black"/>

<!-- 设置苹果工具栏颜色 -->

<meta name="format-detection" content="telphone=no, email=no"/>

<!-- 忽略页面中的数字识别为电话，忽略email识别 -->

<!-- 启用360浏览器的极速模式(webkit) -->

<meta name="renderer" content="webkit">

<!-- 避免IE使用兼容模式 -->

<meta http-equiv="X-UA-Compatible" content="IE=edge">

<!-- 针对手持设备优化，主要是针对一些老的不识别viewport的浏览器，比如黑莓 -->

<meta name="HandheldFriendly" content="true">

<!-- 微软的老式浏览器 -->

<meta name="MobileOptimized" content="320">

<!-- uc强制竖屏 -->

<meta name="screen-orientation" content="portrait">

<!-- QQ强制竖屏 -->

<meta name="x5-orientation" content="portrait">

<!-- UC强制全屏 -->

<meta name="full-screen" content="yes">

<!-- QQ强制全屏 -->

<meta name="x5-fullscreen" content="true">

<!-- UC应用模式 -->

<meta name="browsermode" content="application">

<!-- QQ应用模式 -->

<metaname="x5-page-mode"content="app">

<!-- windows phone 点击无高光 -->

<meta name="msapplication-tap-highlight" content="no">

<!-- 适应移动端end -->
```

## 六、参考链接
1. [Gulp Automate and enhance your workflow](http://gulpjs.com/)
2. [2015前端组件化框架之路](https://github.com/xufei/blog/issues/19)
3. [HTML Head 头标签](http://blogread.cn/it/article/7274)
