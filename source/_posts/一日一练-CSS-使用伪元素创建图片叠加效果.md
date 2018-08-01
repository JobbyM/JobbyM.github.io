---
title: 一日一练-CSS 使用伪元素创建图片叠加效果
date: 2018-08-01 10:31:13
comments: true
tags:
  - 技术
  - 一日一练
  - CSS
  - 伪元素
  - 翻译
categories: 技术
---

> 子曰：一日一练-CSS 使用伪元素创建图片叠加效果

关于本文：
原文：https://designshack.net/author/joshuajohnson/
作者：@[JOSHUA JOHNSON](https://designshack.net/author/joshuajohnson/)

今天我们将看看我们是否可以在HTML 插入单个图像，使其看起来像只使用CSS 的图片叠加。 关键词：伪元素。

在这里可以看到我们如何开始这个项目，并使用一些awesome DRY 编码来解决产生的一些混乱的代码。

<!--more-->

## 挑战（The Challenge）
我在新的下载网站[Pixels Daily](http://pixelsdaily.com/)上发现一个惊艳的内容，一个名为[Stacks](http://pixelsdaily.com/resources/photoshop/psds/stacks/)的小免费PSD，它可以让你快速轻松地在Photoshop中构建一堆宝丽来风格（Polaroid-esque）的图像。
{% asset_img stackscss-1.jpg %}

这是一个非常有吸引力的效果。我一看到它，就立刻想用CSS 实现。因为有多个层叠在一起，我们不希望我们的标记因为每个图库项目的三个图像而混乱。那么我们如何使用最少的HTML 标记的同时实现这种错觉呢？

答案就是伪元素。这是一个完美展示`:before`和`:after` 魔力的用例。

## HTML 元素（The HTML）
我们仅需要一个带有图像的`div` 的HTML 创建我们的第一个堆栈。其余的样式都将通过CSS 来实现。 确保将“stackone”类添加到`div`。
```html
<div class='stackone'>
  <img src="http://lorempixum.com/200/200/city/7" alt="">
</div>
```

## 基础样式（Basic Styles）
一个基本的`margin/padding` 重置，使我们的浏览器保持一致。由于我们图像上的边框为白色，因此需要为背景添加某种色彩。
```css
* {margin: 0; padding: 0;}
body {background: #ccd3d7}
```

## 图片样式（Top Image Styles）
为图片添加样式：
```css
.stackone {
  border: 6px solid #fff;
  float: left;
  height: 200px; width: 200px;
  margin: 50px;
  position: relative;
  -webkit-box-shadow: 2px 2px 5px rgba(0,0,0,0.3);
  -moz-box-shadow: 2px 2px 5px rgba(0,0,0,0.3);
  box-shadow: 2px 2px 5px rgba(0,0,0,0.3);
}
```

逐步分析上述代码，这四行代码决定了尺寸和位置：
```
float: left;
height: 200px; width: 200px;
margin: 50px;
position: relative;
```
因为我们正在实现画廊效果，所以我们将图像浮动到左边并设置了相当`50px` 边距以便将其隔开。高度和宽度设置为图像尺寸（200px×200px）。

这段代码中定位上下文设置为`relative`。因为我知道我们稍后将对伪元素使用绝对定位，希望与这个项目“相对”而不是整个页面。

**修饰（Icing）**
其余的代码纯粹是为了美观。我添加了一个相当厚的白色边框和一个盒子阴影。
```
border: 6px solid #fff;
-webkit-box-shadow: 2px 2px 5px rgba(0,0,0,0.3);
-moz-box-shadow: 2px 2px 5px rgba(0,0,0,0.3);
box-shadow: 2px 2px 5px rgba(0,0,0,0.3);
```

**进度检查（Progress Check）**
此时，我们的图像看起来很棒。只需几行代码，我们就可以实现一个即时相机风格效果。
{% asset_img stackscss-2.jpg %}

## 第一个伪元素（The First Pseudo Element）
现在是时候添加我们的第一个堆栈了。基本上我们想要做的就是让它看起来像我们当前的另一个图像。我们可以使用`:before` 伪元素和我们上面使用的大部分相同代码。

使用以下语法：
```css
.stackone:before {
  content: "";
}
```
在这里，我们针对包含顶部图像的类，然后使用没有内容的`:before` 伪元素实现。这有效地为文档流添加了另一个项目供我们使用。现在让我们像之前的图像一样设计样式：
```css
.stackone:before {
  content: "";
  height: 200px; width: 200px;
  background: #eff4de;
  border: 6px solid #fff;
  -webkit-box-shadow: 2px 2px 5px rgba(0,0,0,0.3);
  -moz-box-shadow: 2px 2px 5px rgba(0,0,0,0.3);
  box-shadow: 2px 2px 5px rgba(0,0,0,0.3);
}
```
这里要注意的关键是我们并没有真正使用图像。我们可以加载另一个图像，但考虑到我们正在制作三个堆叠，这将是一个坏主意。对于图库中的每个图像，我们不想加载三个！ 幸运的是，由于伪元素的大部分表面区域将被遮挡，我们可以简单地使用纯色实现效果。

**定位（Positioning）**
在这一点上，事情看起来并不那么好。事实上，我们真的搞砸了我们的图片样式！
{% asset_img stackscss-3.jpg %}

可以看到伪元素干扰了图像。要解决这个问题，我们需要添加一些定位上下文，然后使用`z-index` 将其推送到后面。在这里拆分了不同的样式块，这样就可以清楚地看到每个块发生了什么。
```css
.stackone:before {
  content: "";
  height: 200px; width: 200px;
  background: #eff4de;
  border: 6px solid #fff;

  position: absolute;
  z-index: -1;
  top: 0px;
  left: -10px;

  -webkit-box-shadow: 2px 2px 5px rgba(0,0,0,0.3);
  -moz-box-shadow: 2px 2px 5px rgba(0,0,0,0.3);
  box-shadow: 2px 2px 5px rgba(0,0,0,0.3);

  -webkit-transform: rotate(-5deg);
  -moz-transform: rotate(-5deg);
  -o-transform: rotate(-5deg);
  -ms-transform: rotate(-5deg);
  transform: rotate(-5deg);
}
```

通过使用绝对定位，我们可以在给定原始图像的起始位置的情况下将图像推入到位。使用`z-index`将图像推送到背景，处理之前的干扰。最后，我们使用一个转换效果来实现你在原始Stacks PSD中看到的凌乱旋转。

**进度检查（Progress Check）**
这时一切看起来都很棒！我们的第二张照片（好吧，假照片）很好地位于我们的第一张照片下面并且有足够的旋转角度来将它们分开。现在还有另一个照片需要实现。

{% asset_img stackscss-4.jpg %}

## 第二个伪元素（The Second Pseduo Element）
你可能猜到，要向堆栈中添加第三张照片，我们需要使用`:after` 伪元素。这与我们刚刚做的完全一样，所以没有必要详细介绍它。主要差异是不同的背景颜色，位置和旋转角度。
```css
.stackone:after {
  content: "";
  height: 200px; width: 200px;
  background: #768590;
  border: 6px solid #fff;
  position: absolute;
  z-index: -1;
  top: 5px;
  left: 0px;
  -webkit-box-shadow: 2px 2px 5px rgba(0,0,0,0.3);
  -moz-box-shadow: 2px 2px 5px rgba(0,0,0,0.3);
  box-shadow: 2px 2px 5px rgba(0,0,0,0.3);
  -webkit-transform: rotate(-4deg);
  -moz-transform: rotate(-4deg);
  -o-transform: rotate(-4deg);
  -ms-transform: rotate(-4deg);
  transform: rotate(-4deg);
}
```

**进度检查（Progress Check）**
看到了吗？我们已经完成了第一个图像堆栈！考虑到它实际上只使用单个图像，这是一个非常时髦的效果。

{% asset_img stackscss-5.jpg %}

## 有缺陷的工作流程（A Flawed Workflow）
鉴于原始PSD 有几个不同的图像堆栈可供选择，我想用CSS类做同样的事情。我们可以轻松地将上面的所有代码复制并粘贴到新的“stacktwo”类中，并更改伪元素的旋转/定位。 Bam：你有了一个新的图像堆栈。

问题是，我用了八个不同的堆栈来做这个，结果是一个可怕的代码块。它比1997年基于表格的布局更丑陋。问题是我一遍又一遍地重复着自己。代码中有一半以上由纯冗余组成。我不想将这种可怕的做法传递给你。

**不要重复CSS（DRY CSS）**
Andy Hunt 和Dave Thomas 在编程方面创造了“DRY”一词，代表“不要重复自己。” 需要注意的是，CSS 在技术上不是一种编程语言，它是一种样式表语言。但是，如果我们将其视为编程语言并应用DRY 编码的概念，我们会得到更好的结果。

看看我们是否可以应用这些实践来使我们的代码更简洁，更可扩展。

## 再试试吧（Let’s Try That Again）
按照当前方法，我们的CSS将变得笨拙。当我们使用伪元素之后，会使代码变为三倍！
```
.stackone {...}
.stacktwo {...}
.stackthree {...}
.stackfour {...}
.stackfive {...}
.stacksix {...}
.stackseven {...}
.stackeight {...}
```

将共享样式提取出来，放在一个声明块中。
```css
.stackone, .stacktwo, .stackthree, .stackfour, .stackfive, .stacksix, .stackseven, .stackeight {
  border: 6px solid #fff;
  float: left;
  height: 200px; width: 200px;
  position: relative;
  margin: 50px;
  -webkit-box-shadow: 2px 2px 5px rgba(0,0,0,0.3);
  -moz-box-shadow: 2px 2px 5px rgba(0,0,0,0.3);
  box-shadow: 2px 2px 5px rgba(0,0,0,0.3);
}
```
将核心样式一次性应用于所有类，这比不断重复自己要好得多。作为可选的下一步，你可以选择更好的选择器。

所有的类都以相同的五个字母开头：“stack”。根据此信息，可以应用属性选择符。
```css
div[class*='stack'] {
  border: 6px solid #fff;
  float: left;
  height: 200px; width: 200px;
  position: relative;
  margin: 50px;
  -webkit-box-shadow: 2px 2px 5px rgba(0,0,0,0.3);
  -moz-box-shadow: 2px 2px 5px rgba(0,0,0,0.3);
  box-shadow: 2px 2px 5px rgba(0,0,0,0.3);
}
```

这个选择器针对我们所有这些类！并且如果我们添加更多的堆栈类，这个选择器仍然可以完美运行。

按照这个方法继续应用，会让代码变得更加简短。这里的关键是找到重复的内容，只应用一次。 开始仅应用于“堆栈”类的声明。 然后应用于堆栈类的样式以及`:before` 和`:after` 元素。之后为`:before` 和`:after` 的样式设置了一个声明块。 最后，为`:before` 元素应用不同的颜色。
```css
div[class*='stack'] {
  float: left;
  position: relative;
  margin: 50px;
}

div[class*='stack'], div[class*='stack']:before, div[class*='stack']:after {
  border: 6px solid #fff;
  height: 200px; width: 200px;
  -webkit-box-shadow: 2px 2px 5px rgba(0,0,0,0.3);
  -moz-box-shadow: 2px 2px 5px rgba(0,0,0,0.3);
  box-shadow: 2px 2px 5px rgba(0,0,0,0.3);
}

div[class*='stack']:before, div[class*='stack']:after {
  background: #768590;
  content: "";
  position: absolute;
  z-index: -1;
}

div[class*='stack']:before {
  background: #eff4de;
}
```

我们在这里所做的实际上是将数百行代码压缩。这对于维护，加载时间和你的一般理智来说非常棒！一开始就应用DRY 原则有点棘手，但是一旦做到了，你就永远不会回头。

**其余堆栈（Other Stacks）**
{% asset_img stackscss-6.jpg %}

从这里开始，每个堆栈将是相当独特的，因此我们必须一次编码其余的堆栈。一点Sass 或Less 会对帮助这一部分有很大的帮助，但你们都厌倦了我对预处理器的讨论，所以我没有使用预处理器。
```css
/* STACK ONE */
.stackone:before {
  top: 4px;
  left: -6px;
}

.stackone:after {
  top: 4px;
  left: -6px;
}

/* STACK TWO */
.stacktwo:before {
  top: 0px;
  left: -10px;
  -webkit-transform: rotate(-5deg);
  -moz-transform: rotate(-5deg);
  -o-transform: rotate(-5deg);
  -ms-transform: rotate(-5deg);
  transform: rotate(-5deg);
}

.stacktwo:after {
  top: 5px; left: 0px;
  -webkit-transform: rotate(4deg);
  -moz-transform: rotate(4deg);
  -o-transform: rotate(4deg);
  -ms-transform: rotate(4deg);
  transform: rotate(4deg);
}

/* STACK THREE */
.stackthree:before {
  top: 5px;
  left: -15px;
  z-index: -1;
  -webkit-transform: rotate(-10deg);
  -moz-transform: rotate(-10deg);
  -o-transform: rotate(-10deg);
  -ms-transform: rotate(-10deg);
  transform: rotate(-10deg);
}

.stackthree:after {
  top: -2px;
  left: -10px;
  -webkit-transform: rotate(-5deg);
  -moz-transform: rotate(-5deg);
  -o-transform: rotate(-5deg);
  -ms-transform: rotate(-5deg);
  transform: rotate(-5deg);
}
```

## 浏览器兼容性（Browser Compatibility）
在测试中，这里的浏览器兼容性实际上令人印象深刻。在Safari，Firefox，Chrome，Opera 和IE9+ 中正常运行。对于IE7，你会得到一个没有花哨堆叠效果的简单图像库，我认为这是完全可以接受的，因为这是一种类似于圆角的纯粹美感。

IE8 是棘手的，因为它部分支持足够的东西来真正搞砸了一切。你可以决定如何处理这个问题，但我想出的解决方案是使用一个可怕的，肮脏的黑客只针对IE8，即将`\9` 添加到任何IE8 样式。利用这种技术，我只是隐藏了`:before` 和`:after` 元素，以便IE8 获得像IE7 这样的简单图像库。
```css
div[class*='stack']:before, div[class*='stack']:after {
  background: #768590;
  content: "";
  position: absolute;
  z-index: -1;

  /* Dirty IE8 hack */
  height: 0px\9; width: 0px\9;
  border: none\9;
}
```
IE8 在选择器遇到问题，只要你坚持单冒号语法，它实际上支持`:before` 和`:after`。如果使用逗号列出类而不是使用属性值选择器，则应该能够使IE8 执行堆栈效果。

## 结论（Conclusion）
我们今天获得了很多。我们构建了一个很酷的图像堆栈错觉，学习了如何使用`:before` 和`:after`，然后讨论了使用DRY 心态时编码的最佳实践。我希望你能学到一些东西。

请关注[PixelsDaily](http://pixelsdaily.com/)，免费下载包含本教程所有精彩的源代码，以及可在项目中使用的八个非常棒的图像堆栈。

## 参考文档
1.[use pseduo elements to create an image stack illusion](https://designshack.net/articles/css/use-pseudo-elements-to-create-an-image-stack-illusion/)
