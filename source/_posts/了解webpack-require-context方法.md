---
title: 了解webpack require.context方法
date: 2018-07-19 16:06:01
tags:
  - 技术
  - webpack
categories: 技术
---

> 子曰：了解webpack 中require.context 方法

`require.context` 是使用webpack 编译代码的方法。了解`require.context` 的起因是在查看代码时，发现了如下使用方式
```js
const icons = {}

// 0 身体--------
const result0s = require.context('../assets/icon/0/s', false, /\.png$/)
const result0b = require.context('../assets/icon/0/b', false, /\.png$/)

icons['0'] = {}
icons['0']['s'] =  result0s
    .keys()
    .map(key => result0s(key))

icons['0']['b'] =  result0b
    .keys()
    .map(key => result0b(key))
```
上述方法使用`require.context` 进行了图片资源的而引入

<!--more-->

**0x00 语法**
```js
require.context(
  directory: String,
  includeSubdirs: Boolean /* 可选，默认为true */,
  filter: RegExp /* 可选 */
)
```
使用`directory` 路径、`includeSubdirs` 选项和`filter` 来指定一系列完整的依赖关系，便于更细粒度的控制模块引入。后面可以很容易地进行解析：

**0x01 示例**
```
var context = require.context('components', true, /\.html$\/)
var componentA = context.resolve('componentA')
```

**0x02 参考文档**
1.[webpack 官方文档](https://webpack.js.org/api/module-methods/#require-context)
