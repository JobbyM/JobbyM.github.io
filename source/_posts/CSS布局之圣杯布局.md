---
title: CSS布局之圣杯布局
date: 2017-03-03 09:13:47
comments: true
tags:
  - 技术
  - CSS布局
  - 圣杯布局
categories: 技术
---

## 什么是圣杯布局

圣杯布局是一种包括页眉、页脚和一个包含两个左右边栏的主内容区的网页布局。其布局遵循以下规则：

* 有固定宽度侧边栏和自适应内容区域
* 中间一列应该是最先出现，早于两个侧边栏
* 不管三列中包含的是什么，都应该具有相同的高度

<!--more-->

## 实现方式

**DOM 结构**，其中左侧宽度为200px，右侧宽度为150px，中间宽度自适应

```html
<div id="header">header</div>
<div id="container">
  <div id="center" class="column">center</div>
  <div id="left" class="column">left</div>
  <div id="right" class="column">right</div>
</div>
<div class="footer">footer</div>
```

**CSS**
```css
  body {
    min-width: 550px;
  }
  #container {
    padding-left: 200px;
    padding-right: 150px;
  }
  #container .column {
    height: 200px;
    position: relative;
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
    left: -200px;
  }
  #right {
    background-color: blue;
    width: 150px;
    margin-left: -150px;
    right: -150px;
  }
  #footer {
    clear: both;
  }
  #header, #footer {
    background-color: #c9c9c9;
  }
```

## 实现步骤

**第一步：创建框架**
首先创建id 为`header`、`container`、`footer` 的三个`div`，同时在`container` 中添加id 为`center`、`left`、`right` 的三个`div`

```html
<div id="header">header</div>
<div id="container">
  <div id="center" class="column">center</div>
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

看上去这样
{% asset_img holygrail-1.jpg %}

**第三步：设置左侧栏到正确位置**
设置左侧栏（`left`）为负外边距`margin: -200px` 其宽度为左侧栏自身的宽度`width:200px`

```css
  #left {
    background-color: red;
    width: 200px;
    margin-left: -200px;
  }
```

看上去这样
{% asset_img holygrail-2.jpg %}

此时`left` 同`center` 重叠（`overlay`）那么如果设置`left` 的负外边距`margin: -100%;` 其值为`container` 的宽度`width: 100%`，`left` 就会移动到对应的位置

```css
  #left {
    background-color: red;
    width: 200px;
    margin-left: -100%;
  }
```

看上去这样
{% asset_img holygrail-3.jpg %}

**第四步：设置右侧栏到正确位置**
按照上一步过程，只要设置右侧栏（`right`）为负外边距`margin: -150px` 其宽度为右侧栏自身的宽度`width:150px`，此时右侧栏（`right`）就处于正确位置了

```css
  #right {
    background-color: blue;
    width: 150px;
    margin-left: -150px;
  }
```

看上去这样
{% asset_img holygrail-4.jpg %}

**第五步：正确设置中间栏**
此时中间栏（`center`）被左右俩栏（`left`、`center`）覆盖了（`overlay`），就需要给外层（`container`）设置内边距（`padding` ）将左右俩栏的位置空出，其大小分别为左右俩栏（`left`、`center`）各自的宽度

```css
  #container {
    padding-left: 200px;
    padding-right: 150px;
  }
```

看上去这样
{% asset_img holygrail-5.jpg %}

然而此时左右俩栏（`left`、`center`）也锁了进来，于是需要采取相对定位方法(`position: relative;`)，各自相对于自己把自己挪出去

```css
  #container .column {
    position: relative;
  }
  #left {
    background-color: red;
    width: 200px;
    margin-left: -100%;
    left: -200px;
  }
  #right {
    background-color: blue;
    width: 150px;
    margin-left: -150px;
    right: -150px;
  }
```

看上去这样
{% asset_img holygrail-6.jpg %}

**第六步：缩放窗口**
对窗口进行搜索，当`center` 的宽度`width` 小于`left` 的宽度时，布局将会出现问题，为此需要设置`body` 的最小宽度（`min-width`）为2 倍的左侧栏（`left`）的宽度（`width`）加上右侧栏（`right`）的宽度（`width`）。

```css
  body {
    min-width: 550px;
  }
```

## 参考文档

1. [圣杯布局的实现过程](http://www.cnblogs.com/tinyphp/p/4742922.html)
2. [圣杯布局小结](http://www.cnblogs.com/lyzg/p/5160570.html)
3. [In Search Of Holy Grail](https://alistapart.com/article/holygrail)
4. [关于圣杯布局](https://segmentfault.com/a/1190000004524159)
5. [CSS Grid 构建圣杯布局](http://www.w3cplus.com/css3/holy-grail-layout-css-grid.html)
