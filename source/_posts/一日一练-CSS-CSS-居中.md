---
title: 一日一练-CSS-CSS 居中
date: 2018-04-12 14:06:06
comments: true
tags:
  - 技术
  - 一日一练
  - CSS
  - 翻译
categories: 技术
---

> 特别声明：此篇文章内容来源于[@CHRIS COYIER](https://css-tricks.com/author/chriscoyier/) 的[Centering in CSS:A Complete Guide](https://css-tricks.com/centering-css-complete-guide/)

> 子曰：CSS 居中是一个非常常见的问题，无论是在项目中，还是在各种面试资料中，这篇文章进行了系统的解答
> 本文为个人翻译

我们总是抱怨使用CSS居中。 为什么要这么辛苦？ 这个问题不是说很难去解决，而是由于不同的情况有很多不同的方式来实现，我们难以抉择去选择那一个方式去解决它。

所以我们希望通过决策树来更处理它。

我需要居中...

<!--more-->

1. 水平居中
  1. inline 或inline-* 元素（像text 或link）
  2. block level 元素
  3. 多个block level 元素
2. 垂直居中
  1. inline 或inline-* 元素（像text 或link）
    1. 单行
    2. 多行
  2. block level 元素
    1. 元素的高度已知
    2. 元素的高度未知
    3. 使用flexbox
3. 水平垂直居中
  1. 固定宽高元素
  2. 未知宽高的元素
  3. 使用flex
  4. 使用grid

## 水平居中
### inline 或inline-* 元素（像text 或link）
可以在块级别的父元素中水平居中inline 元素，只需：
```css
.center-children {
  text-align: center;
}
```
inline、inline-block、inline-table、inline-flex 都可以居中。
### block level 元素
设置块级元素的`margin-left`和`margin-right` 为`auto`进行居中（并且它有一个已知的`width` 值，否则它将是全宽展示，不需要居中）。 通常使用下面的缩写来完成的：
```css
.center-me {
  margin: 0 auto;
}
```
不管需要居中的块级元素的宽度或父级的宽度如何，上述代码都可以。
主要，你不能使用`float`进行元素居中。 [虽然这有一个技巧](http://css-tricks.com/float-center/)。
### 多个block level 元素
如果需要将两个或多个的块级元素在 **一行中** 水平居中，那么你可能最好设置一个不同的`display`类型。 下面是使它们成为内联块的示例以及使用flexbox的示例：
```html
<main class="inline-block-center">
  <div>
    I'm an element that is block-like with my siblings and we're centered in a row.
  </div>
  <div>
    I'm an element that is block-like with my siblings and we're centered in a row. I have more content in me than my siblings do.
  </div>
  <div>
    I'm an element that is block-like with my siblings and we're centered in a row.
  </div>
</main>

<main class="flex-center">
  <div>
    I'm an element that is block-like with my siblings and we're centered in a row.
  </div>
  <div>
    I'm an element that is block-like with my siblings and we're centered in a row. I have more content in me than my siblings do.
  </div>
  <div>
    I'm an element that is block-like with my siblings and we're centered in a row.
  </div>
</main>
```
```css
body {
  background: #f06d06;
  font-size: 80%;
}

main {
  background: white;
  margin: 20px 0;
  padding: 10px;
}

main div {
  background: black;
  color: white;
  padding: 15px;
  max-width: 125px;
  margin: 5px;
}

.inline-block-center {
  text-align: center;
}
.inline-block-center div {
  display: inline-block;
  text-align: left;
}

.flex-center {
  display: flex;
  justify-content: center;
}
```
除非你的意思是你有多个块级元素堆叠在一起，在这种情况下，仍然可以通过设置`margin` 来居中：
```html
<main>
  <div>
    I'm an element that is block-like with my siblings and we're centered in a row.
  </div>
  <div>
    I'm an element that is block-like with my siblings and we're centered in a row. I have more content in me than my siblings do.
  </div>
  <div>
    I'm an element that is block-like with my siblings and we're centered in a row.
  </div>
</main>
```
```css
body {
  background: #f06d06;
  font-size: 80%;
}

main {
  background: white;
  margin: 20px 0;
  padding: 10px;
}

main div {
  background: black;
  margin: 0 auto;
  color: white;
  padding: 15px;
  margin: 5px auto;
}

main div:nth-child(1) {
  width: 200px;
}
main div:nth-child(2) {
  width: 400px;
}
main div:nth-child(3) {
  width: 125px;
}
```
## 垂直居中
CSS 中垂直居中是一个棘手的问题。
### inline 或inline-* 元素（像text 或link）
#### 单行
有时inline / text 元素可以出现垂直居中，只是因为`paddig-top` 等于`padding-bottom`。
```css
.link {
  padding-top: 30px;
  padding-bottom: 30px;
}
```
如果出于某种原因，`padding`不是一个选项，并且你尝试将一些已知不会换行的文本（单行）居中，则有一个技巧是使`height`等于`line-height`将使文本居中。
```css
.center-text-trick {
  height: 100px;
  line-height: 100px;
  white-space: nowrap;
}
```
#### 多行
`paddig-top` 等于`padding-bottom`也可以为多行文本赋予居中效果，但如果这样做不起作用，那么文本所在的元素可能是table cell，无论是字面上的还是与行为类似的CSS。 在这种情况下，[`vertical-align`](http://css-tricks.com/almanac/properties/v/vertical-align/)属性处理这个问题，与通常所处理的不同，它处理排在一行上的元素的对齐方式。 （[更多内容。](http://css-tricks.com/what-is-vertical-align/)）
```html
<table>
  <tr>
    <td>
      I'm vertically centered multiple lines of text in a real table cell.
    </td>
  </tr>
</table>

<div class="center-table">
  <p>I'm vertically centered multiple lines of text in a CSS-created table layout.</p>
</div>
```
```css
body {
  background: #f06d06;
  font-size: 80%;
}

table {
  background: white;
  width: 240px;
  border-collapse: separate;
  margin: 20px;
  height: 250px;
}

table td {
  background: black;
  color: white;
  padding: 20px;
  border: 10px solid white;
  /* default is vertical-align: middle; */
}

.center-table {
  display: table;
  height: 250px;
  background: white;
  width: 240px;
  margin: 20px;
}
.center-table p {
  display: table-cell;
  margin: 0;
  background: black;
  color: white;
  padding: 20px;
  border: 10px solid white;
  vertical-align: middle;
}
```
如果tabel-like 布局过时了，也许你可以使用flexbox？ 一个单独的flex-child 可以很容易地在flex-parent 中居中。
```css
.flex-center-vertically {
  display: flex;
  justify-content: cnter;
  flex-direction: column;
  height: 400px;
}
```
```html
<div class="flex-center">
  <p>I'm vertically centered multiple lines of text in a flexbox container.</p>
</div>
```
```css
body {
  background: #f06d06;
  font-size: 80%;
}

div {
  background: white;
  width: 240px;
  margin: 20px;
}

.flex-center {
  background: black;
  color: white;
  border: 10px solid white;
  display: flex;
  flex-direction: column;
  justify-content: center;
  height: 200px;
  resize: vertical;
  overflow: auto;
}
.flex-center p {
  margin: 0;
  padding: 20px;
}
```
记住，只有父容器具有固定高度（px，%等），那么它才真正相关，这就是为什么这里的容器有一个高度。
如果这两种技术都过时了，你可以使用“鬼元素（ghost element）”技术，在这种技术中，一个全高度的伪元素被放置在容器中，并且文本与其垂直对齐。
```css
.ghost-center {
  position: relative;
}
.ghost-center::before {
  content: " ";
  display: inline-block;
  height: 100%;
  width: 1%;
  vertical-align: middle;
}
.ghost-center p {
  display: inline-block;
  vertical-align: middle;
}
```
```html
<div class="ghost-center">
  <p>I'm vertically centered multiple lines of text in a container. Centered with a ghost pseudo element</p>
</div>
```
```css
body {
  background: #f06d06;
  font-size: 80%;
}

div {
  background: white;
  width: 240px;
  height: 200px;
  margin: 20px;
  color: white;
  resize: vertical;
  overflow: auto;
  padding: 20px;
}

.ghost-center {
  position: relative;
}
.ghost-center::before {
  content: " ";
  display: inline-block;
  height: 100%;
  width: 1%;
  vertical-align: middle;
}
.ghost-center p {
  display: inline-block;
  vertical-align: middle;
  width: 190px;
  margin: 0;
  padding: 20px;
  background: black;
}
```
### block level 元素
#### 元素的高度已知
不知道网页布局的高度是很常见的，原因很多：如果宽度改变，文本重排可以改变高度。 文字造型的差异可以改变高度。 文字数量的变化可以改变高度。 具有固定宽高比的元素（如图像）可在更改大小时更改高度, 等等。
但是如果你知道高度，你可以像这样垂直居中：
```css
.parent {
  position: relative;
}
.child {
  position: absolute;
  top: 50%;
  height: 100px;
  margin-top: -50px; /* account for padding and border if not using box-sizing: border-box; */
}
```
```html
<main>

  <div>
     I'm a block-level element with a fixed height, centered vertically within my parent.
  </div>

</main>
```
```css
body {
  background: #f06d06;
  font-size: 80%;
}

main {
  background: white;
  height: 300px;
  margin: 20px;
  width: 300px;
  position: relative;
  resize: vertical;
  overflow: auto;
}

main div {
  position: absolute;
  top: 50%;
  left: 20px;
  right: 20px;
  height: 100px;
  margin-top: -70px;
  background: black;
  color: white;
  padding: 20px;
}
```
#### 元素的高度未知
将top 设置为50% 之后，可以通过将它的高度调高一半来使它居中：
```css
.parent {
  position: relative;
}
.child {
  position: absolute;
  top: 50%;
  transform: translateY(-50%)
}
```
#### 使用flexbox
没有什么大惊喜，这在[flexbox](http://css-tricks.com/snippets/css/a-guide-to-flexbox/)中更容易。
```css
.parent {
  display: flex;
  flex-direction: column;
  justify-content: center;
}
```
## 水平垂直居中
你可以以任何方式将上述技巧结合起来，以获得完美居中的元素。 但我发现这通常分为三个阵营：
### 固定宽高元素
在将其绝对定位在50％/ 50％后，使用等于该宽度和高度的一半的负边距，获得完美的跨浏览器支持水平垂直居中：
```css
.parent {
  position: relative;
}
.child {
  width: 300px;
  height: 100px;
  padding: 20px;

  position: absolute;
  top: 50%;
  left: 50%;

  margin: -70px 0 0 -170px;
}
```
### 未知宽高的元素
如果不知道宽度或高度，则可以使用`transform`属性，在两个方向上的50％负向平移（它基于元素的当前宽度/高度）居中：
```css
.parent {
  position: relative;
}
.child {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}
```
```html
<main>

  <div>
     I'm a block-level element of an unknown height and width, centered vertically within my parent.
  </div>

</main>
```
```css
body {
  background: #f06d06;
  font-size: 80%;
  padding: 20px;
}

main {
  position: relative;
  background: white;
  height: 200px;
  width: 60%;
  margin: 0 auto;
  padding: 20px;
  resize: both;
  overflow: auto;
}

main div {
  background: black;
  color: white;
  width: 50%;
  transform: translate(-50%, -50%);
  position: absolute;
  top: 50%;
  left: 50%;
  padding: 20px;
  resize: both;
  overflow: auto;
}
```
### 使用flexbox
使用flexbox 实现水平垂直居中，需要使用两个居中属性：
```css
.parent {
  display: flex;
  justify-content: center;
  align-items: center;
}
```
```html
<main>

  <div>
     I'm a block-level-ish element of an unknown width and height, centered vertically within my parent.
  </div>

</main>
```
```css
body {
  background: #f06d06;
  font-size: 80%;
  padding: 20px;
}

main {
  background: white;
  height: 200px;
  width: 60%;
  margin: 0 auto;
  padding: 20px;
  display: flex;
  justify-content: center;
  align-items: center;
  resize: both;
  overflow: auto;
}

main div {
  background: black;
  color: white;
  width: 50%;
  padding: 20px;
  resize: both;
  overflow: auto;
}
```
### 使用grid
这只是一个小技巧（由Lance Janssen 提出），对于一个元素来说效果非常好：
```css
body, html {
  height: 100%;
  display: grid;
}
span { /* thing to center */
  margin: auto;
}
```
## 总结
在CSS 中你可以居中任何事
