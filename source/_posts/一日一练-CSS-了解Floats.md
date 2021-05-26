---
title: 一日一练-CSS 了解Floats
date: 2021-05-26 09:39:15
tags:
  - 技术
  - 一日一练
  - CSS
  - Float
categories: 技术
---


## 名词解释
Float：浮动

float 属性最初只用于在成块的文本内浮动图像，但是现在它已成为在网页上创建多列布局的最常用工具之一。

<!--more-->

## 浮动的背景知识
最初，引入 float 属性是为了能让 web 开发人员实现简单的布局，包括在一列文本中浮动的图像，文字环绕在它的左边或右边。

但 Web 开发人员很快意识到，任何东西都可以浮动，而不仅仅是图像，所以浮动的使用范围扩大了

浮动曾被用来实现整个网站页面的布局，它使信息列得以横向排列（默认的设定则是按照这些列在源代码中出现的顺序纵向排列）。目前出现了更新更好的页面布局技术，所以使用浮动来进行页面布局应被看作传统的布局方法。

## 简单的例子
让我们来探讨如何使用浮动。我们将从一个非常简单的例子开始，包括在图像周围浮动一个文本块。

首先新建一个 HTML
```html
<h1>Simple float example</h1>

<img src="https://mdn.mozillademos.org/files/13340/butterfly.jpg" alt="A pretty butterfly with red, white, and brown coloring, sitting on a large leaf">

<p> Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla luctus aliquam dolor, eu lacinia lorem placerat vulputate. Duis felis orci, pulvinar id metus ut, rutrum luctus orci. Cras porttitor imperdiet nunc, at ultricies tellus laoreet sit amet. Sed auctor cursus massa at porta. Integer ligula ipsum, tristique sit amet orci vel, viverra egestas ligula. Curabitur vehicula tellus neque, ac ornare ex malesuada et. In vitae convallis lacus. Aliquam erat volutpat. Suspendisse ac imperdiet turpis. Aenean finibus sollicitudin eros pharetra congue. Duis ornare egestas augue ut luctus. Proin blandit quam nec lacus varius commodo et a urna. Ut id ornare felis, eget fermentum sapien.</p>

<p>Nam vulputate diam nec tempor bibendum. Donec luctus augue eget malesuada ultrices. Phasellus turpis est, posuere sit amet dapibus ut, facilisis sed est. Nam id risus quis ante semper consectetur eget aliquam lorem. Vivamus tristique elit dolor, sed pretium metus suscipit vel. Mauris ultricies lectus sed lobortis finibus. Vivamus eu urna eget velit cursus viverra quis vestibulum sem. Aliquam tincidunt eget purus in interdum. Cum sociis natoque penatibus et magnis dis parturient montes, nascetur ridiculus mus.</p>
```

并应用如下样式
```css
body {
  width: 90%;
  max-width: 900px;
  margin: 0 auto;
}

p {
  line-height: 2;
  word-spacing: 0.1rem;
}
```

使用如下 float 来让图片周围的文本浮起来。
```css
img {
  float: left;
  margin-right: 30px;
}
```

可以看到如下效果
{% asset_img 1.png %}

我们考虑一下浮动是如何工作的 -- 浮动元素（这个例子中是 `<img>` 元素）会脱离正常的文档布局流，并吸附到其父容器的左边（这个例子中是 `<body>` 元素）。在正常布局中位于该浮动元素之下的内容，此时会围绕着浮动元素，填满其右侧的空间。

**注意：浮动内容仍然遵循盒子模型诸如外边距和边界**。 我们设置一下图片右侧的外边距就能阻止右侧的文字紧贴着图片。

向右浮动的内容是一样的效果，只是反过来了-- 浮动元素会吸附到右边，而其他内容将从左侧环绕它。尝试将上一个例子中的浮动值改为 `right`，再把 `margin-right` 换成 `margin-left`，则看到如下效果
{% asset_img 2.png %}

