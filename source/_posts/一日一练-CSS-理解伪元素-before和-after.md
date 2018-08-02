---
title: '一日一练-CSS 理解伪元素::before和::after'
date: 2018-08-02 10:57:37
comments: true
tags:
  - 技术
  - 一日一练
  - CSS
  - 伪元素
  - 翻译
categories: 技术
---

> 子曰：一日一练-CSS 理解伪元素::before和::after

关于本文：
原文：https://www.hongkiat.com/blog/pseudo-element-before-after/
作者：@[Thoriq Firdaus](https://www.hongkiat.com/blog/author/thoriq/)

[CSS](https://www.hongkiat.com/blog/tag/css/) 将样式应用于[HTML](https://www.hongkiat.com/blog/tag/css/) 标记，在某些情况下，当向文档添加额外标记是不必允许的，CSS 使用[伪元素](http://www.w3.org/TR/selectors/#pseudo-elements) 添加额外的标记。

在[我们的课程](https://www.hongkiat.com/blog/tag/css+coding/) 中提到过这个术语。

`:first-line`，`:first-letter`，`::selection`，`:before` 和`:after` 这些CSS 成员被归类为伪元素。 本文中伪元素特指`:before` 和`:after`，从基础开始研究伪元素。

<!--more-->

## 语法和浏览器支持（The Syntax and Brower Support）
---
伪元素实际上从[CSS1](http://reference.sitepoint.com/css/pseudoelements)就存在了，而`:before` 和`:after` 是在 **CSS2.1** 时发布的。开始伪元素使用单冒号作为语法，然后随着网络的发展，在CSS3中，伪元素被修改为使用双冒号--成为`::before` &`::after`--来区分伪类（即`:hover`，`:active`等）。
{% asset_img syntax.jpg %}

无论是单冒号还是双冒号格式，浏览器都会识别。但是由于IE8 仅支持单冒号格式，为了获得[更好的浏览器兼容性](http://reference.sitepoint.com/css/pseudoelement-before#compatibilitysection)，使用单冒号更安全。

### 它是做什么的（What dose it do？）
伪元素将在内容元素之前或之后插入一个额外元素，在技术上同下列标记是相等的。
```html
<p>
  <span>:before</span>
    This the main content.
  <span>:after</span>
</p>
```
但这些元素实际上并未在文档中生成。它在表面上仍然可见，但你不会在文档源上找到它们，因此实际上它们是 **假** 元素。

## 使用伪元素（Using pseudo-elements）
---
使用伪元素相对容易; `selector:before` 将在内容的选择器之前添加一个元素，`selector:after` 将在它后面添加，使用[`content` 属性](http://www.w3schools.com/cssref/pr_gen_content.asp) 向其中添加内容。

下面的代码段将在`blockquote` 前后添加引号。
{% asset_img quotationmark.jpg %}
```css
blockquote:before {
  content: open-quote;
}
blockquote:after {
  content: close-quote;
}
```

### 修饰伪元素（Styling pseudo-elements）
尽管伪元素是假的元素，但伪元素实际上就像一个“真实”元素; 我们可以在它们上添加任何样式声明，例如更改颜色，添加背景，调整字体大小，对齐文本内容等等。
{% asset_img styles.jpg %}
```css
blockquote:before {
  content: open-quote;
  font-size: 24pt;
  text-align: center;
  line-height: 42px;
  color: #fff;
  background: #ddd;
  float: left;
  position: relative;
  top: 30px;
}
blockquote:after {
  content: close-quote;
  font-size: 24pt;
  text-align: center;
  line-height: 42px;
  color: #fff;
  background: #ddd;
  float: right;
  position: relative;
  bottom: 30px;
}
```

### 指定大小（Specifying the dimension）
默认情况生成的元素是[内联级元素](http://webdesign.about.com/od/htmltags/g/bldefinlineelem.htm)，因此当我们要指定高度和宽度时，必须首先使用`display:block` 声明将其定义为块元素。
{% asset_img dimension.jpg %}
```css
blockquote:before {
  content: open-quote;
  font-size: 24pt;
  text-align: center;
  line-height: 42px;
  color: #fff;
  background: #ddd;
  float: left;
  position: relative;
  top: 30px;
  border-radius: 25px;

  /* define it as a block element */
  display: block;
  height: 25px;
  width: 25px;
}
blockquote:after {
  content: close-quote;
  font-size: 24pt;
  text-align: center;
  line-height: 42px;
  color: #fff;
  background: #ddd;
  float: right;
  position: relative;
  bottom: 40px;
  border-radius: 25px;

  /* define it as a block element */
  display: block;
  height: 25px;
  width: 25px;
}
```

### 附上背景图片（Attach background image）
我们也可以用图像而不是纯文本替换内容。尽管`content` 属性提供了一个`url()` 字符串来插入图像，但在大多数情况下，`background` 属性能更好地控制附加的图像。
{% asset_img image-background.jpg %}
```css
blockquote:before {
  content: "";
  font-size: 24pt;
  text-align: center;
  line-height: 42px;
  color: #fff;
  background: #ddd;
  float: left;
  position: relative;
  top: 30px;
  border-radius: 25px;

  background: url(images/quotationmark.png) -3px -3px #ddd;

  display: block;
  height: 25px;
  width: 25px;
}
blockquote:after {
  content: "";
  font-size: 24pt;
  text-align: center;
  line-height: 42px;
  color: #fff;
  background: #ddd;
  float: right;
  position: relative;
  bottom: 40px;
  border-radius: 25px;

  background: url(images/quotationmark.png) -1px -32px #ddd;

  display: block;
  height: 25px;
  width: 25px;
}
```
但从上面的代码片段中可以看到，我们仍然使用空字符串声明`content`属性。这是必须的，否则伪元素将无法正常工作。

### 结合伪类（Combining with pseudo-classes）
虽然伪元素和伪类不同，但是我们仍可以在一个CSS 规则中将伪类和伪元素一起使用，例如，当鼠标悬停在`blockquote` 上时将 **引号背景** 变得更暗。
{% asset_img hover.jpg %}
```css
blockquote:hover:after, blockquote:hover:before {
  background-color: #555;
}
```

### 添加过渡效果（Adding Transition Effect）
也可以应用`transition` 属性来创建优美的颜色过渡效果。
```
transition: all 350ms;
-o-transition: all 350ms;
-moz-transition: all 350ms;
-webkit-transition: all 350ms;
```
不过，转换效果似乎只适用于[最新版本的Firefox](http://www.mozilla.org/en-US/firefox/new/)。 希望[更多的浏览器](https://www.hongkiat.com/blog/20-awesome-battle-of-the-browsers-artworks/) 能够在将来允许在过渡属性中应用伪元素。

[DEMO](https://assets.hongkiat.com/uploads/before-after-elements/demo/index.html)
[DOWNLOAD SOURCE](https://assets.hongkiat.com/uploads/before-after-elements/demo/source.zip)

## 更多灵感（More Inspiration）
---
这里有两个例子，可以为你的网页设计提供想法。

### [迷人的阴影](http://www.paulund.co.uk/creating-different-css3-box-shadows-effects)
在本教程中，Paul Underwood 解释了如何使用`:before` 和`:after` 伪元素创建更逼真，更迷人的阴影效果。它们绝对定位于最后面，并设置`z-index` 值为负值。
{% asset_img fascinating-shadows.jpg %}

### [图片叠加效果](http://designshack.net/articles/css/use-pseudo-elements-to-create-an-image-stack-illusion/)
在单个图片标记上使用伪元素创建杂乱的堆叠图像效果也是可能的。伪元素使用负`z-index` 在实际图像的后部创建堆叠图像的错觉。
{% asset_img stacked-image.jpg %}

## 总结
---
伪元素为每个元素都添加了两个额外元素，而不会干扰实际的HTML 结构，并且可以转换为几乎所有内容。

事实上正在进行[伪元素的改进](http://www.w3.org/TR/css3-content/)，例如嵌套伪元素`div::before::before {content：'';}` 和多个伪元素`div::before(3) {content：'';}` 这将开辟更多的可能性。

## 参考文档
1.[理解伪元素::before 和::after](https://www.hongkiat.com/blog/pseudo-element-before-after/)
