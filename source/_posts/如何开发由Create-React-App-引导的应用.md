---
title: 如何开发由Create React App 引导的应用
date: 2017-03-30 09:49:39
comments: true
tags:
  - 技术
  - Create React App
  - 翻译
categories: 技术
---

> 此文章是翻译[How to develop apps bootstrapped with Create React App](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md) 官方文档

## Updating to New Release

Creat React App 分成两个包：

* `create-react-app` 是用于创建新项目的全局命令行工具。
* `react-scripts` 是在新建项目中的开发依赖。

几乎不需要更新`create-react-app` 自身：它将所有的设置委托给了`react-scripts` 。

当你运行`create-react-app` 时，它总是使用`react-scripts` 的最新版本来创建项目，以便你自动获得所有的新特性和改进。

要将现有项目更新为`react-scripts` 的最新版本，[打开更新日志](https://github.com/facebookincubator/create-react-app/blob/master/CHANGELOG.md)，找到你当前正在用的版本（可以在`package.json`中找到），按照最新版本的迁移说明来操作。

在大多数情况下，更改`packages.json` 中`react-scripts` 的版本号，然后运行`npm install` 应该就可以了，但是对于潜在的重大变化，请参考[更新日志](https://github.com/facebookincubator/create-react-app/blob/master/CHANGELOG.md)。

我们致力于保持最小的重大变化，从而可以无痛的升级`react-scripts`。

## Sending Feedback

我们总是关注[您的反馈](https://github.com/facebookincubator/create-react-app/issues)。

## Folder Structure

创建完成后，你的项目应该看上去像这样：

```json
my-app/
  README.md
  node_modules/
  package.json
  public/
    index.html
    favicon.ico
  src/
    App.css
    App.js
    App.test.js
    index.css
    index.js
    logo.svg
```

对于要构建的项目，这些文件必须存在：

* `public/index.html` 是页面模版；
* `src/index.js` 是JavaScript 入门点。

你可以删除或重命名其它的文件。

你可以在`src` 中创建子目录。为了更快的重构，只有位于`src` 中的文件会被Webpack 处理。
你需要*将任何JS 和CSS 放到`src` 中*，否则Webpack 将不会处理。

`public/index.html` 只能使用`public` 中的文件。
阅读使用JavaScript 和HTMl 资源的说明。

但是，你可以创建更多的顶级目录。
它们不会被包含在生产版本中，因此你可以将它们用于文档说明等。

## Available Scripts

在项目目录中，你可以运行：

**npm start**

在开发模式下运行应用。
打开 http://localhost:3000 在浏览器中查看。

如果你进行了修改，页面将重新加载。
你还可以在控制台中看到lint error。

**npm test**

在交互观察者模式下，启动test runner。
更多信息请参考running tests 章节。

**npm run build**

构建生产版本应用到build 文件夹。
它在生产模式下正确地绑定了React，并且优化了构建以获得最佳性能。

构建是minified 并且文件名包含哈希值。
你的应用已经准备好部署了！

更多信息请参考deployment 章节。

**npm run eject**

**注意：这是一个单向操作。一旦你`eject`，你不能返回！**

如果你对构建工具和配置选项不满意，你可以运行`eject`。此命令将从项目中删除单个构建依赖。

相反，它会将所有配置文件和转变的依赖（Webpack、Babel、ESLint 等）复制到你的项目中，所以你可以完全控制它们。除了`eject` 外所有的命令仍将其作用，但它们将指向复制的脚本，以便你调整它们。在这一点，完全看你自己。

你不必使用`eject`。The curated feature set 适用于中小型部署。但是我们明白，当你准备使用这个工具时，如果你不能自定义它，则它将没有用。

## Supported Language Features and Polyfills

这个项目支持最新JavaScript 标准的超集。
除了[ES6](https://github.com/lukehoban/es6features) 语法，它还支持：

* [Exponentiation Operator](https://github.com/rwaldron/exponentiation-operator)（ES2016）
* [Async/await](https://github.com/tc39/ecmascript-asyncawait)（ES2017）
* [Object Rest/Spread Properites](https://github.com/sebmarkbage/ecmascript-rest-spread)（stage 3 proposal）
* [Class Field and Static Properties](https://github.com/tc39/proposal-class-public-fields)（stage 3 proposal）
* [JSX](https://facebook.github.io/react/docs/introducing-jsx.html) 和 [Flow](https://flowtype.org/) 语法

学习更多关于[不同提案阶段](https://babeljs.io/docs/plugins/#presets-stage-x-experimental-presets-)。

虽然我们建议你谨慎使用这些实验性提案，但Facebook 在产品代码中大量使用这些功能，因此，如果将来这些提案发生变化，我吗将提供[codemods](https://medium.com/@cpojer/effective-javascript-codemods-5a6686bb46fb)。

注意**这个项目只包括几个ES6[polyfills](https://en.wikipedia.org/wiki/Polyfill)：**

* [Object.assign()](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Object/assign) 通过[object-assign](https://github.com/sindresorhus/object-assign)
* [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) 通过[promise](https://github.com/then/promise)
* [fetch()](https://developer.mozilla.org/en/docs/Web/API/Fetch_API) 通过[whatwg-fetch](https://github.com/github/fetch)

如果你使用任何需要**运行时支持**的ES6+ 特性（像`Array.from()` 或`Symbol`）,确保你手动添加了合适的polyfill，或者你的目标浏览器已经支持它们了。

## Syntax Highlighting in the Editor

要在你喜欢的文本编辑器中配置语法高亮，前往[相关的Babel 文档说明页](https://babeljs.io/docs/editors)，然后按照说明操作。包含大部分流行的编辑器。

## Displaying Lint Output in the Editor

> 注意：这个特性需呀`react-scripts@0.2.0` 或更高。

一些编辑器，包括Sublime Text、Atom 和Visual Studio Code 提供了ESLint 插件。

对于linting 它们不是必须要的。你会在终端以及浏览器控制台中看到linter 输出。但是，如果你喜欢在编辑器中显示lint 结果，则需要执行一些额外的操作。

首先，你需要为你的编辑器安装ESLint 插件。

> Atom 用户使用 `linter-eslint` 笔记
> 如果你正在使用Atom `linter-eslint` 插件，确保 **Use global ESLint installation** 选项已经选中：
> {% asset_img %}
> Visual Studio Code 用户
> VS Code ESLint 插件会自动侦测Create React App 的配置文件。所以你不需要在根目录下创建`eslintrc.json` 文件，除非你想要添加你自己的规则。在这种情况下，你应该包含CRA 的配置，通过添加下面这行：
>
```js
{
 // ...
 "extends": "react-app"
}
```

然后将下面这块代码添加到你的项目中的`package.json` 文件中
```js
{
 //...
 "eslintConfig": {
    "extends": "react-app"
  }
}
```

最后，你需要全局安装一些包：
```bash
npm install -g eslint-config-react-app@0.3.0 eslint@3.8.1 babel-eslint@7.0.0 eslint-plugin-react@6.4.1 eslint-plugin-import@2.0.1 eslint-plugin-jsx-a11y@4.0.0 eslint-plugin-flowtype@2.21.0
```

我们认识到这是次优的，由于我们隐藏ESLint 依赖的方式，它目前是必须的。ESLint 团队已经在为此[提供解决方案](https://github.com/eslint/eslint/issues/3458)，因此在几个月内可以会变得不必要。

## Debugging in the Editor

**这个特性目前只有[Visual Studio Code](https://code.visualstudio.com/)编辑器支持。**

Visual Studio Code 支持实时编辑和调试，使用Create React App 开箱即用。这能够使你作为一个开发者不需要离开编辑器就可以进行编写和调试你的React 代码，最重要的是它可以让您拥有持续的开发工作流程，其中上下文切换是最小的，因为你不必在不同工具之间进行切换。

你需要确保[VS Code](https://code.visualstudio.com/) 是最新版本，并且VS Code 的[Chrome Debugger Extension](https://marketplace.visualstudio.com/items?itemName=msjsdiag.debugger-for-chrome) 已经安装了。

然后将下面代码添加到你的`launch.json` 文件，并将其放到你的应用根目录下的`.vscode` 文件夹中。

```json
{
  "version": "0.2.0",
  "configurations": [{
    "name": "Chrome",
    "type": "chrome",
    "request": "launch",
    "url": "http://localhost:3000",
    "webRoot": "${workspaceRoot}/src",
    "userDataDir": "${workspaceRoot}/.vscode/chrome",
    "sourceMapPathOverrides": {
      "webpack:///src/*": "${webRoot}/*"
    }
  }]
}
```
运行`npm start` 启动你的应用，按`F5` 或点击绿色debug 图标在VS Code 中调试。现在你可以写代码、设置断点、修改代码以及调试你最新修改的代码--所有都在你的编辑器中实现。

 