## 多列浮动布局
浮动通常用于创建多个列布局，并且由于其广泛的浏览器支持已经有相当一段时间。尽管事实上，他们不是真的打算这个工作，并有一些奇怪的副作用必须处理，你会在后面的文章中看到。

### 两列布局
首先，创建一个 HTML
```html
<h1>2 column layout example</h1>
<div>
  <h2>First column</h2>
  <p> Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla luctus aliquam dolor, eu lacinia lorem placerat
    vulputate. Duis felis orci, pulvinar id metus ut, rutrum luctus orci. Cras porttitor imperdiet nunc, at ultricies
    tellus laoreet sit amet. Sed auctor cursus massa at porta. Integer ligula ipsum, tristique sit amet orci vel,
    viverra egestas ligula. Curabitur vehicula tellus neque, ac ornare ex malesuada et. In vitae convallis lacus.
    Aliquam erat volutpat. Suspendisse ac imperdiet turpis. Aenean finibus sollicitudin eros pharetra congue. Duis
    ornare egestas augue ut luctus. Proin blandit quam nec lacus varius commodo et a urna. Ut id ornare felis, eget
    fermentum sapien.</p>
</div>

<div>
  <h2>Second column</h2>
  <p>Nam vulputate diam nec tempor bibendum. Donec luctus augue eget malesuada ultrices. Phasellus turpis est, posuere
    sit amet dapibus ut, facilisis sed est. Nam id risus quis ante semper consectetur eget aliquam lorem. Vivamus
    tristique elit dolor, sed pretium metus suscipit vel. Mauris ultricies lectus sed lobortis finibus. Vivamus eu urna
    eget velit cursus viverra quis vestibulum sem. Aliquam tincidunt eget purus in interdum. Cum sociis natoque
    penatibus et magnis dis parturient montes, nascetur ridiculus mus.</p>
</div>
```

给 HTML 提供一些基本样式设置：
```css
body {
    width: 90%;
    max-width: 900px;
    margin: 0 auto;
}
```

在宽度达到 900px 之前，整个视图的宽度将达到 90%，在超过 900px 后，它将保持在这个宽度，并在视口中居中。默认情况下，子元素(这个 `<h1>` 和两个 `<div>`)将跨越整个 body 宽度的 100%。如果我们希望将两个 `<div>` 放在一起，那么我们需要将它们的宽度设置为父元素的宽度的 100%，或者更小，这样它们就可以彼此匹配。将下面的内容添加到 CSS 的底部：
```css
div:nth-of-type(1) {
    width: 48%;
}
div:nth-of-type(2) {
    width: 48%;
}
```

在这里我们设置了他们的父亲的宽度的 48％ —— 这总计 96％，留下我们 4％ 自由作为两列之间的沟槽，给内容一些空间呼吸。现在我们只需要浮动列，像这样：
```css
div:nth-of-type(1) {
    width: 48%;
    float: left;
}
div:nth-of-type(2) {
    width: 48%;
    float: right;
}
```

效果如下
{% asset_img 3.png %}

你会注意到，我们所有列使用宽度百分比——这是一个很好的策略，因为它创建一个**流式布局（liquid layout）**，一种适应不同的屏幕尺寸，并在较小的屏幕尺寸下保持相同的列宽度比例。这是**响应式网页设计**的一个有价值的工具，我们将在以后的模块中讨论。

需要注意的一件事是，当它们变得非常窄时，列就会变得很糟糕。切换回窄屏幕的单列布局通常是有意义的（如手机)，使用媒体查询可以实现这一功能。

另一种选择是将宽度设置为一个固定的单位如 rem 或像素，或者通过删除 `max-width` 声明来转换你自己的示例，并改变各个宽度为 900px, 430px 和  430px。这就是**固定宽度布局（fixed-width layout）**——现在调整浏览器大小，将看到布局不再调整以适应视图宽度，在尺寸更小时将需要滚动来查看它的全部。

