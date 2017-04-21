layout: draf
title: Lodash 学习-入门
date: 2017-04-21 15:26:59
tags:
---


## 介绍

在阅读《JS函数式编程指南》时，发现其中使用到了Lodash，需要对Lodash 进行初步的入门了解。在其官网上介绍为：

> Loadsh
> A modern JavaScript uitility library delivering modularity, performance & extras.

也就是说Lodash 是一个现代的JavaScript 实用工具库提供了模块化、高性能和其他功能。按照官网的介绍进行安装。

<!--more-->

## 安装

1、在浏览器中直接引入文件
```js
<script src="lodash.js"></script>
```
2、使用npm 安装
```bash
npm i --save lodash
```
3、在Node.js 中使用
```js
// 加载所有构建。
var _ = require('lodash');
// 加载核心构建。
var _ = require('lodash/core');
```

上述主要就是安装了，接下来就是使用了。而在《JS函数式编程指南》中的柯里化（curry）中使用到了`curry` 方法，下面就需要了解一下`curry` 这个方法的介绍以及使用

## curry
`_.curry(func, [arity-func.length])`

创建一个函数，该函数接受一个或多个`func` 参数。当该函数被调用时，如果`func` 所需要传递的所有参数都被提供，则直接返回`func` 所执行的结果。否则继续返回该函数并等待接收剩余的参数。可以使用`func.length` 强制需要累积的参数个数。

**参数**
1.`func`(Function)
需要curry 的函数
2.`[arity=-func.length]`(number)
需要提供给`func` 的参数数量

**返回值**(Function)
返回curry 后的函数

**示例**

```js
var abc = function(a, b, c){
  return [a, b, c]
}

var curried = _.curry(abc)

curried(1)(2)(3)
// => [1, 2, 3]

curried(1, 2)(3)
// => [1, 2, 3]

curried(1, 2, 3)
// => [1, 2, 3]
```

## 参考文档

1. [Lodash 官网](https://lodash.com)
