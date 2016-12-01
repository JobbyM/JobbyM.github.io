---
title: simplest redux example
date: 2016-12-01 15:45:01
tags:
  - Redux
  - webpack
  - babel
comments: true
updated:
categories: 技术
---

## 简介

这是按照[redux 中文文档](http://cn.redux.js.org/index.html redux) 进行实现的最简redux 实例
1. Redux
  {% cq %} Redux is a predictabel state container for JavaScript apps. {% endcq %}
2. webpack
  {% cq %} webpack is a module bundler. {% endcq %}
3. babel
  {% cq %} Babel is a compiler for writing next generation JavaScript. {% endcq %}

<!--more-->

## 操作步骤

1. 初始化
```bash
mkdir simplest-redux-example
cd simplest-redux-example
npm init
```
2. 安装redux 包
```bash
npm install --save redux
```
3. 新建index.js
```js
import { createStore } from 'redux';

/**
* 这是一个reducer，形式为(state, action) => state 的纯函数
* 描述了action 如何把state 转变成下一个state
*
* state 的形式取决于你，可以是基本类型、数组、对象、
* 甚至是Immutabale.js 生成的数据结构。唯一的要点是
* 当state 变化时需要返回全新的对象，而不是修改传入的参数
*
* 下面例子是用`switch` 语句和字符串做判断，但你可以写帮助类（helper）
* 根据不同的约定（如方法映射）来判断，只要适用你的项目即可
*/
function counter(state = 0, action){
  switch(action.type){
    case 'INCREMENT':
      return state + 1;
    case 'DECREMENT':
      return state - 1;
    default:
      return state;
  }
}

// 创建Redux store 来存放应用的状态
// API 是 { subscribe, dispatch, getStatet}
let store = createStore(counter);

// 可以手动订阅更新，也可以事件绑定到视图层
store.subscribe(() => {
  console.log(store.getState())
})

// 改变内部state 唯一方法是dispatch 一个action
// action 可以被序列化，用日志记录和存储下来，后期还可以以回放的形式执行
store.dispatch({ type: 'INCREMENT' })
// 1
store.dispatch({ type: 'INCREMENT' })
// 2
store.dispatch({ type: 'DECREMENT' })
// 1
```
4. 安装babel
```bash
npm install babel-loader babel-core babel-preset-2015 --save-dev
```
5. 安装webpack
```bash
npm install webpack --save-dev
```
6. 新增webpack.config.js 文件
```js
module.exports = {
  entry: './index.js',
  output: {
    filename: 'bundle.js',
    path: './'
  },
  devtool: 'inline-source-map',
  module: {
    loaders: [
      {
        test: /\.js?$/,
        exclude: /(node_modules|bower_components)/,
        loader: 'babel-loader?presets[]=es2015'
      }
    ]
  }
}

```
7. 修改package.json
```js
     ...
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build": "webpack",
    "start": "node bundle.js"
  },
     ...
```
8. 运行`npm run build`
```bash
$ npm run build

> simplest-redux-example@1.0.0 build D:\nodejs\simplest-redux-example
> webpack

Hash: 9341ad45a50aef2b78d9
Version: webpack 1.13.3
Time: 1135ms
    Asset    Size  Chunks             Chunk Names
bundle.js  108 kB       0  [emitted]  main
    + 23 hidden modules

```
9. 运行`npm start`
```bash
$ npm start

> simplest-redux-example@1.0.0 start D:\nodejs\simplest-redux-example
> node bundle.js

1
2
1

```

## 参考文档

1. [redux 中文文档](http://cn.redux.js.org/index.html redux)
2. [babel-loader](https://github.com/babel/babel-loader)