### 三列布局
在两列布局上添加第三列
```html
<div>
  <h2>Third column</h2>
  <p>Nam consequat scelerisque mattis. Duis pulvinar dapibus magna, eget congue purus mollis sit amet. Sed euismod lacus sit amet ex tempus, a semper felis ultrices. Maecenas a efficitur metus. Nullam tempus pharetra pharetra. Morbi in leo mauris. Nullam gravida ligula eros, lacinia sagittis lorem fermentum ut. Praesent dapibus eros vel mi pretium, nec convallis nibh blandit. Sed scelerisque justo ac ligula mollis laoreet. In mattis, risus et porta scelerisque, augue neque hendrerit orci, sit amet imperdiet risus neque vitae lectus. In tempus lectus a quam posuere vestibulum. Duis quis finibus mi. Nullam commodo mi in enim maximus fermentum. Mauris finibus at lorem vel sollicitudin.</p>
</div>
```

更新 CSS
```css
body {
    width: 90%;
    max-width: 900px;
    margin: 0 auto;
}
div:nth-of-type(1) {
    width: 36%;
    float: left;
}
div:nth-of-type(2) {
    width: 30%;
    float: left;
    margin-left: 4%
}
div:nth-of-type(3) {
    width: 26%;
    float: right;
}
```

可以看到如下效果：
{% asset_img 4.png %}

这里需要注意的一点是，你必须仔细考虑将列放在什么位置，以及如何浮动它们，以获得所需的结果。你的内容应该是有意义的，当你阅读它的源代码和它的视觉布局的时候；但是，使用浮动可以使可视化布局与源顺序不同。来说明我们的意思，尝试改变第二列的 float 值为 right 你会看到现在的视觉顺序是这样的
```
div1 div3 div2
```

这是因为第二个 `<div>` 源代码顺序上比第三个 `<div>` 等级要高 (DOM上第二个 `<div>` 先出现并声明了 `float: right;`) ，所以在浮动顺序上也会比第三个 `<div>` 等级要高。又因为两者同时向右浮动，第二个 `<div>` 就会更加地靠右。

然而视觉受损的人使用屏幕阅读器来听你的内容，仍然会听到这个顺序的内容：
```
div1 div2 div3
```

## 清除浮动
在运用 float 时，你可能会遇到一个问题 -- 所有在浮动下面的自身不浮动的内容都将围绕着浮动元素进行包裹，如果没有处理这些元素，就会变得很糟糕。为了说明这一点，尝试在第三个 `<div>` 元素下面添加如下 HTML：
```html
<footer>
  <p>&copy;2016 your imagination. This isn't really copyright, this is a mockery of the very concept. Use as you wish.</p>
</footer>
```

你会看到页脚在最长的列旁边环绕着 -- 我们希望页脚保持在底部，在所有的列下面。幸运的是，有一种简单的方法可以解决这个问题—— clear 属性。当你把这个应用到一个元素上时，它主要意味着"此处停止浮动" -- 这个元素和源码中后面的元素将不浮动，除非你将一个新的 float 声明应用到此后的另一个元素。

所以，要解决我们的问题，添加以下规则：
```css
footer {
  clear: both;
}
```

这将会给你一个页脚，它会在所有列下面：
{% asset_img 5.png %}

clear 可以取三个值：

* left：停止任何活动的左浮动
* right：停止任何活动的右浮动
* both：停止任何活动的左右浮动

你通常只想设定一个 `clear: both;` 在你想让浮动停止的元素上。在某些情况下，你会想要只取消 left 或 right。

## 浮动问题

### 整个宽度可能难以计算
到目前为止，我们的例子是没有应用样式的浮动框——这很容易。当你开始给这些框加上样式时，比如添加背景、外边距、内边距等等，问题就来了。将下面的 CSS 加入到代码中
```css
div, footer {
  padding: 1%;
  border: 2px solid black;
  background-color: red;
}
```

此时，可以看到布局已损坏 —— 由于内边距和边界引入的额外宽度，一行容纳不下三列了，因此第三列下降到另外两列之下。

有两个方法可以解决问题，最好的方法是给你的 html 加上下面的 css。
```css
* {
  box-sizing: border-box;
}
```

