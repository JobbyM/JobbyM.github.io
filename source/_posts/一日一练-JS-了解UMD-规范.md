---
title: 一日一练-JS 了解UMD 规范
date: 2018-04-25 10:24:15
comments: true
tags:
  - 技术
  - 一日一练
  - JS
  - 翻译
categories: 技术
---

> 子曰：{AMD + CommonJS} = UMD
> 翻译自[UMD github](https://github.com/umdjs/umd)

## UMD（Universal Module Definition，通用模块定义）
该库正式化了用于JavaScript 模块的UMD API的设计和实现。这些模块能够在任何地方工作，无论是在客户端，服务器还是其他地方。

UMD 模式通常会尝试提供适配当前最流行的脚本加载器（例如RequireJS等）的兼容性。在许多情况下，它使用[AMD](https://github.com/amdjs/amdjs-api/wiki/AMD)作为基础，并添加了特殊方式来处理[CommonJS](http://wiki.commonjs.org/wiki/CommonJS)兼容性。
> UMD的实现很简单，先判断是否支持NodeJS模块格式（exports是否存在），存在则使用NodeJS模块格式。
> 再判断是否支持AMD（define是否存在），存在则使用AMD方式加载模块。前两个都不存在，则将模块公开的全局（window或global）。

<!--more-->

## Variations （变体）
**常规模块**
1. [amdWeb.js](https://github.com/umdjs/umd/blob/master/templates/amdWeb.js) - 定义一个适用于AMD 和浏览器全局的模块。如果你还希望在AMD处于运行状态时导出全局（如果你正在加载其他仍然期望全局的脚本，则可以使用），请使用[amdWebGlobal.js](https://github.com/umdjs/umd/blob/master/templates/amdWebGlobal.js)。
2. [returnExports.js](https://github.com/umdjs/umd/blob/master/templates/returnExports.js) - 定义一个在Node，AMD 和浏览器全局工作的模块。如果你还希望在AMD 处于运行状态时导出全局（如果你正在加载其他仍然期望全局的脚本，则可以使用），请使用[returnExportsGlobal.js](https://github.com/umdjs/umd/blob/master/templates/returnExportsGlobal.js)。
3. [commonjsStrict.js](https://github.com/umdjs/umd/blob/master/templates/commonjsStrict.js) - 定义一个与更多CommonJS运行时一起工作的模块，以及具有循环依赖性的模块。如果你还希望在AMD处于运行状态时导出全局（如果你正在加载其他仍然期望全局的脚本，则可以使用），请使用[commonjsStrictGlobal.js](https://github.com/umdjs/umd/blob/master/templates/commonjsStrictGlobal.js)

**jQuery 插件**
1. [jqueryPlugin.js](https://github.com/umdjs/umd/blob/master/templates/jqueryPlugin.js) - 定义一个适用于AMD 和浏览器全局变量的jQuery 插件。

**采用简单的Node/CommonJS 适配器的AMD**
这些对于使用AMD 风格很有用，同时还可以制作可用于Node 的模块，并通过npm 进行安装，无需额外的依赖来设置完整的AMD API。

这种方法不允许使用[AMD 加载器插件](https://github.com/amdjs/amdjs-api/wiki/Loader-Plugins)，只是基本的JS模块依赖关系。它也不支持在AMD 中可用的[回调式require](https://github.com/amdjs/amdjs-api/wiki/require)。

1. [nodeAdapter.js](https://github.com/umdjs/umd/blob/master/templates/nodeAdapter.js) - 最适合使用AMD 风格但希望在没有设置AMD 的辅助库的情况下在Node 中工作。
2. [commonjsAdapter.js](https://github.com/umdjs/umd/blob/master/templates/commonjsAdapter.js) - 与nodeAdapter.js相似，但与更多CommonJS运行时兼容，并且你要定义循环依赖。

## 工具
**构建工具**
1. [docpad-plugin-umd](https://github.com/docpad/docpad-plugin-umd) 是一个[DocPad](http://docpad.org/)插件，用于使用UMD 样板编译包裹JavaScript代码
2. [grunt-umd](https://github.com/alexlawrence/grunt-umd) 是使用UMD样板编译包裹JavaScript代码的[Grunt](http://gruntjs.com/)任务
3. [gulp-umd](https://github.com/eduardolundgren/gulp-umd) 是用UMD 样板编译包裹JavaScript代码的一个[Gulp](http://gulpjs.com/)任务
4. [grunt-urequire](https://github.com/aearly/grunt-urequire) 是用于[uRequire](https://github.com/anodynos/uRequire)通用JavaScript 模块的转换工具的Grunt包装器。
5. [generator-umd](https://github.com/ruyadorno/generator-umd) 是一个Yeoman 生成器，它使用UMD 样板创建单个模块项目

**测试**
1. [用grunt-contrib-jasmine测试UMD](http://stackoverflow.com/questions/16940548/grunt-test-for-umd)

## 资源
1. [Browserify 和UMD](http://dontkry.com/posts/code/browserify-and-the-universal-module-definition.html)

## 待办事项
1. noConflict。虽然使用AMD 加载器和构建工具，但应该可以获得特定于版本的绑定，可能会显示具有noConflict 选项的版本。
2. 将一些功能附加到`$impersonator`的变体。尽管对于这种情况很有诱惑力，但要求'jquery'作为依赖关系，如果开发人员想要使用与实际'jquery'不同的东西，请将该文件映射到'jquery'名称。这是模块名称的优势之一，它们可以映射到不同的实现。
3. 用法示例
  3.1. 进一步的理由使用
  3.2. Gotchas/custom-tweaks我们知道，但宁愿不适用于默认的UMD 样板

## 影响
这个资源库中UMD 变体的基本模式来自用于[Q promise](https://github.com/kriskowal/q)库的方法[@kriskowal](https://github.com/kriskowal)。

早期UMD 的变体也有影响，从Kit-Cambridge的[UMD](https://gist.github.com/1251221)到Addy Osmani，Thomas Davis和Ryan Florence讨论的[模式](https://github.com/addyosmani/jquery-plugin-patterns/issues/1)，以及最近由[James Burke](https://gist.github.com/1262861)提出的UMD模式。

## 执照
版权所有(c)UMD贡献者

根据[MIT许可证](https://github.com/umdjs/umd/blob/master/LICENSE.md)获得许可。

## 相关文档
1. [javascript模块化之CommonJS、AMD、CMD、UMD、ES6](https://blog.csdn.net/Real_Bird/article/details/54869066)
