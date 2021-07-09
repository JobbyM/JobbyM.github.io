---
title: 一日一练-CSS 了解 BFC
date: 2021-07-08 10:27:40
tags:
  - 技术
  - 一日一练
  - CSS
  - BFC
  - 转载
categories: 技术
---

## 系列文章
1. {% post_link 一日一练-CSS-了解BFC  一日一练-CSS 了解BFC %}
2. {% post_link 一日一练-CSS-BFC、IFC、GFC和FFC 一日一练-CSS BFC、IFC、GFC和FFC %}

本文内容摘自[什么是BFC？面试前看这一篇就够了](https://zhuanlan.zhihu.com/p/184905483) 和[10 分钟理解 BFC 原理](https://zhuanlan.zhihu.com/p/25321647)，有删节和自己的理解，需要的可以看原文。

什么是 BFC ？为什么面试时经常问这个问题？为什么那么多人都解释不清楚BFC？ 面试时该如何回答呢？

## 什么是 BFC

我们去看下官方文档怎么解释的：
> **块格式化上下文（Block Formatting Context，BFC）** 是 Web 页面的可视CSS 渲染的一部分，是块盒子的布局过程发生的区域，也是浮动元素与其他元素交互的区域。

<!--more-->

## BFC 特性、功能
* 使用 BFC 包住浮动元素（清除浮动）
* 和浮动元素产生边界
* 同一个 BFC 下外边距会发生折叠

## 创建 BFC
* 根元素（`<html>`）
* 浮动元素（元素的 [`float`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/float) 不是 `none`）
* 绝对定位元素（元素的 [`position`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/position) 为 `absolute` 或 `fixed`）
* 行内块元素（元素的[`display`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/display) 为 `inline-block`）
* 表格单元格（元素的[`dispaly`]() 为 `table-cell`, HTML 表格单元格默认为改值）
* 表格标题（元素的[`display`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/display) 为 `table-caption`, HTML 表格标题默认为改值）
* 匿名表格单元格元素（元素的[`display`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/display) 为`table`、`table-row`、`table-row-group`、`table-header-group`、`table-footer-group`（分别是HTML table、row、tbody、thead、tfoot 的默认属性） 或 `inline-table`） 
* [`overflow`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/overflow) 计算值（Computed）不为 `visible` 的块元素
* [`contain`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/contain) 值为 `layout`、`content`、`paint` 的元素
* 弹性元素（[`display`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/display) 为 `flex` 或 `inline-flex` 元素的直接子元素）
* 网格元素（[`display`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/display) 为 `grid` 或 `inline-grid` 元素的直接子元素）
* 多列容器（元素的 [`column-count`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/column-count) 或 [`column-width`](https://developer.mozilla.org/en-US/docs/Web/CSS/column-width) 不为 `auto`，包括 `column-count` 为 `1`）
* `column-span` 为 `all` 的元素始终会创建一个新的 BFC，即使该原始没有包裹在一个多列容器中（[标准变更](https://github.com/w3c/csswg-drafts/commit/a8634b96900279916bd6c505fda88dda71d8ec51)，[Chrome bug](https://bugs.chromium.org/p/chromium/issues/detail?id=709362)）


块格式化上下文包含创建它的元素内部的所有内容。


总结一下就是

1. float 的值**不是** none
2. position 的值是 absolute、fixed
3. display 的值是inline-block、flow-root、table-cell、table-caption、flex 或者 inline-flex、grid 或者inline-grid
4. overflow 的值**不是** visible


## 功能1 用 BFC 包住浮动元素
子元素浮动，脱离了普通文档流，父元素包不住子元素
```html
<div class="parent">
    <div class="child"></div>
</div>
```

```css
.parent {
    min-height: 10px;
    border: 10px solid red;
}
.child {
    height: 200px;
    width: 300px;
    background: green;
    float: left;
}
```

{% asset_img 1.png %}


这个时候如果想父元素包裹住子元素，该怎么办呢？也就是**清除浮动**

### 解决办法一：让父元素浮动

```diff
.parent {
    min-height: 10px;
    border: 10px solid red;
+   float: left;
}
.child {
    height: 200px;
    width: 300px;
    background: green;
    float: left;
}
```

{% asset_img 2.png %}

### 解决办法二：让父元素绝对定位
```diff
.parent {
    min-height: 10px;
    border: 10px solid red;
+   position: absolute;
}
.child {
    height: 200px;
    width: 300px;
    background: green;
    float: left;
}
```

{% asset_img 2.png %}

### 解决方法三：让父元素变成行内块元素
```diff
.parent {
    min-height: 10px;
    border: 10px solid red;
+   display: inline-block;
}
.child {
    height: 200px;
    width: 300px;
    background: green;
    float: left;
}
```

{% asset_img 2.png %}

### 解决办法四：让父元素的 overflow 设置为 hidden
```diff
.parent {
    min-height: 10px;
    border: 10px solid red;
+   overflow: hidden;
}
.child {
    height: 200px;
    width: 300px;
    background: green;
    float: left;
}
```

{% asset_img 3.png %}

### 解决办法五：让父元素的 display 设置为 flow-root (触发 BFC,无副作用)
```diff
.parent {
    min-height: 10px;
    border: 10px solid red;
+   display: flow-root;
}
.child {
    height: 200px;
    width: 300px;
    background: green;
    float: left;
}
```

{% asset_img 3.png %}


**注意：如果父元素产生了 BFC，会把浮动的子元素包住，但是如果子元素里面还有浮动的子元素(也就是浮动的孙子)，这时，父元素是不会包住孙子元素的**

### 解决方法六
**使用 clear:both 清除浮动，并未产生 BFC**

```diff
.parent {
    min-height: 10px;
    border: 10px solid red;
}
+.parent::after {
+    content: "";
+    display: block;
+    clear: both;
+}
.child {
    height: 200px;
    width: 300px;
    background: green;
    float: left;
}
```

{% asset_img 3.png %}

## 功能二 和浮动元素产生边界
兄弟元素之间，其中左边元素浮动，让其划清界限
```html
<div class="left">我浮动了，在上面</div>
<div class="right">我在下面</div>
```

```css
div {
    height: 200px;
}
.left {
    min-width: 100px;
    border: 3px solid green;
    float: left;
    margin-right: 20px;
}
.right {
    border: 5px solid red;
}
```

{% asset_img 4.png %}

**用 float + div 做左右自适应布局**

### 解决办法一
浮动元素的 margin-right 没有生效，这个例子中，如果没有 BFC，我们想要产生边距，需要将普通元素的 margin-left 设置为浮动元素宽度 + 想要产生的边距宽度

```html
<div class="left">我宽度不变</div>
<div class="right">我宽度自适应</div>
```

```diff
div {
    height: 200px;
}
.left {
    min-width: 100px;
    border: 3px solid green;
    float: left;
-   margin-right: 20px;
}
.right {
    border: 5px solid red;
+   margin-left: 120px;
}
```

{% asset_img 5.png %}

### 解决方法二
让右侧兄弟元素的 display 设置为 flow-root
```html
<div class="left">我宽度不变</div>
<div class="right">我宽度自适应</div>
```

```diff
div {
    height: 200px;
}
.left {
    min-width: 100px;
    border: 3px solid green;
    float: left;
    margin-right: 20px;
}
.right {
    border: 5px solid red;
+   display: flow-root;
}
```

{% asset_img 5.png %}


### 解决方法三
让右侧兄弟元素的 display 设置为 flex
```html
<div class="left">我宽度不变</div>
<div class="right">我宽度自适应</div>
```

```diff
div {
    height: 200px;
}
.left {
    min-width: 100px;
    border: 3px solid green;
    float: left;
    margin-right: 20px;
}
.right {
    border: 5px solid red;
+   display: flex;
}
```

{% asset_img 5.png %}

### 解决方法四
让右侧兄弟元素的 overflow 设置为 hidden
```html
<div class="left">我宽度不变</div>
<div class="right">我宽度自适应</div>
```

```diff
div {
    height: 200px;
}
.left {
    min-width: 100px;
    border: 3px solid green;
    float: left;
    margin-right: 20px;
}
.right {
    border: 5px solid red;
+   overflow: hidden;
}
```

{% asset_img 5.png %}

## 功能三 同一个 BFC 下外边距会发生折叠
```html
<div></div>
<div></div>
```

```css
div {
    width: 100px;
    height: 100px;
    background: red;
    margin: 50px;
}
```

{% asset_img 6.png %}

从效果上看，因为两个 div 元素都处于同一个 BFC 容器下 (这里指 body 元素) 所以第一个 div 的下边距和第二个 div 的上边距发生了重叠，所以两个盒子之间距离只有 50px，而不是 100px。

### 解决方案一
首先这不是 CSS 的 bug，我们可以理解为一种规范，**如果想要避免外边距的重叠，可以将其放在不同的 BFC 容器中**。
```html
<div class="container">
    <p></p>
</div>
<div class="container">
    <p></p>
</div>
```

```css
.container {
    overflow: hidden;
}
p {
    width: 100px;
    height: 100px;
    background: red;
    margin: 50px;
}
```

{% asset_img 7.png %}

这时候，两个盒子边距就变成了 100px

## 总结
BFC 就是页面上一个单独的容器，容器内部的元素不会影响到外面的元素