box-sizing 通过更改盒模型来拯救我们，盒子的宽度取值为 content + padding + border，而不仅是之前的 content ——所以当增加内边距或边界的宽度时，不会使盒子更宽——而是会使内容调整得更窄。

我们有另一个问题——页脚正压在最长列上， 在这一点并不理想——我们来试着清除页脚浮动的同时给出一些顶部外边距（ margin-top ）来解决这个问题：
```css
footer {
    clear: both;
    margin-top: 4%;
}
```

然而，这不起作用 ——浮动的元素存在于正常的文档布局流之外，在某些方面的行为相当奇怪：

* 首先，他们在父元素中所占的面积的有效高度为0，这里正文高度只有 `<h1>` 的高度 。这个可以通过很多方式解决，但是我们所依赖的是在父容器的底部清除浮动，如我们在我们的当前示例所做的那样。 如果检查当前示例中正文的高度，你应该看它的高度是 `div` 本身。
* 其次，非浮动元素的外边距不能用于它们和浮动元素之间来创建空间——这是我们在这里眼前的问题，我们将在下面实施修复。
* 还有一些关于浮动的奇怪的事情——Chris Coyier优秀的[关于Floats文章](https://css-tricks.com/all-about-floats/)概述了其他一些以及修复这些。

所以，让我们解决这个！ 首先，在HTML的代码里添加新的 `<div>` 元素，位于在 `<footer>` 标签的上方：
```html
<div class="clearfix"></div>
```

如果你没有一个可用的元素来清除你的浮动(比如我们的页脚)，在你想要清除的浮动之后添加一个看不见的 `clearfix div` 是非常有用的，但是在这里页脚也要用到。接下来我们要做的是，移除页脚样式规则中的 `clear: both;` 声明，取而代之将其放在 `clearfix div` 中：
```css
.clerfix {
    clear: both;
}
```

我们的页脚现在有一个很好的顶部外边距，但也有另一个问题——`clearfix div` 背景、内边距和边界与我们的列和页脚相同！为了解决这个问题，让我们先给每个列块一个类（ class ）column：
```html
<div class="column">

</div>
```

现在让我们改变应用盒子样式的规则到这些块和页脚，这样只有列块被样式化：
```css
.column, footer {
  padding: 1%;
  border: 2px solid black;
  background-color: red;
}
```
至此，修复问题大概就那样。

### 浮动项目的背景高度
到目前为止，我们建好的示例是有效的，但另一个问题是列高度是不同的—— 如果列都是相同的高度，它看起来会更好。

我们可以通过给所有的列固定height 来解决这个问题
```css
.column {
    height: 550px;
}
```
你也可以考虑：

* 将这些列的背景颜色设置为父元素的背景颜色，这样就不会看到高度是不同的。这是目前最好的选择。
* 将它们设置为固定的高度，并使内容滚动overflow 。
* 使用一种叫做**伪列（faux columns）**的技术——这包括将背景(和边界)从实际的列中提取出来，并在列的父元素上画一个伪造的背景，看起来像列的背景一样。不幸的是，这将无法处理列边界。 详见对于[伪列和伪列流体布局](https://www.addedbytes.com/blog/code/faux-columns-for-liquid-layouts/)的教程。


### 清除浮动会变复杂
我们在文章中建立的简单例子很容易理解，但是当布局变得更加复杂清理（clearing）也会变得更加复杂。你需要确保所有的浮动都能尽快清除，以避免它们给下方的内容制造麻烦。如果你没有一个方便的容器来进行清理，那么在必要的时候使用clearfix块。

## 参考文档
1.[Chris Coyier 的关于 Floats 文章](https://css-tricks.com/all-about-floats/)
2.[伪列和伪列流体布局](https://www.addedbytes.com/blog/code/faux-columns-for-liquid-layouts/)
3.[MDN Floats 布局](https://developer.mozilla.org/zh-CN/docs/Learn/CSS/CSS_layout/Floats)