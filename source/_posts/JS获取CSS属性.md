---
title: JS获取CSS属性
date: 2017-03-07 16:09:30
tags:
---

主要是介绍一些使用js 来获取元素的CSS 样式的方法

一、style

我们可以使用`element.style` 获取元素的CSS 样式声明对象，以及设置元素的CSS 样式声明对象。

注意：`element.style` 只能获取元素`style` 属性中的CSS 样式。

```html
<style>
.bg {
  height: 30px;
}
</style>
<div class="bg" style="width: 200px;">
使用element.style 获取CSS 属性值
</div>
<script>
console.log(document.querySelector('.bg').style.width) // 200px
console.log(document.querySelector('.bg').style.height) // ''
</script>
```

<!--more-->

二、Window.getComputedStyle()

Widnow.getComputedStyle() 是一个可以获取当前元素所有最终使用的CSS 属性值。返回的是一个CSS样式声明对象（[ojbect CSSStyleDeclaration]），只读。

语法：
```js
let style = window.getComputedStyle(element, [pseudoElt])
```

例子：
```html
<style>
  h3::after {
    content: "rocks!"
  }
</style>
<h3>使用getComputedStyle获取CSS 属性值</h3>
<script>
console.log(getComputedStyle(document.querySelector('h3'), '::after').content) // "rocks!"
</script>
```

在许多在线的演示代码中，getComputedStyle 是通过 document.defaultView 对象来调用的。大部分情况下，这是不需要的，因为可以直接通过window 对象调用。但有一种情况，你必须使用defaultView，那就是在firefox3.6 上访问框架（iframe）的样式。

三、currentStyle

`currentStyle` 是IE 浏览器自娱自乐的一个属性，返回的是元素当前应用的最终CSS 属性值（包括外链CSS 文件，页面中嵌入的`<style>` 属性）

语法：
```js
element.currentStyle
```

获取一个元素的高度，可以使用类似下面的代码：
```js
(element.currentStyle ? element.currentStyle : window.getComputedStyle(element, null)).height
```

四、参考文档

1. [获取元素CSS值之getComputedStyle方法熟悉](http://www.zhangxinxu.com/wordpress/2012/05/getcomputedstyle-js-getpropertyvalue-currentstyle/)
2. [Window.getComputedStyle() - Web API 接口 | MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/getComputedStyle)
3. [JS获取CSS属性值](http://www.css88.com/archives/625)
