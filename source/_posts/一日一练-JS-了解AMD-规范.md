---
title: 一日一练-JS 了解AMD 规范
date: 2018-04-24 17:50:03
comments: true
tags:
  - 技术
  - 一日一练
  - JS
  - 翻译
categories: 技术
---

> 本文主要翻译自[github amdjs](https://github.com/amdjs/amdjs-api/blob/master/AMD.md)
> 子曰：了解了CommonJS 规范，再了解一下AMD 规范

## AMD 定义

AMD（Asynchronous Module Definition，异步模块定义）指定了一种定义模块的机制，以便可以异步加载模块及其依赖关系。 这尤其适用于模块的同步加载会导致性能，可用性，调试和跨域访问问题的浏览器环境。

## 模块定义
该规范定义了一个可用作自由变量或全局变量的单个函数`define()` 函数
```
define(id?, dependencies?, factory)
```
关于参数：
  1. `id`
    1.1. 是一个字符串字面量。 它指定了正在定义的模块的ID。 这个参数是可选。
  2. `dependencies`
    2.1 是数组字面量，它是正在定义的模块所需的依赖关系。必须在执行模块工厂函数之前解析依赖关系，并将解析值作为参数传递给工厂函数，`dependencies`元素的顺序和`factory`参数一一对应

    2.2. 本规范定义了三个具有不同的特殊依赖项名称。如果中出现`require`，`exports`或`module`的值，则该参数应解析为由CommonJS模块规范定义的相应变量。

    2.3. 依赖关系参数是可选的。如果省略，它应该默认为` ["require", "exports", "module"]`。但是，如果`factory` 函数的arity（长度属性）小于3，那么加载器可能会选择仅使用与该函数的arity或长度相对应的参数数目来调用`factory`。
  3. `factory`
   3.1. 是一个应该被执行来实例化模块或对象的函数。 如果`factory`是一个函数，它应该只执行一次。 如果`factory`参数是一个对象，则该对象应该被指定为模块的导出值。

   3.2. 如果`factory`函数返回一个值（一个对象，函数或强制为true的任何值），则应该将该值指定为模块的导出值。

<!--more-->

## 使用require 和exports
1. 设置模块ID 为`alpha`，并使用`require`，`exports` 已经`beta` 模块
```js
define('alpha', ['require', 'exports', 'beta'], function (require, exports, beta) {
  exports.verb = function() {
    return beta.verb();
    // Or:
    // return require("beta").verb();
  }
})
```
2. 一个返回对象字面量的匿名模块
```js
define(['alpha'], function (alpha) {
  return {
    verb: function () {
      return alpha.verb() + 2
    }
  }
})
```
3. 一个无依赖的模块可以直接定义一个对象字面量
```js
define({
  add: function (x, y) {
    return x + y
  }
})
```
4. 一个使用简单CommonJS wrapping 的模块定义
```js
define(function (require, exports, module) {
  var a = require('a')
  var b = require('b')

  exports.action = function () {}
})
```

## 同CommonJS 的关系
开始在CommonJS wiki上作为传输格式，作为Modules Transport/C，但随着时间的推移它也随之改变，以包含模块定义API。 在CommonJS 列表中没有达成将此API推荐为模块定义API 的共识。 该API被转移到它自己的wiki和讨论组。

只要CommonJS 模块不使用大计算量的、同步的`require('')`调用，AMD就可以用作CommonJS 模块的传输格式。 大多数AMD加载器支持将使用大计算量的、同步的`require('')` CommonJS代码可以转换为使用[回调样式的`require`](https://github.com/amdjs/amdjs-api/blob/master/require.md)。


## 规范的实现
目前主要有两个JavaScript 库实现了AMD 规范：[require.js](http://requirejs.org/) 和[curl.js](https://github.com/cujojs/curl)

## 相关文档
1. [Javascript模块化编程（二）：AMD规范 阮一峰](http://www.ruanyifeng.com/blog/2012/10/asynchronous_module_definition.html)
2. [使用 AMD、CommonJS 及 ES Harmony 编写模块化的 JavaScript ](http://caibaojian.com/modular-javascript.html)
3. [使用 AMD、CommonJS 及 ES Harmony 编写模块化的 JavaScript 原文地址](http://justineo.github.io/singles/writing-modular-js/)
