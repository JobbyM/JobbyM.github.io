---
title: 阅读aframe-spritesheet-component，了解A-Frame创建组件
date: 2017-03-13 10:51:15
comments: true
tags:
  - 技术
  - 鬼懿IT-干货分享
categories: 技术

---

## 简介

A-Frame 是构建虚拟现实体验的web 框架。此框架由Mozilla VR 发起，目的是使WebVR 内容的创建更容易、更快速、更易于访问。A-Frame 是自由开源的同时拥有一个深受欢迎的社区以及蓬勃发展的工具和组件生态系统。

此雪碧图组件（[源码](https://github.com/EkoLabs/aframe-spritesheet-component)）就是A-Frame 生态中实现的一个组件，雪碧图是制作预渲染动画的常用方法，而该组件可以将雪碧图加载到`<a-frame>` 元素，从而在A-Frame 中来进行动画控制。


首先我们了解一下雪碧图组件在A-Frame 中的安装以及使用，最后就再根据源码来对此流程图进行分析

{% asset_img create-a-component.jpg %}

<!--more-->

## DEMO


使用雪碧图组件的两种方式，一种是[行列形式](https://ekolabs.github.io/aframe-spritesheet-component/examples/rowscols/)，效果如图
{% asset_img A-Frame-demo-1.gif %}

所谓的行列式就是需要提供一个雪碧图，这个雪碧图是由大小相等图片组成，像这样
{% asset_img npc_piggy_explorer__x1_walk_png_1354837301.png %}

这样我们在使用的时候，直接声明为几行几列就可以了
```js
<a-image src="npc_piggy_explorer__x1_walk_png_1354837301.png"
         sprite-sheet="cols:8; rows: 3; progress: 0;"
         position="0 1.5 -4"></a-image>
```

其中的`src` 为雪碧图地址；
`sprite-sheet` 就是雪碧图组件，其中的`cols:8; rows: 3; progress: 0;` 就是雪碧图组件的所需要的参数，为3行8列；
`position` 为在3D空间中的位置，使用右手坐标系，三个值分别代表x 轴、y 轴、z 轴。

另一种是[JSON 数据格式](https://ekolabs.github.io/aframe-spritesheet-component/examples/json/),效果如图
{% asset_img A-Frame-demo-2.gif %}

所谓的JSON 格式就是由于雪碧图并不是由多个大小相等的图片组层，像这样
{% asset_img toaster.png %}

所以我们需要以json 的格式进行描述，
```json
{
  "frames": {
    "0.png":
    {
    	"frame": {"x":191,"y":105,"w":75,"h":73},
    	"rotated": false,
    	"trimmed": true,
    	"spriteSourceSize": {"x":22,"y":0,"w":75,"h":73},
    	"sourceSize": {"w":115,"h":73},
    	"pivot": {"x":0.5,"y":0.5}
    },
    "1.png":
    {
    	"frame": {"x":191,"y":105,"w":75,"h":73},
    	"rotated": false,
    	"trimmed": true,
    	"spriteSourceSize": {"x":22,"y":0,"w":75,"h":73},
    	"sourceSize": {"w":115,"h":73},
    	"pivot": {"x":0.5,"y":0.5}
    }
  }
}
```

在使用的时候，需要通过`dataUrl: toaster.json` 来指明数据位置
```
<a-image src="toaster.png"
         sprite-sheet="dataUrl: toaster.json; progress: 0;"
         width="1"
         height="1"
         position="0 0 -4"></a-image>
```


顺便推荐一个录屏存为gif的工具[ScreenToGif](http://screentogif.codeplex.com/)

## 使用方法

使用方法也分为两种，一种是直接在浏览器中通过`<script>` 标签引入
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

## 源码分析

在这里主要根据代码进行介绍一下A-Frame，以及实现一个组件。

更多详细信息，请参考这里，[A-FRAME 介绍](https://aframe.io/docs/0.5.0/introduction/)，和[实现一个组件介绍](https://aframe.io/docs/0.5.0/guides/writing-a-component.html)

### 第一步：注册组件

使用AFRAME.registerComponent 注册一个`sprite-sheet` 组件，[线上代码](https://github.com/EkoLabs/aframe-spritesheet-component/blob/master/index.js#L6)
```js
let SpriteSheet = AFRAME.registerComponent('sprite-sheet', {
  ...
}
```

### 第二步：定义属性

使用`schema` 定义组件的属性，`type` 定义属性类型，`default` 定义属性的默认值，[线上代码](https://github.com/EkoLabs/aframe-spritesheet-component/blob/master/index.js#L7-L15)
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
这里定义了`progress` 、`cols`、`rows` 等等相关属性，并分别赋予了默认值。

`sprite-sheet ` 在页面中直接作为HTML 特性进行声明，同时可以将相关属性值以行内CSS 样式的形式进行传入（name/value 键值对以冒号（：）分割，键值对之间以分号（；）分割），[线上代码](https://github.com/EkoLabs/aframe-spritesheet-component/blob/master/examples/rowscols/index.html#L10-L12)
```html
      <a-image src="npc_piggy_explorer__x1_walk_png_1354837301.png"
               sprite-sheet="cols:8; rows: 3; progress: 0;"
               position="0 1.5 -4"></a-image>
```


### 第三步：初始化

> 在生命周期处理函数方法（lifecycle handler method）中对组件进行初始化、更新属性以及销毁。

`.init()` 初始化方法，仅在组件第一次被插入实体时调用，[线上代码](https://github.com/EkoLabs/aframe-spritesheet-component/blob/master/index.js#L18-L67)
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

### 第四步：更新

`.update()` 更新处理函数去处理属性的更新，[线上代码](https://github.com/EkoLabs/aframe-spritesheet-component/blob/master/index.js#L69-L81)
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

### 第五步：销毁

`.remove()` 销毁处理函数在组件被移除时调用，[线上代码](https://github.com/EkoLabs/aframe-spritesheet-component/blob/master/index.js#L83-L94)
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

上述相关代码就是如何实现一个A-Frame 组件流程。

## 总结

1. 主要是对A-Frame 这个虚拟现实体验 web 框架进行了初步的熟悉了解
2. A-Frame 组件创建同Vue.js 构建组件在形式上类似，都会封装数据、方法，同时提供一系列的生命周期方法来对组件进行管理。在使用上有所区别一个是作为HTML 特性，一个是作为HTML Tag 来使用。
3. 本文中的名词翻译、名词解释、组件创建流程等都是基于自己的理解，如有问题请多多交流。
