---
title: 一日一练-JS 了解Istanbul
date: 2018-06-19 09:14:21
tags:
  - 技术
  - 一日一练
  - JS
categories: 技术
---

>子曰：Istanbul 与土耳其伊斯坦布尔的联系

最近在读Vuejs 源码的时候，发现有这样的一句话
```js
/* istanbul ignore if */
```
一开始并没有太在意，只是认为这是一个简单的注释，说是`伊斯坦布尔忽略if`，但是发现还有
```js
/* istanbul ignore next */
```
这样的语句，于是检索了一下，发现Istanbul 是一个代码覆盖率工具，而上面这些注释，是Istanbul 提供的注释代码，允许某些代码不计入覆盖率。

<!--more-->

## Istanbul
[Istanbul](https://github.com/gotwarlost/istanbul) 是JavaScript 程序的代码覆盖率工具。
Istanbul 命名同土耳其的伊斯坦布尔相关的原因是，土耳其地毯闻名世界，而地毯是用来覆盖的。

## 代码覆盖率（code coverage）
我们在测试时，会关心是否所有的代码都测试到了，而这指标就是“代码覆盖率”。它有四个测量纬度。
1. 行覆盖率（line coverage）：是否每一行都执行了。
2. 函数覆盖率（function coverage）：是否每个函数都调用了。
3. 分支覆盖率（branch coverage）：是否每个if 代码块都执行了。
4. 语句覆盖率（statement coverage）：是否每个语句都执行了。

## 入门
1. 使用npm 全局安装
```
npm i -g istanbul
```
2. 覆盖率测试
编写一个`test.js` 文件
```js
var foo = 1
var bar = 2
if (foo > 0) {
  console.log('foo' + (foo + bar))
}
```
  使用`istanbul cover` 命令
```js
$ istanbul cover test.js
foo3
=============================================================================
Writing coverage object [\coverage\coverage.json]
Writing coverage reports at [\coverage]
=============================================================================

=============================== Coverage summary ===============================
Statements   : 100% ( 4/4 )
Branches     : 50% ( 1/2 )
Functions    : 100% ( 0/0 )
Lines        : 100% ( 4/4 )
================================================================================
```
  返回结果显示，test.js 有4个语句（statement），执行了4个；有2个分支（branch），执行了1个 **（默认还有一个else 分支）** ；有0个函数，调用了0个；有4行代码，执行了4行。

  这条命令同时还生成了一个 coverage 子目录，宝行`converage.json`，`lcov.info`,`lcov-report/*html`。其中`lcov-report/*html`是可以在浏览器打开的覆盖率报告，其中有详细信息，到底哪些代码没有覆盖到。
{% asset_img istanbul_0.jpg %}

## 忽略某些代码
笔记开始的代码注释，就是Istanbul 提供的[注释代码]()，允许某些代码不计入覆盖率。
1. 忽略else 语句
```js
/* istanbul ignore else  */
if (foo.hasOwnProperty('bar')) {
  // do something
}
````
  这会忽略else 分支（默认还有一个else 分支）
2. 忽略if 语句
```js
/* istanbul ignore if  */
if (hardToReproduceError)) {
    return callback(hardToReproduceError);
}
```
3. 忽略特定的switch case 语句
```js
switch (foo) {
  case 1: /* some code */; break;
  /* istanbul ignore next */
  case 2: // really difficult to enter in a unit test for some reason
    someCode();
}
```
  在上例中，`case 2` 语句被忽略了
4. 忽略UMD 包裹语句
```js
(function (root, factory) {
  'use strict';
  /* istanbul ignore next */
  if (typeof exports === 'object') {
    // CommonJS
    module.exports = factory();
  } else if (typeof define === 'function' && define.amd) {
    // AMD. Register as an anonymous module.
    define(factory);
  } else {
    // Browser globals
    root.module = factory();
  }
})(this, fn);
```
  整个函数表达式被忽略了。

## 参考文档
1. [代码覆盖率工具 Istanbul 入门教程 阮一峰](http://www.ruanyifeng.com/blog/2015/06/istanbul.html)
2. [为了覆盖目的而忽略代码](https://github.com/gotwarlost/istanbul/blob/master/ignoring-code-for-coverage.md)
