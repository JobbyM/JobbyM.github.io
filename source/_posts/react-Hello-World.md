---
title: react Hello World
date: 2016-12-13 09:05:00
tags:
  - React
  - 翻译
  - 技术
categories: 技术
comments: true
---

> 此文章是翻译[Hello World](https://facebook.github.io/react/docs/hello-world.html)这篇React（版本v15.4.0）官方文档。

## Hello World

使用[this Hello World example code on CodePen](http://codepen.io/gaearon/pen/ZpvBNJ?editors=0010) 是最简单的了解React 的方式。你不必安装任何东西；你只需要跟着我们在新页面打开它就可以了。如果你更喜欢使用本地开发环境，请学习[Installationg](https://facebook.github.io/react/docs/installation.html) 页面。

这个最简单的React 例子：
```jsx
ReactDOM.render(
  <h1>Hello, world!</h1>,
  document.getElementById('root')
)
```
它渲染了一个打印"Hello World" 在浏览器页面上。

<!--more-->

接下来的部分将向你逐渐介绍使用React。我们将会检测构建React  应用的块：elements 和components。一旦你掌握它们，你就可以从小的复用块构建复杂的应用。

### A Note on JavaScript

React 是一个JavaScript 库，所以假设你已经掌握了基本的JavaScript 语言。如果你不觉得非常有信心，我们建议你[更新JavaScript 知识](https://developer.mozilla.org/en-US/docs/Web/JavaScript/A_re-introduction_to_JavaScript)，你可以更容易的学习。

在例子中，我们将会使用一些ES6 语法。因为它相对来说是新的，所以我们会保守的使用它，但是我们鼓励你去了解[arrow functions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)，[classes](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes)，[template literals](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Template_literals)，`let` 和`const` 语句。你可以使用[Babel REPL](http://babeljs.io/repl/) 去了解ES6 代码如何编译。


## 参考文档

1. [Hello World](https://facebook.github.io/react/docs/hello-world.html)
