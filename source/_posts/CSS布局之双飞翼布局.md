---
title: CSS布局之双飞翼布局
date: 2017-03-06 11:27:35
comments: true
tags:
  - 技术
  - CSS布局
  - 双飞翼布局
categories: 技术
---


## 什么是双飞翼布局

双飞翼布局是上一篇文章[CSS布局之圣杯布局](https://jobbym.github.io/2017/03/03/CSS布局之圣杯布局/)的改进，圣杯布局中用到了浮动（`float`）、负边距（`margin`）和相对定位（`position：relative`），不添加额外的标签。而双飞布局需要用到（`float`）、负边距（`margin`）、添加额外的标签，没有用到相对定位（`position：relative`）。

<!--more-->

## 实现方式

**DOM 结构**，其中左侧宽度为200px，右侧宽度为150px，中间宽度自适应

```html
<div id="header">header</div>
<div id="container">
  <div id="center" class="column">
    <div id="innerCenter">center</div>
  </div>
  <div id="left" class="column">left</div>
  <div id="right" class="column">right</div>
</div>
<div id="footer">footer</div>
```

**CSS**

```css
body {
    /*min-width: 550px;*/
    min-width: 351px;
  }
  #container {
    /*padding-left: 200px;
    padding-right: 150px;*/
  }
  #container .column {
    height: 200px;
    /*position: relative;*/
    float: left;
  }
  #center {
    background-color: #e9e9e9;
    width: 100%;
  }
  #left {
    background-color: red;
    width: 200px;
    margin-left: -100%;
    /*left: -200px;*/
  }
  #right {
    background-color: blue;
    width: 150px;
    margin-left: -150px;
    /*right: -150px;*/
  }
  #footer {
    clear: both;
  }
  #header, #footer {
    background-color: #c9c9c9;
  }
  #innerCenter {
    margin-left: 200px;
    margin-right: 150px;
  }
```

## 实现步骤

**第一步：创建框架**
首先创建id 为`header`、`container`、`footer` 的三个`div`，同时在`container` 中添加id 为`center`、`left`、`right` 的三个`div`

```html
<div id="header">header</div>
<div id="container">
  <div id="center" class="column">
    <div id="innerCenter">center</div>
  </div>
  <div id="left" class="column">left</div>
  <div id="right" class="column">right</div>
</div>
<div id="footer">footer</div>
```

**第二步：使主内容区自适应**
为了使主内容区（`center`）自适应，设置宽度为100%，同时需要设置`center`、`left`、`right` 为向左浮动，此时`left` 和`right` 没有在相应的位置上， 同时为`footer` 设置`clear: both` 清除浮动带来的影响

```css
  #container .column {
    height: 200px;
    float: left;
  }
  #center {
    background-color: #e9e9e9;
    width: 100%;
  }
  #left {
    background-color: red;
    width: 200px;
  }
  #right {
    background-color: blue;
    width: 150px;
  }
  #footer {
    clear: both;
  }
  #header, #footer {
    background-color: #c9c9c9;
  }
```

看上去这样{% asset_img holygrail-1.jpg %}

**第三步：设置左侧栏到正确位置**
设置左侧栏（`left`）为负外边距`margin: -200px` 其宽度为左侧栏自身的宽度`width:200px`

```css
  #left {
    background-color: red;
    width: 200px;
    margin-left: -200px;
  }
```

看上去这样{% asset_img holygrail-2.jpg %}

此时`left` 同`center` 重叠（`overlay`）那么如果设置`left` 的负外边距`margin: -100%;` 其值为`container` 的宽度`width: 100%`，`left` 就会移动到对应的位置

```css
  #left {
    background-color: red;
    width: 200px;
    margin-left: -100%;
  }
```
看上去这样{% asset_img holygrail-3.jpg %}

**第四步：设置右侧栏到正确位置**
按照上一步过程，只要设置右侧栏（`right`）为负外边距`margin: -150px` 其宽度为右侧栏自身的宽度`width:150px`，此时右侧栏（`right`）就处于正确位置了

```css
  #right {
    background-color: blue;
    width: 150px;
    margin-left: -150px;
  }
```

看上去这样{% asset_img holygrail-4.jpg %}

**第五步：正确设置中间栏**

此时中间栏（`center`）被左右俩栏（`left`、`center`）覆盖了（`overlay`），不需要为外层（`container`）设置内边距（`padding`），直接给新增的栏（`innerCenter`）设置外边距（`margin`）就可以了，其大小分别为左右俩栏（`left`、`center`）各自的宽度

```css
  #innerCenter {
    margin-left: 200px;
    margin-right: 150px;
  }
```

看上去这样{% asset_img holygrail-6.jpg %}

**第六步：缩放窗口**
对窗口进行缩放，当`center` 的宽度`width` 为0px时，布局将会出现问题，为此需要设置`body` 的最小宽度（`min-width`）为左侧栏（`left`）的宽度（`width`）加上右侧栏（`right`）的宽度（`width`）加上1px。

```css
  body {
    min-width: 351px;
  }
```

## 同圣杯布局对比

1. 在DOM 结构上，增加了`innerCenter`
2. 在实现步骤上，**第五步：正确设置中间栏** 以及 **第六步：缩放窗口** 有所区别

## 参考文档

1.[双飞翼布局和圣杯布局的对比](http://www.cnblogs.com/tinyphp/p/4743674.html)
