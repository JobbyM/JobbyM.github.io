---
title: 一日一练-CSS 【转载】CSS左中右自适应布局六种方案与原理
date: 2021-05-17 16:52:32
tags:
  - 技术
  - 一日一练
  - CSS
  - 转载
categories: 技术
---

本文转载自[CSS左中右自适应布局六种方案与原理](https://segmentfault.com/a/1190000022096517)，有部分删减，看原文请到原地址。

如何实现左右固定 300px , 中间宽度自适应？
{% asset_img 1.gif %}

<!--more-->

有如下 HTML 结构
```html
<div class="wrapper">
    <div class="item left">左</div>
    <div class="item right">右</div>
    <div class="item center">中</div>
</div>
```

公有样式，设置高，设置左右宽度固定 300px ，左右为红色，中间为黄色
```css
body { 
    margin: 0;
}
.item {
    height: 400px;
}
.left,
.right {
    width: 300px;
    background: #f00;
}
.center {
    background: yellow;
}
```

## 一、双浮动法
```css
.left { 
    float: left;
}
.right {
    float: right;
}
```

```css
.center {
    overflow: hidden;
}
```

### 原理
float 不会完全脱离文档流，会占用原空间
> MDN 官方解释：该元素从网页的正常流动（文档流）中移除，尽管仍然保持部分的流动性（与绝对定位相反）。

### 问
为什么 center 要放到 left 和 right 之后？

### 答
浏览器加载页面的顺序是从上到下，left 已经左浮动，right 右浮动。渲染 center 时，浏览器忽略掉已经脱离文档流的 left 和 right， center 元素会从头占领整行。

此时可以使 center 成为 BFC，例如设置 `overflow: hidden;` 这样，使得 center 的宽度在 left 和 center 之间。

## 二、绝对定位法
```css
.left {
    position: absolute;
    left: 0;
    top: 0;
}
.right {
    position: absolute;
    right: 0;
    top: 0;
}
.center {
    margin: 0 300px;
}
```

### 原理
left 、 right 绝对定位脱离文档流。center 不设置宽度，只设置左右边距，自动撑满块级元素一行的剩余空间

## 三、弹性盒子
首先需要调整 html 布局
```html
<div class="wrapper">
    <div class="item left">左</div>
    <div class="item center">中</div>
    <div class="item right">右</div>
</div>
```

接着，父容器设置 `display: flex`，左右设置 300px, center 把 flex 设置为1 ，效果是否实现了呢?
```css
.wrapper {
    display: flex;
}
.center {
    flex: 1;
}
```

浏览器运行，看上去是对的 ，但实际上屏幕缩小后左右不是固定的 300px，宽度会被压缩。
还需要再添加如下样式：
```css
.left , .right {
    flex-shrink: 0; /* 等同于 flex: 0 0 auto;  */
}
```

### 原理
`flex:1` 实际上是三个属性的简写 , `flex:1;` 等同于 `flex:1 1 auto;`。
简单的说，分别代表：剩余空间 容器缩小时压缩比例 最小宽度。具体细节参考：[MDN — 弹性盒子](https://developer.mozilla.org/zh-CN/docs/Learn/CSS/CSS_layout/Flexbox)
`flex: none | [ <'flex-grow'> <'flex-shrink'>? || <'flex-basis'> ]`

1. `flex-grow` 属性定义项目的放大比例，默认为 0，即如果存在剩余空间，也不放大。
2. `flex-shrink` 属性定义了项目的缩小比例，默认为 1，即如果空间不足，该项目将缩小。
3. `flex-basis` 属性定义了在分配多余空间之前，项目占据的主轴空间（main size）。浏览器根据这个属性，计算主轴是否有多余空间。它的默认值为 auto，即项目的本来大小。

由此得知，center 设置 `flex：1;` ，即为 `flex:1 1 auto;` (自动占满容器剩余宽度 默认压缩比例 默认最小宽度)
left 和 right 设置  `flex-shrink: 0;`, flex-shrink 就是 flex 简写中的第二个属性, 等同 `flex:0 0 auto;` (没有设置自动占满容器剩余宽度 0不压缩 最小宽度默认auto)

### 问 
为什么 left 和 right 设置 `flex-shrink: 0;` 等同 `flex:0 0 auto;`,其中最小宽度为 auto, 但是宽度依旧固定为 300px?

### 答
最小宽度为 auto 是默认值 , 优先级小于具体元素设置的 width。如果设置 `flex:0 0 400px`，那么左右最小宽度为 400px, 优先级高于元素设置的 width

## 四、网格布局 grid
最简单的网格布局
```css
.wrapper {
    display: grid;
    grid-template-columns: 300px 1fr 300px;
}
```

### 原理
`grid-template-columns` 为父容器属性，表示三个子元素的宽度为 (300px 自动占满1份剩余空间 300px),如果写 `grid-template-columns: 300px 1fr 2fr 100px;` 即表示四个子元素宽度分别为 300px 1/3剩余空间 2/3剩余空间 100px

## 五、表格布局
```css
.wrapper {
    width: 100%;
    display: table;
}
.wrapper>.item:not(.center) {
    display: table-cell;
}
```
把左右元素设置为单元格即可,**不过该方法在屏幕缩小时,左右固定300px会被压缩**

### 问
为什么要设置width为100%?

### 答
`display: table;` 与 table 元素类似 ,实际上是行内块级元素, 而不是块级元素。

表格不能自动响应: 当你使用正确的布局容器 (比如 `<header>`, `<section>`, `<article>`, 或是 `<div>` 是一个通用型的流内容容器，在不使用CSS的情况下，其对内容或布局没有任何影响。), 它们的默认宽度是父元素的 100%. 而表格的的默认大小是根据其内容而定的。因此，需要采取额外的措施来获取表格布局样式，以便有效地在各种设备上工作。

## 六、CSS 计算
```css
.wrapper {
    white-space: nowrap;
    font-size: 0;
    -webkit-text-size-adjust: none;
}
.item {
    display: inline-block;
}
.center {
    width: calc( 100vw - 300px * 2 )
}
```

### 原理
只要把div放在同一行，使用calc计算宽度即可 ，`font-size: 0;-webkit-text-size-adjust: none;` 是为了消除 inline-block 的间隙，其他方法亦可

## 七、JS 计算
```css
.wrapper {
    white-space: nowrap;
    font-size: 0;
    -webkit-text-size-adjust: none;
}
.item {
    display: inline-block;
}
```
```js
window.onload = function () {
    function computed() {
        const oCenter = document.querySelector('.wrapper .center')
        oCenter.style.width = innerWidth - 300 * 2 < 0 ? 0 + 'px' : innerWidth - 300 * 2 + 'px'
    }
    computed()
    var timer = null
    window.addEventListener('resize', () => {
        clearTimeout(timer)
        timer = setTimeout(computed, 300)
    })
}
```
与calc一致,计算出相对宽度即可

## 总结
* 绝对定位法脱离文档流，易带来布局问题
* grid 布局简单强大，移动端兼容性好
* js 方法万能，但不易维护
* 推荐使用flex 、grid
