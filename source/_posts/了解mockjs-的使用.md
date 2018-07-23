---
title: 了解mockjs 的使用
date: 2018-07-19 18:05:03
tags:
  - 技术
  - mockjs
  - easy-mock
categories: 技术
---

> 子曰：Mock.js 生成随机数据，拦截Ajax 请求。

作为前端开发者，需要独立于后端进行开发，推荐使用Mock.js 来生成随机数据，拦截Ajax 请求。

## 简介
0x00. 官网地址：http://mockjs.com/
0x01. github 地址：https://github.com/nuysoft/Mock/tree/master
0x02. 特点
1.前后端分离。让前端工程师独立于后端进行开发。
2.开发无侵入。不需要修改既有代码，就可以拦截Ajax 请求，返回模拟的响应数据。
3.数据类型丰富。支持生成随机的文本、数字、布尔值、日期、邮箱、连接、图片、颜色等。
4.增加单元测试的真实性。通过随机数据，模拟各种场景。
5.用法简单。符号直觉的接口。
6.方便扩展。支持扩展更多数据类型，支持自定义函数和正则。

<!--more-->

## 开始安装
支持多种使用方式，例如Nodejs、Bower、RequireJS、SeaJS 和Random CLI。这里介绍Nodejs 的使用场景。
```bash
# 安装
npm install mockjs
```
```js
// 使用Mock
var Mock = require('mockjs')
var data = Mock.mock({
  // 属性list 的值是一个数组，其中含有1 到10 个元素
  'list|1-10': [{
    // 属性id 是一个自增数，起始值为1， 每次增1
    'id|+1': 1
  }]
})
// 输出结果
console.log(JSON.stringify(data, null, 4))
```

## 语法规范
Mock.js 的语法规范包括两部分：
1.数据模版定义规范（Data Template Definition，DTD）
2.数据占位符定义规范（Data Placeholder Definition， DPD）

### 数据模版定义规范 DTD
**数据模块中的每个属性由3 部分构成：属性名、生成规则、属性值：**
```js
// 属性名 name
// 生成规则 rule
// 属性值 value
'name|rule': value
```
**注意**
* `属性名` 和`生成规则` 之间有竖线`|` 分隔。
* `生成规则` 是可选的。
* `生成规则` 是有7 中格式。
  * `'name|min-max': value`
  * `'name|count': value`
  * `'name|min-max.dmin-dmax': value`
  * `'name|min-max.dcount': value`
  * `'name|count.dmin-dmax': value`
  * `'name|count.dcount': value`
  * `'name|+step': value`
* `生成规则` 的含义需要依赖 `属性值` 的类型才能确定。
* `属性值` 中可以含有`@占位符`。
* `属性值` 中还指定了最终值的初始值和类型。

## 结合easy-mock
如何不想在本地搭建mockjs，还可以结合[easy-mock](https://www.easy-mock.com)，使用easy-mock 进行接口模拟。

## 参考文档
1.[mock.js](http://mockjs.com/)
2.[easy-mock](https://www.easy-mock.com)
