---
title: 一日一练-JS 了解CommonJS 规范
date: 2018-04-24 14:49:33
comments: true
tags:
  - 技术
  - 一日一练
  - JS
categories: 技术
---

> 子曰：CMD、AMD傻傻分不清，那就先了解一下CommonJS规范
> 摘自《深入浅出Node.js》（田永强@朴灵 ）

## CommonJS 规范
CommonJS 规范为JavaScript 制定了一个美好的愿景--希望JavaScript 能够在任何地方运行

## CommonJS 的出发点
主要是为了拟补当前JavaScript 没有标准的缺陷，以达到像Python 具备开发大型应用的能力，而不是停留在小脚本程序的阶段。期望用CommonJS API 写出的应用可以具备跨宿主环境执行的能力，这样不仅可以利用JavaScript 开发客户端应用，还可以编写以下应用
  1. 服务器端JavaScript 应用程序
  2. 命令行工具
  3. 桌面图形界面应用程序
  4. 混合应用

{% asset_img CommonJS.png %}

<!--more-->

## CommonJS 的模块规范
CommonJS 对模块的定义十分简单，主要分为模块引用，模块定义和模块标识3 部分。
1. 模块引用
```js
var math = require('math')
```
在CommonJS 规范中，存在`require()` 方法，这个方法接受模块标识，以此引入一个模块的API 到当前上下文中。

2. 模块定义
上下文提供了`exports` 对象用于导出当前模块的方法或者变量，并且它是唯一导出的出口。在模块中，还存在一个`module` 对象，它代表模块自身，而`exports` 是`module` 的属性。
在Node 中，一个文件就是一个模块，将方法挂载在`exports` 对象上作为属性即可定义导出的方式：
```js
exports.add = function () {
  return 0
}
```

3. 模块标识
模块标识其实就是传递给`require()` 方法的参数，它必须是符合小驼峰命名的字符串，或者以`.`，`..` 开头的相对路径，或者绝对路径。它可以没有文件名后缀`.js`

## Node 中模块的实现
Node 采用的CommonJS 模块规范。

我们知道每个模块文件中存在着`require`、 `exports`、 `module` 这3 个变量，甚至在Node 的API 文档中，我们知道每个模块还有`__filename`、 `__dirname` 这两个变量。

事实上，在编译的过程中，Node 对获取的JavaScript 文件内容进行了头尾封装。在头部添加了`(function(exports, require, module, __filename, __dirname){\n`，在尾部添加了 `\n});`
```js
(function(exports, require, module, __filename, __dirname){
  var math = require('math')
  exports.area = function (raidus) {
    return math.PI * radius* radius
  }
});
```

`exports` 对象是通过形参的方式传入的，直接赋值形参会改变形参的引用，但并不能改变作用域外的值。
```js
var change = function (a) {
  a = 100
  console.log(a) // => 100
}
var a = 10
change(a)
console.log(a) // => 10
```
如果要达到`require` 引入一个类的效果，请赋值给`module.exports` 对象。

## 模块的侧重点
前后端JavaScript 分别搁置在HTTP 的两端，它们扮演的角色不同。浏览器端的JS 需要经历从同一个服务器分发到多个客户端执行，而服务器端JS 则是相同的代码需要多次执行。

Node 的模块引入过程中，几乎全部是同步的。如果前端也采用同步的方式引入，那将会在用户体验上造成很大的问题。UI 在初始化过程中需要花费很多时间来等待脚本加载完成。

AMD（Asynchronous Module Definition 异步模块定义）规范在前端应用场景中胜出。除此之外，还有玉伯定义的CMD 规范。


## 参考文档
1. [JavaScript 标准参考教程（alpha） 阮一峰 CommonJS规范](http://javascript.ruanyifeng.com/nodejs/module.html)
