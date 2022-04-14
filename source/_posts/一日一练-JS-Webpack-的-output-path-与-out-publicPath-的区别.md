---
title: 一日一练-JS Webpack 的 output.path 与 out.publicPath 的区别
date: 2022-04-13 14:14:07
tags:
  - 技术
  - 一日一练
  - Webpack
  - 转载
categories: 技术
---

本文转载自[Webpack 的output中 path与publicPath的区别详解](https://www.jianshu.com/p/9f5d03b4afb4)，有部分删节，看原文请到原地址。

## output 中的 path 和 publicPath
* path: 项目打包所存放到的路径，此路径中包含项目所有文件和目录。应该是一个绝对路径。
* publicPath: 静态文件打包存放的目录。静态文件是指 `img` 的 `src`，`link`，`script` 标签等所指向的文件。`publicPath` 是相对于 `path` 所在的路径。是一个相对路径。

<!--more-->

## publicPath 大白话
对于按需加载( `on-demand-load` )或加载外部资源( `external resources`)（如图片、文件等）来说，`output.publicPath` 是很重要的选项。如果指定了一个错误的值，则在加载这些资源时会收到 `404` 错误。

此选项指定在浏览器中所引用的(此输出目录对应的公开 `URL`)。相对 `URL` ( `relative URL`) 会被相对于 `HTML` 页面（或 `<base>` 标签）解析。

`publicPath` 是用于引导静态资源(`js`, `css`, `img`)，比如在页面引入了`background-image:url(’/static/people.png’);` 这时候这里的路径就会相对publicPath做调整；指的是将来这些静态资源将会放到哪，由线上静态资源存放的路径决定的

`publicPath` 取决于根目录 `path` 的位置，因为打包的文件都在 `path` 目录了，这些文件的引用都是基于该目录的。假设 `path` 为 `public`，引用的图片路径是 `'./img.png'`，如果`publicPath` 为 `'/'`，图片显示不了，因为图片都打包放在了 `dist` 中，那么你就要把 `publicPath` 设置为 `/dist`。

举个例子：
假设你将这个工程部署在服务器 `http://server/`
通过将 `output.publicPath` 设置为 `/assets/`，这个工程将会在 `http://server/assets/` 找到 `webpack` 资源。
在这种前提下，所有与 `webpack` 相关的路径都会被重写成以 `/assets/` 开头。
```js
src="picture.jpg" Re-writes ➡ src="/assets/picture.jpg"
Accessed by: (http://server/assets/picture.jpg)
src="/img/picture.jpg" Re-writes ➡ src="/assets/img/picture.jpg"
Accessed by: (http://server/assets/img/picture.jpg)
```

## devServer 中的publicPath
此路径下的打包文件可在浏览器中访问。

假设服务器运行在 `http://localhost:8080` 并且 `output.filename` 被设置为 `bundle.js`。默认 `devServer.publicPath` 是 `'/'`，所以你的包(`bundle`)可以通过 `http://localhost:8080/bundle.js` 访问。

修改 `devServer.publicPath`，将 `bundle` 放在指定目录下：
```js
module.exports = {
  //...
  devServer: {
    publicPath: '/assets/'  // 现在可以通过 http://localhost:8080/assets/bundle.js 访问 bundle。
  }
};  
```

`dev` 时 在内存中的路径，大多时候，`output.publicPath` 和 `devserver.publicPath` 保持一致。

## 最后
如果你在用 `style-loader` 或者 `css sourceMap`，你就需要设置 `publicPath`。把它设置成服务器地址的绝对路径，比如 `http://server/assets/`，这样资源可以被正确加载。

