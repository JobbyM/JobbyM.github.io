---
title: 一日一练-CSS 了解Data URIs
date: 2018-05-19 15:48:48
comments: true
tags:
  - 技术
  - CSS
  - Data URIs
  - 翻译
categories: 技术
---

> 子曰：一日一练-CSS 了解Data URIs

关于本文：
原文：https://css-tricks.com/data-uris/
作者：@[CHRIS COYIER](https://css-tricks.com/author/chriscoyier/)

你是否知道在HTML 中使用`<img>` 元素或在CSS 中声明`background-image` 时不必链接外部图像文件？ 你可以使用 **data URIs** 将图像数据直接嵌入到文档中。

在CSS，它像这样使用：
```css
li {
  background:
    url(data:image/gif;base64,R0lGODlhEAAQAMQAAORHHOVSKudfOulrSOp3WOyDZu6QdvCchPGolfO0o/XBs/fNwfjZ0frl3/zy7////wAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAACH5BAkAABAALAAAAAAQABAAAAVVICSOZGlCQAosJ6mu7fiyZeKqNKToQGDsM8hBADgUXoGAiqhSvp5QAnQKGIgUhwFUYLCVDFCrKUE1lBavAViFIDlTImbKC5Gm2hB0SlBCBMQiB0UjIQA7)
    no-repeat
    left center;
  padding: 5px 0 5px 25px;
}
```

<!--more-->

在HTML 中，它像这样使用：
```html
<img width="16" height="16" alt="star" src="data:image/gif;base64,R0lGODlhEAAQAMQAAORHHOVSKudfOulrSOp3WOyDZu6QdvCchPGolfO0o/XBs/fNwfjZ0frl3/zy7////wAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAACH5BAkAABAALAAAAAAQABAAAAVVICSOZGlCQAosJ6mu7fiyZeKqNKToQGDsM8hBADgUXoGAiqhSvp5QAnQKGIgUhwFUYLCVDFCrKUE1lBavAViFIDlTImbKC5Gm2hB0SlBCBMQiB0UjIQA7" />
```

具体的格式：
```
data:[<mime type>][;charset=<charset>][;base64],<encoded data>
```

基本上，超长串的乱码字符。当然，这对浏览器来说并不是乱码。此数据被解释为你所说的文件类型。

你可以在这里[看到一个非常愚蠢的演示页面](http://css-tricks.com/examples/DataURIs/)。接下来我将介绍重要的部分。

## 你为什么要这样做？（Why would you do this?）
最大的原因是：它节省了HTTP 请求。除了纯粹的文档大小之外，这是关于页面加载速度的第一个因素。少=更好（Less = better）。

## 你如何获得代码？（How do you get the code?）
使用此[在线转换工具](http://websemantics.co.uk/online_tools/image_to_data_uri_convertor/)。这是我找到的最好的一个。还有一个[可拖放](http://jpillora.com/base64-encoder/)。

另请注意，base64 不是data URI 的唯一可能格式，有时甚至不是一个好主意。ASCII是另一种，其中代码基本上是URL 编码的，或UTF-8。

## 浏览器兼容性（Browser Compatibility）
Data URI 在IE 5-7 中不起作用，但在IE 8 支持。你可以：

* 使用[仅IE 的样式表](http://css-tricks.com/how-to-create-an-ie-only-stylesheet/)将图像放入，或，
* 仅用于渐进增强型的东西，其中没有图像是完全可以接受的，或者，
* 不关心
* [阅读本文](http://www.phpied.com/mhtml-when-you-need-data-uris-in-ie7-and-under/)，了解有效的替代技术。

## 重要笔记（Important Notes）
* 嵌入代码的大小本身略大于资源的大小。[GZip 压缩](http://css-tricks.com/snippets/htaccess/active-gzip-compression/)会有所帮助。
* IE8 的最大data URI 大小最低为32768字节。（嘿？！？！还有那个[疯狂的号码](http://css-tricks.com/32766/)。）
* 对于所有内容，很难维护具有嵌入式data URI 的站点。更新图像并替换它更容易。
* 如果你使用PHP（或[PHP 作为CSS](http://css-tricks.com/css-variables-with-php/)），你可以动态创建data URI，如下所示：
```
<?php
  echo base64_encode(file_get_contents("../images/folder16.gif"))
?>
```
* 你应该只在高度缓存的文档中使用它，就像你应该使用CSS 一样。如果CSS 文件节省6 个HTTP 请求，那么拥有300k 而不是50k 的CSS文件就好了，但只有当CSS 文件缓存时才会像那些图像那样缓存。设置CSS 文件的[长期过期](http://css-tricks.com/snippets/htaccess/set-expires/)应该会有所帮助。
* Data URI 不仅限于图像，它们实际上可以是任何东西。
* 当它得到更多支持并且人们为它构建很酷的工具时，`<canvas>` 可能会淘汰所有这些。
* 指南针（Compass） [有一个帮手](http://compass-style.org/reference/compass/helpers/inline-data/)。

## 论表现（On Performance）
Peter McLachlan 的[一些相关研究](http://www.mobify.com/blog/data-uris-are-slow-on-mobile/)：

> ...在测量数十万个移动页面视图的性能时，使用data URI 加载图像的速度平均比使用二进制源链接（如带有src 属性的img 标记）慢6倍！
