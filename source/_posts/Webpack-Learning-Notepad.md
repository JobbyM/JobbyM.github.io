---
title: Webpack Learning Notepad
date: 2016-11-29 09:43:19
tags:
  - webpack
  - 翻译
  - 技术
categories: 技术
comments: true
---

## Getting Start

webpack is a tool to build JavaScript modules in your application

<!--more-->

## Creating a bundle

创建一个demo 目录去尝试webpack。**安装webpack**
```bash
mkdir webpack-demo && cd webpack-demo
npm init -y
npm install --save-dev webpack
webpack --help # Shows a list of valid cli commands
npm install --save lodash
```
现在创建一个`index.js` 文件
**app/index.js**
```js
function component(){
  var element = document.createElement('div');

  /* lodash is required for the next line to work*/
  element.innerHTML = _.map(['Hello','webpack'], function(item){
    return item + ' ';
  })

  return element;
}

document.body.appenChild(component())
```
需要下面的html 文件来运行上面这段代码
**index.html**
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Webpack demo</title>
  <script src="https://unpkg.com/lodash@4.16.6" type="text/javascript"></script>
</head>
<body>
  <script scr="app/index.js" type="text/javascript"></script>
</body>
</html>
```
在这个例子中，在script 标签中包含了依赖。
`index.js` 依赖 `lodash` 运行。
为了在`index.js` 文件中使用`lodash`，我们只需要引入`lodash`.
**app/index.js**
```js
import _ from 'lodash'

function component(){
...
```
同时我们需要修改`index.html`
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Webpack demo</title>
  <!-- <script src="https://unpkg.com/lodash@4.16.6" type="text/javascript"></script> -->
</head>
<body>
  <script scr="dist/bundle.js" type="text/javascript"></script>
</body>
</html>
```
现在`index.js` 明确的需要 `lodash`，绑定`_`（没有全局污染）。
现在项目文件夹运行`webpack`，通过入口文件`index.js` 生成'bundle.js' 文件。
```bash
webpack app/index.js dist/bundle.js

Hash: a3c861a7d42fc8944524
Version: webpack 2.2.0
Time: 90ms
   Asset     Size  Chunks             Chunk Names
index.js  1.56 kB       0  [emitted]  main
   [0] ./app/index.js 170 bytes {0} [built]
```
## Using webpack with a config
对于更复杂的配置，我们可以使用配置文件
**webpack.config.js**
```js
module.exports = {
  entry: './app/index.js',
  output: {
    filename: 'bundle.js',
    path: './dist'
  }
}
```
通过webpack 来运行
```bash
webpack --config webpack.config.js

Hash: a3c861a7d42fc8944524
Version: webpack 2.2.0
Time: 90ms
   Asset     Size  Chunks             Chunk Names
index.js  1.56 kB       0  [emitted]  main
   [0] ./app/index.js 170 bytes {0} [built]
```
> 如果`webpack.config.js` 文件存在，`webpack` 命令默认使用此配置文件

## Using webpack with npm
通过调整package.json 文件来简化使用webpack 命令
```json
{
    ...
    "scripts": {
          "build": "webpack"
     },
    ...
}
```
同时使用`npm run build` 命令实现webpack 功能

## 参考文档

1. [webpack 官方网站](https://webpack.js.org/get-started/)
