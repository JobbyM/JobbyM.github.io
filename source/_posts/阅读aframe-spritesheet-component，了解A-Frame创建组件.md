---
title: 阅读aframe-spritesheet-component，了解A-Frame创建组件
date: 2017-03-13 10:51:15
comments: true
tags:
  - 技术
  - 鬼懿IT-干货分享
categories: 技术

---

## What

这是A-Frame 精灵序列图（Spritesheet） 组件，一个支持A-Frame 的动画精灵序列图组件。

## Demo

支持两种方式，一种是[行列形式](https://ekolabs.github.io/aframe-spritesheet-component/examples/rowscols/)，另一种是[JSON 数据格式](https://ekolabs.github.io/aframe-spritesheet-component/examples/json/)

<!--more-->

## How to Use

使用方式也分为两种，一种是直接在浏览器中通过`<script>` 标签引入
```html
<head>
  <title>My A-Frame Scene</title>
  <script src="https://aframe.io/releases/0.5.0/aframe.min.js"></script>
  <script src="https://unpkg.com/aframe-spritesheet-component/dist/aframe-spritesheet-component.min.js"></script>
</head>

<body>
  <a-scene>
    <!-- rows/cols format-->
    <a-image src="spritesheet.png" sprite-sheet="cols:8; rows: 3; progress: 0;"></a-image>
    <!-- json format -->
    <a-image src="spritesheet.png" sprite-sheet="dataUrl: spritesheet.json; progress: 0;" ></a-image>
  </a-scene>
</body>
```
另一种就是通过npm 进行安装
```cmd
npm install aframe-spritesheet-component
```
然后通过`require` 引入
```js
require('aframe');
require('aframe-spritesheet-component');
```

## Code Analysis

在这里主要根据代码进行介绍一下A-Frame

> A web framework for building virtual reality expriences

，以及实现一个组件。

更多详细信息，请参考这里，[A-FRAME 介绍](https://aframe.io/docs/0.5.0/introduction/)，和[实现一个组件介绍](https://aframe.io/docs/0.5.0/guides/writing-a-component.html)

1、使用AFRAME.registerComponent 注册一个`sprite-sheet` 组件，[线上代码](https://github.com/EkoLabs/aframe-spritesheet-component/blob/master/index.js#L6)
```js
let SpriteSheet = AFRAME.registerComponent('sprite-sheet', {
  ...
}
```

2、使用`schema` 定义组件的属性，`type` 定义属性类型，`default` 定义属性的默认值，[线上代码](https://github.com/EkoLabs/aframe-spritesheet-component/blob/master/index.js#L7-L15)
```js
    schema: {
        progress: { type: 'number', default: 0 },
        cols: { type: 'number', default: 1 },
        rows: { type: 'number', default: 1 },
        firstFrame: { type: 'number', default: 0 },
        lastFrame: { type: 'number', default: null },
        cloneTexture: { default: false },
        dataUrl: { type: 'string', default: null }
    },
```
这里定义了`progress` 、`cols`、`rows` 等等相关属性，并分别赋予了响应的默认值。

`sprite-sheet ` 在页面中直接作为HTML 特性进行声明，同时可以将相关属性值以行内CSS 样式的形式进行传入（name/value 键值对以冒号（：）分割，键值对之间以分号（；）分割），[线上代码](https://github.com/EkoLabs/aframe-spritesheet-component/blob/master/examples/rowscols/index.html#L10-L12)
```html
      <a-image src="npc_piggy_explorer__x1_walk_png_1354837301.png"
               sprite-sheet="cols:8; rows: 3; progress: 0;"
               position="0 1.5 -4"></a-image>
```

3、在生命周期处理函数方法（lifecycle handler method）中对组件进行初始化、更新属性以及销毁。

4、`.init()` 初始化方法，仅在组件第一次被插入实体时调用，[线上代码](https://github.com/EkoLabs/aframe-spritesheet-component/blob/master/index.js#L18-L67)
```js
init: function() {

        // if specified load spritesheet json data
        if (this.data.dataUrl) {
            this.mapCanvas = document.createElement( 'canvas' );
            ...

            // callback for when the image has loaded
            this.el.addEventListener('materialtextureloaded', () => {
                this.imageLoaded = true;

                ...
            });

        } else {
            // use rows and cols
            this.numFrames = this.data.rows * this.data.cols;

            // callback for when the image has loaded
            this.el.addEventListener('materialtextureloaded', () => {
                this.imageLoaded = true;

                ...
            });

        }

        this.currentFrame = 0;
    },
```
在这里根据`this.data.dataUrl` 的值，来进行区分是使用Demo 中的[行列形式](https://ekolabs.github.io/aframe-spritesheet-component/examples/rowscols/)，还是[JSON 数据格式](https://ekolabs.github.io/aframe-spritesheet-component/examples/json/) 进行初始化相关属性。

**注意**：组件的属性类型值可以通过`this.data` 获取，[线上代码](https://github.com/EkoLabs/aframe-spritesheet-component/blob/master/index.js#L24)
```js
if(this.data.dataUrl){
```
通过`this.el` 引用组件的实体，可以在上面添加事件监听函数，[线上代码](https://github.com/EkoLabs/aframe-spritesheet-component/blob/master/index.js#L31)
```js
            this.el.addEventListener('materialtextureloaded', () => {
```

5、`.update()` 更新处理函数去处理属性的更新，[线上代码](https://github.com/EkoLabs/aframe-spritesheet-component/blob/master/index.js#L69-L81)
```js
    update: function() {
        // no actual animation
        if (!this.framesData && this.data.firstFrame == this.data.lastFrame) return;

        // if no last frame is specified use the number of available frames
        let lastFrame = this.data.lastFrame ? this.data.lastFrame : this.numFrames - 1;

        this.currentFrame = Math.round(this.data.progress * (lastFrame - this.data.firstFrame)) + this.data.firstFrame;
        this.adjustTexture(this.currentFrame);
    },
```

6、`.remove()` 销毁处理函数在组件被移除时调用，[线上代码](https://github.com/EkoLabs/aframe-spritesheet-component/blob/master/index.js#L83-L94)
```js
    remove: function() {
        // Cleanup
        this.mapCanvas = null;
        this.textureCtx = null;
        this.texture = null;
        this.spriteSheetImage = null;
        this.spriteSheetData = null;
        this.framesData = null;
    },
```
这里主要是清理相关属性，将其赋值为null。

上述代码相关就是如何写一个A-Frame 组件。下面主要就是获取精灵序列图数据的获取`getSpriteSheetData()` 、调整纹理`adjustTexture()` ，根据数据形式的不同调用不同的调整方法`adjustFrameByRowsCols()`、`adjustFrameBySpriteSheet()` 。

## Summary

1. 主要是对A-Frame 这个虚拟现实体验 web 框架进行了初步的熟悉了解
2. A-Frame 组件创建同Vue.js 构建组件在形式上类似，都会封装数据、方法，同时提供一系列的生命周期方法来对组件进行管理。在使用上有所区别一个是作为HTML 特性，一个是作为HTML Tag 来使用。
3. 本文主要是基于自己的理解进行分析，如有疏漏之处请多多指教。


## 源文文档

1. [源码](https://github.com/EkoLabs/aframe-spritesheet-component)
