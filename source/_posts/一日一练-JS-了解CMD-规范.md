---
title: 一日一练-JS 了解CMD 规范
date: 2018-04-25 08:37:07
comments: true
tags:
  - 技术
  - 一日一练
  - JS
  - 翻译
categories: 技术
---

> 子曰：了解了AMD，之后了解CMD 规范
> 翻译自[通用模块定义 草案](https://github.com/cmdjs/specification/blob/master/draft/module.md)

## CMD 定义
CMD（Common Module Definition，通用模块定义）本规范说明了如何编写模块以便在 **基于浏览器** 的环境中实现互操作。通过暗示，本规范定义了模块系统为支持可互操作模块而必须提供的最小功能。

* 模块是单例模式。
* 在模块范围内的不应引入新自由变量。
* 执行必须是懒惰的。

## 模块定义
一个模块是用`define` 关键字定义的，这是一个函数。
```js
define(factory)
```
1. `define` 函数接受一个参数，即模块`factory`。
2. `factory` 可能是一个函数或其他有效的值。
3. 如果`factory` 是一个函数，则该函数的前三个参数（如果指定）必须依次为`require`，`exports`和`module`。
4. 如果`factory` 不是一个函数，那么模块的导出就被设置为该对象。

## 模块上下文
在一个模块中，有三个自由变量：`require`，`exports`和`module`。
```js
define( function (require, exports, module) {
  //模块代码在这里
})
```

## `require` 函数
1. `require` 是一个函数
  1.1. `require` 接受一个模块标识符。
  1.2. `require` 返回外部模块的输出API。
  1.3. 如果请求的模块不能被返回，那么`require`应该返回`null`。
2. `require.async` 是一个函数
  2.1. `require.async` 接受模块标识符列表和一个可选的回调函数。
  2.2. 回调函数接收模块导出作为函数参数，按照与第一个参数中的顺序相同的顺序列出。
  2.3. 如果被请求的模块不能被返回，则回调应该相应地收到`null`。

## `exports` 对象
在一个模块中，有一个名为`exports` 的自由变量，这是模块在执行时可以添加其API的对象。

## `module` 对象
1. `module.uri`
  模块uri 的解析。
2. `module.dependencies`
  模块所需的模块标识符列表。
3. `module.exports`
  模块的导出API。它与`exports` 对象相同。

## 模块标识符
1. 模块标识符是且必须是 **字面量** 字符串。
2. 模块标识符可能没有像`.js`这样的文件扩展名。
3. 模块标识符应该是像`foo-bar` 使用`-` 进行连接。
4. 模块标识符可以是相对路径，如`./foo` 和`../bar`。

## 示例代码
**一个典型的例子**
math.js
```js
define(function (require, exports, module) {
  exports.add = function () {
    var sum = 0, i = 0, args = argments, l = args.length
    while (i < l) {
      sum += args[i ++]
    }
    return sum
  }
})
```
increment.js
```js
define( function (require, exports, module) {
  var add = require('math').add
  exports.increment = function (val) {
    return add(val, 1)
  }
})
```
program.js
```js
define(function (require, exports, module) {
  var inc = require('increment').increment
  var a = 1
  inc(a) // 2

  module.id = 'program'
})
```
**用非函数factory包装模块**
object-data.js
```js
define({
  foo: 'bar'
})
```
array-data.js
```js
define([
  'foo',
  'bar'
])
```
string-data.js
```js
define('foo bar')
```

## 相关文档
1. [CMD 模块定义规范](https://github.com/seajs/seajs/issues/242)
2. [RequireJS 和Sea.js 异同](https://github.com/seajs/seajs/issues/277)
