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

<!--more-->

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
> {% asset_img Use-global-ESLint-installation.png %}
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

## Change the Page **<title>**

你可以在生成项目的`public` 文件夹下知道源HTML 文件。你可以编辑`<title>` 标签，把“React App” 标题改为其他任何东西。

请注意，通常你不会经常在`public` 目录下编辑文件。例如，可以在不更改HTML 情况下，[添加CSS 文件](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md#adding-a-stylesheet)。

如果你需要根据内容来动态更新页面的标题，你可以使用浏览器的[document.title]() API。对于要从React 组件更改标题的更复杂的场景，可以使用[React Helmet](https://github.com/nfl/react-helmet) 这个第三方库。

在生产环境中，你为自己的应用使用自定义服务器，要将标题在发送到浏览器之前修改，你可以遵循[本章](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md#generating-dynamic-meta-tags-on-the-server) 建议。或者，你可以预构建每一个页面为静态HTML 文件，然后加载JavaScript 包，将在[这里](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md#pre-rendering-into-static-html-files)介绍。


## Installing a Dependency

生成的项目中包括React 和ReactDOM 依赖。它也包括Create React App 使用的一组脚本作为开发依赖。你也可以使用`npm`安装其它的依赖（例如，React Router）：
```bash
npm install --save <library-name>
```

## Importing a Component

此项目设置支持ES5 模块，多亏了Babel。
虽然你仍然可以使用`require()` 和`module.exports`，我们建议你改用[`import` and `export`](http://exploringjs.com/es6/ch_modules.html)。

例如：

**Button.js**
```js
import React, { Component } from 'react';

class Button extends Component {
  render(){
    //...
  }
}

export default Button; // Don't forget to use export default!
```
**DangerButton.js**
```js
import React, { Component } from 'react';
import Button from './Button'; // Import a component from another file

class DangerButton extends Component {
  render(){
    return <Button color="red" />;
  }
}

export default class DangerButton;
```

注意[默认导出和命名导出的区别](http://stackoverflow.com/questions/36795819/react-native-es-6-when-should-i-use-curly-braces-for-import/36796281#36796281)。这是常见的错误来源。

我们建议你在模块仅导出单个（例如，一个组件）时，坚持使用默认导入和导出。当你使用`export default Button` 和`import Button from './Button'` 时，就会得到你想要的。

命名导出对导出多个功能的实用模块很有用。一个模块最多可能有一个默认的导出和任意数量的命名导出。

了解有关ES 模块的更多内容：

* [When to use the curly braces?](http://stackoverflow.com/questions/36795819/react-native-es-6-when-should-i-use-curly-braces-for-import/36796281#36796281)
* [Exploring ES6: Modules](http://exploringjs.com/es6/ch_modules.html)
* [Understanding ES6: Modules](https://leanpub.com/understandinges6/read#leanpub-auto-encapsulating-code-with-modules)

## Adding a Stylesheet

此项目设置使用Webpack 管理所有资源。Webpack 提供了一种定制方式，将导入概念扩展“extending”到JavaScript 之外。为了表示一个JavaScript 文件依赖一个CSS 文件，你需要将CSS 导入JavaScript 文件。

**Button.css**
```css
.Button {
  padding: 20px;
}
```

**Button.js**
```js
import React, { Component } from 'react';
import './Button.css'; // Tell Webpack that Button.js uses these styles

class Button extends Component {
  render(){
    // You can use them as regular CSS styles
    return <div className="Button" />
  }
}
```

**对于React 这并不是必需的**，但是许多人发现这个特性很方便。你可以在[这里]阅读这种方法的好处。但是，您应该意识到，这使得你的代码更适合Webpack而不是他构建工具和环境。

在开发中，使用这种方式表达依赖，可以在编辑样式使立即重新加载样式。在生产中，所有的CSS 文件将被连接到构建输出中的一个最小化的.css 文件中。

如果你关系使用Webpack-specific 语法，你可以将你所有的CSS 放到`src/index.css`。它将从`src/index.js` 导入，但是如你之后迁移到一个不同的构建工具，你可以移除这个导入。

## Post-Processing CSS

此项目设置可以减少你的CSS ，并通过[Autoprefixer]() 自动添加厂商前缀，因此你不需要担心。

例如，它：
```css
.App {
  display: flex;
  flex-direction: row;
  align-items: center;
}
```
变成这样：
```css
.App {
  display: -webkit-box;
  display: -ms-flexbox;
  display: flex;
  -webkit-box-orient: horizontal;
  -webkit-box-direction: normal;
      -ms-flex-direction: row;
          flex-direction: row;
  -webkit-box-align: center;
      -ms-flex-align: center;
          align-items: center;
}
```

如果你因为某些原因需要禁用自动添加前缀功能，[参考这里](https://github.com/postcss/autoprefixer#disabling)。

## Adding a CSS Preprocessor (Sass, Less etc.)

通常，我们建议你不要在不同的组件直接重复使用相同的CSS 类。例如，我们建议你创建一个具有自己的`.Button` 样式的`<Button>` 组件，而不是在`<AcceptButton>` 和`<RejectButton>`中使用`.Button` CSS 类，这样`<AcceptButton>` 和`<RejectButton>` 都能渲染（而[不是继承]()）。

遵循这条规则往往使CSS 预处理器不太有用，因为像混合和嵌套之类的功能被组件组合所替代。但是，如果你发现它有价值，你可以集成CSS 预处理器。在这个指导中，我们将使用Sass，但是你也可以使用Less 或其他方式。

首先，安装Sass 命令行接口：
```bash
npm install node-sass --save-dev
```

然后在`package.json` 中添加如下内容到`scripts`：
```js
  "scripts": {
    "build-css": "node-sass src/ -o src/",
    "watch-css": "npm run build-css && node-sass src/ -o src/ --watch --recursive",
    ...
  }
```

> 注意：使用不同的预处理器，需要根据预处理器的文档替换`build-css` 和`watch-css` 命令。

现在，你可以将`src/App.css` 重命名为`src/App.scss`，然后运行`npm run watch-css`。watcher 将会查找`src` 子目录下的所有Sass 文件，并且在它旁边创建相应的CSS 文件，我们这里例子会重写`src/App.css`。由于`src/App.js` 仍然导入`src/App.css`，这个样式成为你的应用的一部分。现在你可以编写`src/App.scss`，`src/App.css` 将会被重新生成。

在Sass 文件中共享变量，你可以使用Sass 导入功能。例如，`src/App.scss` 和其他组件样式文件可以包括`@import "./shared.scss"`中的变量定义。

此时你可能想移除源文件控制中所有的CSS 文件，添加`src/**/*.css` 到你的`.gitignore` 文件中。 保留构建产品在源代码控制之外是一个很好的做法。

最后，你可能发现在运行`npm start` 时自动运行`watch-css`，并将`build-css` 作为`npm run build` 的一部分是非常方便的。你可以使用`&&` 操作符去顺序地执行这两个脚本。但是，没有跨平台地并行运行两个脚本，所以我们需要为此安装一个包：
```bash
npm install --save-dev npm-run-all
```

然后我们在`start` 和`build` 两个脚本包含CSS 预处理命令：
```js
 "scripts": {
   "start-js": "react-script start",
   "start": "npm-run-all -p watch-css start.js",
   "build": "npm run build-css && react-scripts build"
 }
```

现在运行`npm start` 和`npm run build` 也会构建Sass 文件。注意`node-sass` 似乎有一个[issue recognizing newely created files on some systems](https://github.com/sass/node-sass/issues/1891)，所以你可能需要重启watcher，当你新建一个文件，直到这个问题被解决。

## Adding Images, Fonts, and Files

使用Webpack，使用静态资源像图像和字体的方式同CSS 类似

你可以**在JavaScript 文件中直接`import`文件**。这告诉Webpack 在包中包括该文件。不像CSS 导入，导入一个文件给你一个字符串值。这个值是你在代码中可以引用的最终路径，例如：作为一个图片的`src` 属性或一个链接到PDF 的`href`。

为了减少请求服务器的次数，导入的图像如果小于10,000 byte 会返回一个[data URI](https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/Data_URIs) 而不是一个路径。这适用于以下文件扩展名：bmp、gif、jpg、jpeg 和png。由于[#1153](https://github.com/facebookincubator/create-react-app/issues/1153) SVG 文件除外。


这有一个例子：
```js
import React from 'react';
import logo from './logo.png'; // Tell Webpack this JS file uses this image

console.log(logo); // logo.84288709.png

function Header(){
  // Import result is the URL of your image
  return <img src={logo} alt="logo" />;
}

export default Header;
```

这确保当项目构建后，Webpack 将正确的移动图像到build 文件夹，并且提供会一个正确的路径。

在CSS 中也是一样的：
```css
.Logo {
  background-image: url(./logo.png)
}
```

Webpack 在CSS 中查找所有相对的模块引用（它们以`./` 开始）然后使用已编译包中的最终路径来替换它们。如果你输入错误或意外删除一个重要文件，则会出现编译错误，就像当你引入一个不存在的JavaScript 模块那样。编译包中最终文件名由Webpack 根据内容哈希生成。如果将来文件内容发生变化，Webpack 将在生产中给出不同的名称，因此你不必担心资源的长期缓存。

请注意，这也是Webpack 自定义功能。

React 并不需要它，但是很多人喜欢它（React Native 使用类似的图片机制）。
下一节将会介绍处理静态资源的另一种方法。

## Using the **public** Folder

> 注意：这个特性需要`react-scripts@0.5.0` 版本以上。

### Changing the HTML

`public` 文件夹包括HTML 文件，所以你可以调整它，例如，[设置页面标题](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md#changing-the-page-title)。在构建过程中，编译好的代码的`<script>` 标签会被自动添加。

### Adding Assets Outside of Module System

你可以在`public` 文件夹中添加其他资源。

注意我们通常鼓励你在JavaScript 中`import` 资源。例如，查看[添加样式](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md#adding-a-stylesheet) 和[添加图片和字体](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md#adding-images-and-fonts) 章节内容。这个机制有许多好处：

* 脚本和样式表被压缩并打包在一起，以避免额外的网络请求。
* 缺少文件导致编译错误，而不是用户的404 错误
* 结果文件名包括内容哈希，因此你不必担心浏览器会缓存旧版本。

但是，你可以使用一个在模块系统之外添加资源的**逃生门**。

如果你将文件放到`public` 文件夹，它将不会被Webpack 处理。并且它将不被修改的复制到构建文件夹。为了引用`public` 文件夹中的资源，你需要使用`PUBLIC_URL` 这个变量。

在`index.html` 中，你可以这样使用：
```js
<link ref="shortcut icon" href="%PUBLIC_URL%/favicon.ico">
```

只有位于`public` 文件夹中的文件可以通过`%PUBLIC_URL%` 前缀访问。如果你需要使用来自`src` 或`node_modules` 中的文件，你必须将其复制到明确指定此文件作为构建的一部分的意图。

当你运行`npm run build` ，Create React App 将使用正确的绝对路径来替换`%PUBLIC_URL%`，这样即使你使用客户端路径或将其托管到非艮URL，你的项目也可以正常工作。

在JavScript 代码中，你可以使用`process.env.PUBLIC_URL` 进行类似的操作：
```js
render() {
  // Note: this is an escape hatch and should be used sparingly!
  // Normally we recommend using `import` for getting asset URLs
  // as described in “Adding Images and Fonts” above this section.
  return <img src={process.env.PUBLIC_URL + '/img/logo.png'} />;
}
```

记住这种方式的缺点：

* `public` 文件夹中中的文件都不能进行后处理或压缩。
* 在编译时，缺少的文件不会被调用，会对用户产生404 错误。
* 结果文件名中不会包含内容哈希，因此在每次改动时，你需要添加查询参数或重命名它们。

### When to Use the **public** Folder

通常我们建议从JavaScript导入[样式表](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md#adding-a-stylesheet)、[图像和字体](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md#adding-images-and-fonts)。 `public` 文件夹作为一些不太常见的情况的解决方法是有用的：

* 你需要在构建输出中具有特定名称的文件，如[`manifest.webmanifest`](https://developer.mozilla.org/en-US/docs/Web/Manifest)。
* 你有成千上万的图像，需要动态引用他们的路径。
* 你希望在打包代码之外加入一个像[`pace.js`](http://github.hubspot.com/pace/docs/welcome/)一样的脚本。
* 某些库可能与Webpack不兼容，你没有其他选项，除了将其作为`<script>`标签。

请注意，如果添加声明全局变量的`<script>`，则还需要阅读下一节使用它们。

## Using Global Variables

当你在HTML文件中的脚本中包含一个全局声明的变量，并尝试在代码中使用这些变量时，linter会抱怨，因为它看不到这些变量的定义。

你可以明确地从`window` 对象中读取全局变量来避免这个问题，例如：
```js
const $ = window.$;
```  

你正在有意使用一个全局变量，而不是因为书写错误。
或者，你可以通过添加`// eslint-disable-line` 强制linter 忽略任意一行。

## Adding Bootstrap

你不必与React一起使用[React Bootstrap]()，但它是将Bootstrap与React应用程序集成的流行库。 如果需要，可以通过以下步骤将其与Create React App集成：

从npm安装React Bootstrap和Bootstrap。 React Bootstrap不包括Bootstrap CSS，因此还需要安装它们：
```bash
npm install react-bootstrap --save
npm install bootstrap@3 --save
```

在`src/index.js` 开头导入Bootstrap CSS 和可选的Bootstrap theme CSS：
```js
import 'bootstrap/dist/css/bootstrap.css';
import 'bootstrap/dist/css/bootstrap-theme.css';
// Put any other imports below so that CSS from your
// components takes precedence over default styles.
```

在`src/App.js` 中导入需要的React Bootstrap 组件或者你自定义的组件：
```js
import { Navbar, Jumbotron, Button } from 'react-bootstrap';
```

现在你可以在这些导入了React Bootstrap 组件的组件的渲染方法中使用。这有一个使用React Bootstrap 的[例子App.js](https://gist.githubusercontent.com/gaearon/85d8c067f6af1e56277c82d19fd4da7b/raw/6158dd991b67284e9fc8d70b9d973efe87659d72/App.js)。

### Using a Custom Theme

有时你可能需要调整Bootstrap（或其他类似包）的视觉样式。
我们建议按照下面方法：

* 创建一个依赖于你要自定义的包的新包，例如 Bootstrap。
* 添加必要的构建步骤来调整主题，并在npm发布你的包。
* 安装你自己的主题npm软件包作为你的应用程序的依赖。

这是按照这些步骤添加一个[自定义的Bootstrap](https://medium.com/@tacomanator/customizing-create-react-app-aa9ffb88165)的示例。

## Adding Flow

Flow是一种静态类型的检查器，可以帮助你编写更少错误的代码。 如果你是这个概念的新手，请参阅[JavaScript中使用静态类型的介绍](https://medium.com/@preethikasireddy/why-use-static-types-in-javascript-part-1-8382da1e0adb)。

[Flow](http://flowtype.org/)的最新版本在Create React App项目中是开箱即用。

要将Flow添加到Create React App项目，请按照下列步骤操作：

1. 运行`npm install --save-dev flow-bin`（或`yarn add --dev flow-bin`）。
2. 将`"flow": "flow"`添加到你的的`package.json`的`scripts`部分。
3. 运行`npm run flow-init`（或`yarn flow -- init`）在根目录中创建一个[`.flowconfig`文件](https://flowtype.org/docs/advanced-configuration.html)。
4. 将`// @flow`添加到你想要进行类型检查的文件中（例如，到`src/App.js`）。

现在，你可以运行`npm run flow`（或`yarn flow`）来检查文件的类型错误。 你可以选择使用像[Nuclide](https://nuclide.io/docs/languages/flow/)这样的IDE来获得更好的集成体验。 未来我们计划将更加紧密地将其整合到Create React App中。

要了解有关Flow的更多信息，请查看[其文档](https://flowtype.org/)。

## Adding Custom Environment Variables

> 注意：这个特性需要`react-scripts@0.2.3` 版本以上。

你的项目可以在你的环境中使用声明的变量，就像它们在JS文件中本地声明一样。 默认情况下，您将为你定义的`NODE_ENV`，和以`REACT_APP_`开头的任何其他环境变量。

**环境变量在构建时嵌入**。 由于Create React App生成了一个静态的HTML/CSS/JS包，所以在运行时无法读取它们。 要在运行时读取它们，你需要将HTML加载到服务器的内存中，并在运行时替换占位符，就像[这里所述]()。 或者，你可以在任何时候更改，在服务器端时重新构建应用程序。

> 注意：你必须创建以`REACT_APP_`开头的自定义环境变量。除了`NODE_ENV`之外的任何其他变量将被忽略，以避免意外[暴露可能具有相同名称的机器上的私钥](https://github.com/facebookincubator/create-react-app/issues/865#issuecomment-252199527)。 如果运行时，更改任何环境变量将需要重新启动开发服务器。

这些环境变量将会被定义在`process.env` 上。你可以通过`process.env.NODE_ENV` 读取。当你运行`npm start`，它总是等于`'development'`，当你运行`npm test`时，它总是等于`'test'`，当你运行`npm run build` 是去生成一个生产包，它总是等于`'production'`。**你不能手动覆盖`NODE_ENV`**。这样可以防止开发人员将缓慢的开发构建部署到生产环境中。

这些环境变量对于有条件地显示信息有用，基于项目的部署位置或者消耗存在与版本控制之外的敏感数据。

首先，你需要定义环境变量。 例如，假设你想要使用`<form>`中的环境中定义的敏感数据：
```js
render() {
  return (
    <div>
      <small>You are running this application in <b>{process.env.NODE_ENV}</b> mode.</small>
      <form>
        <input type="hidden" defaultValue={process.env.REACT_APP_SECRET_CODE} />
      </form>
    </div>
  );
}
```

在构建中，`process.env.REACT_APP_SECRET_CODE` 将会被当前`REACT_APP_SECRET_CODE` 环境变量中的值替换。记住`NODE_ENV` 变量将会为你自动设置。

当你在浏览器中加载这个应用去查看`<input>`，你可以看到它的值设置为`abcdef`，加粗的字体将会显示运行`npm start` 时的环境。
```js
<div>
  <small>You are running this application in <b>development</b> mode.</small>
  <form>
    <input type="hidden" value="abcdef" />
  </form>
</div>
```

上面的表单正在从环境中寻找一个名为`REACT_APP_SECRET_CODE`的变量。 为了消耗这个值，我们需要在环境中定义它。 这可以通过两种方式完成：在shell或`.env`文件中。 这两种方法将在接下来的几节中进行描述。

访问`NODE_ENV`对于有条件地执行操作是有用的：
```js
if (process.env.NODE_ENV !== 'production') {
  analytics.disable();
}
```

当你使用`npm run build` 编译这个应用时，压缩操作在这个条件之外，所以这个最终的包会更小。

### Referencing Environment Variables in the HTML

> 注意：这个特性需要`react-scripts@0.9.0` 版本以上。

你也可以在`public/index.html` 中访问以`REACT_APP_` 开头的环境变量。例如：
```js
<title>%REACT_APP_WEBSITE_NAME%</title>
```

请注意，上述部分的注意事项适用：

* 除了几个内容的变量（`NODE_ENV` 和`PUBLIC_URL`），变量名字必须以`REACT_APP_` 才工作。
* 环境变量在构建时被注入。如果你需要在运行时注入它们，[需要按照这里的步骤](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md#generating-dynamic-meta-tags-on-the-server)。

### Adding Temporary Environment Variables In Your Shell

定义环境变量可能会因操作系统而异。知道这种方式对于shell会话的生命周期是暂时的也很重要。

**Windows(cmd.exe)**
```bash
set REACT_APP_SECRET_CODE=abcdef&&npm start
```
（注意：空格的缺失是有意的）

**Linux, macOS(Bash)**
```bash
REACT_APP_SECRET_CODE=abcdef npm start
```

### Adding Developement Environment Variables In `.env`

> 注意：这个特性需要`react-scripts@0.5.0` 版本以上。

在你的项目的根目录中创建一个`.env` 文件，去定义永久性的环境变量

```js
REACT_APP_SECRET_CODE=abcdef
```

如果机器没有明确设置它们，这些变量将作为默认值。
有关详细信息，请参阅[dotenv文档](https://github.com/motdotla/dotenv)。

> 注意：如果要定义用于开发的环境变量，则您的CI和/或托管平台也有可能需要这些定义。请参考他们的文档如何做到这一点。例如，请参阅[Travis CI](https://docs.travis-ci.com/user/environment-variables/)或[Heroku](https://devcenter.heroku.com/articles/config-vars)的文档。

## Can I Use Decorators?

许多受欢迎的库在其文档中使用装饰器。
目前，Create React App不支持装饰器语法，因为：

* 这是一个实验性提案，可能会改变。
* 目前的规范版本没有被Babel正式支持。
* 如果规范发生变化，我们将无法编写一个codemod，因为我们不在Facebook内部使用它们。

但是，在许多情况下，您可以重新编写基于装饰器的代码，而不需要装饰器就可以了。
请参考这两个线程以供参考：

* [#214](https://github.com/facebookincubator/create-react-app/issues/214)
* [@411](https://github.com/facebookincubator/create-react-app/issues/411)

当规范进展到稳定的阶段时，Create React App将添加装饰器支持。

## Integrating with an API Backend

这些教程将帮助您将应用程序与在另一个端口上运行的API后端集成，使用`fetch()`来访问它。

### Node

看看[这个教程](https://www.fullstackreact.com/articles/using-create-react-app-with-a-server/)。 您可以在这里找到配套的[GitHub存储库](https://github.com/fullstackreact/food-lookup-demo)。

### Ruby on Rails

看看[这个教程](https://www.fullstackreact.com/articles/how-to-get-create-react-app-to-work-with-your-rails-api/)。 您可以在这里找到配套的[GitHub存储库](https://github.com/fullstackreact/food-lookup-demo-rails)。


## Proxying API Requests in Development

> 注意：这个特性需要`react-scripts@0.2.3` 版本以上。

人们通过提供与前端React 应用相同的主机和端口作为后端实现。
例如，应用部署后，生产设置可能看上去像这样：

```js
/             - static server returns index.html with React app
/todos        - static server returns index.html with React app
/api/todos    - server handles any `/api/*` requests using the backend implementation
```

像这样的设置不是必须的。但是，如果你已经有了一个这样的设置，很方便写`fetch('api/todos')` 这样的请求而不必担心在开发过程中将它们重定向到另一个主机或端口。

在开发中，让开发服务器代理任何未知API 服务请求，添加一个`proxy` 域到你的`package.json`，例如：
```js
 "proxy": "http://localhost:4000"
```

这样，当您在开发中`fetch('/api/todos')`时，开发服务器将会认识到它不是一个静态资产，并会将您的请求代理到`http://localhost:4000/api/todos`作为后备。 开发服务器将仅尝试发送没有`text/html` accept header 的请求到代理。

方便的，这样可以避免在开发过程中发生[CORS问题]()和像下面的错误消息：
```html
Fetch API cannot load http://localhost:4000/api/todos. No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin 'http://localhost:3000' is therefore not allowed access. If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.
```

请记住，`proxy`仅在开发中有效（使用`npm start`），由你确保像`/api/todos` 这样的URL指向正确的生产环境。 你不必使用`/api` 前缀。 任何无法识别没有`text/html` accept header 的请求，将被重定向到指定的`proxy`。

`proxy` 选项支持HTTP，HTTPS和WebSocket连接。
如果`proxy` 选项对你不够灵活，或者你可以：

* 在您的服务器上启用CORS（[这是在Express 中的操作方法](http://enable-cors.org/server_expressjs.html)）。
* 使用[环境变量](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md#adding-custom-environment-variables)将正确的服务器主机和端口注入到应用程序中。

## Using HTTPS in Development

> 注意：这个特性需要`react-scripts@0.4.0` 版本以上。

您可能需要开发服务器通过HTTPS提供页面。 一个特别的情况可能是有用的，当API服务器本身服务本身用于HTTPS时，使用[the "proxy" feature](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md#proxying-api-requests-in-development)来代理对API服务器的请求。

为此，请将`HTTPS` 环境变量设置为`true`，然后像以往那样以`npm start`启动开发服务器：

**Windows(cmd.exe)**
```bash
set HTTPS=true&&npm start
```
（注意：空格的缺失是有意的）

**Linux, macOS(Bash)**
```bash
HTTPS=true npm start
```

请注意，服务器将使用自签名证书，因此你的Web浏览器几乎肯定会在访问页面时显示警告。

## Generating Dynamic `<meta>` Tags on the Server

由于Create React App 不支持服务器渲染，你可能想知道如何使`<meta>`标签动态化并反映当前URL。 为了解决这个问题，我们建议在HTML中添加占位符，如下所示：

```
<!doctype html>
<html lang="en">
  <head>
    <meta property="og:title" content="__OG_TITLE__">
    <meta property="og:description" content="__OG_DESCRIPTION__">`
```

然后，在服务器上，不管你使用的后端，你都可以将`index.html`读入内存，并根据当前URL替换`__OG_TITLE__`、`__OG_DESCRIPTION__`以及任何其他具有值的占位符。 只需确保清理和转义内插的值，以便它们可以安全地嵌入到HTML中！

如果使用Node服务器，你甚至可以在客户端和服务器之间共享路由匹配逻辑。 但是在简单的情况下，复制它也可以正常工作。

## Pre-Rendering into Static HTML Files

如果您使用静态主机提供商托管您的构建程序，则可以使用反应快照为应用程序中的每个路由或相对链接生成HTML页面。 然后，这些页面将在JavaScript软件包加载时无缝地变为活动状态或“水合”。

还有机会在静态托管之外使用它，在生成和缓存路由时将压力从服务器上取下。

预渲染的主要优点是，你可以使用HTML有效内容获取每个页面的核心内容，而不管你的JavaScript软件包是否成功下载。 这也增加了你的应用程序的每个路由将被搜索引擎抓取的可能性。

你可以阅读[零配置预渲染（也成为snapshotting）](https://medium.com/superhighfives/an-almost-static-stack-6df0a2791319)。

## Injecting Data from the Server into the Page

同上一节相似，你可以在HTML 中留一些插入全局变量的占位符，例如：
```
<!document html>
<html lang="en">
  <head>
    <script>
      window.SERVER_DATA = __SERVER_DATA__
    </script>
```
然后，在服务器上，您可以在发送响应之前将`__SERVER_DATA__`替换为真实的JSON数据。 客户端代码可以读取`window.SERVER_DATA`来使用它。 **确保在[清理JSON之后再发送到客户端](https://medium.com/node-security/the-most-common-xss-vulnerability-in-react-js-applications-2bdffbcc1fa0)，因为它使你的应用程序易受XSS攻击。**

## Running Test

> 注意：这个特性需要`react-scripts@0.3.0` 版本以上。
> [阅读迁移指导如果在旧项目中使用](https://github.com/facebookincubator/create-react-app/blob/master/CHANGELOG.md#migrating-from-023-to-030)

Create React App 使用Jest作为其测试运行器。 为了做好这个整合的准备，我们为Jest做了一个重大变革，所以如果你听到很多年前的坏事，请再试一次。

Jest是一个基于Node的运行器。 这意味着测试总是在Node环境中运行，而不是在真实的浏览器中运行。 这使我们能够实现加快迭代速度并防止怪异的行为。

虽然Jest提供像`window`的浏览器全局变量，这多亏了[jsdom](https://github.com/tmpvar/jsdom)，但它们只是同真正的浏览器行为相似。 Jest旨在用于你的逻辑和组件的单元测试，而不是DOM怪异。

如果需要，我们建议你使用单独的浏览器端到端的测试工具。 它们超出了Create React App的范围。

### Filename Coventions

Jest将使用以下任何常见的命名规则来查找测试文件：

* `__tests__`文件夹中带有`.js`后缀的文件。
* 带有`.test.js`后缀的文件。
* 带有`.spec.js`后缀的文件。

`.test.js` 或`.spec.js` 文件（或`__tests__`文件夹）可以位于`src`顶级文件夹下的任意深度。

我们建议将测试文件（或`__tests__`文件夹）放在正在测试的代码旁边，以使相对导入路径更短。 例如，如果`App.test.js`和`App.js`在同一个文件夹中，测试只需要从`import App from './App'`，而不是长的相对路径。 Colocation还有助于在更大的项目中更快地找到测试。

### Command Line Interface

当你运行`npm test`时，Jest将以观察者模式启动。 每次保存文件时，都会重新运行测试，就像`npm start`重新编译代码一样。

观察者包括交互式命令行界面，具有运行所有测试的能力，或专注于搜索模式。 它是这样设计的，以便你可以保持它开启并享受快速重新运行。 你可以从“Watch Usage” 笔记中了解每次运行后观察者打印的命令：

{% asset_img Command-Line-Interface.gif %}

### Version Control Integration

默认情况下，当您运行`npm test`时，Jest将仅运行与上次提交后更改的文件相关的测试。 这是一个优化，旨在使你的测试运行快速，无论您有多少测试。 但是，它假定你不经常提交不通过测试的代码。

Jest将始终明确提到，它只运行与上次提交后更改的文件相关的测试。 你也可以在观察者模式按`a` 强制Jest运行所有测试。

Jest将始终在[持续集成](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md#continuous-integration)服务器上运行所有测试，或者该项目不在Git或Mercurial资源库中。

### Writing Tests

要创建测试，请使用测试名称及其代码添加在`it()`（或`test()`）块。 你可以选择将它们包装在`describe()`块中进行逻辑分组，但这不是必需的，也不是推荐的。

Jest提供了一个内置的`expec()`全局函数来进行断言。 基本测试可能如下所示：
```js
import sum from './sum';

it('sums numbers', () => {
  expect(sum(1, 2)).toEqual(3);
  expect(sum(2, 2)).toEqual(4);
});
```
Jest支持的所有`expect()` 匹配器都在[这里进行了广泛的记录](http://facebook.github.io/jest/docs/expect.html)。
你也可以使用[`jest.fn()`和`expect(fn).toBeCalled()`]创建“spies”或模拟函数。

### Testing Components

有广泛的组件测试技术。 它们的范围从“冒烟测试（smoke test）”来验证组件渲染器没有错误抛出，到浅渲染来测试某些输出，到完全渲染来测试组件生命周期和状态更改。

不同的项目根据组件变化的频率及其包含的逻辑选择不同的测试权衡。 如果你尚未决定测试策略，我们建议你首先为组件创建简单的冒烟测试：
```js
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';

it('renders without crashing', () => {
  const div = document.createElement('div');
  ReactDOM.render(<App />, div)
});
```

该测试加载一个组件，并确保它在渲染过程中没有抛出错误。 这样的测试用很少的影响提供了很多价值，所以他们是伟大的起点，这个测试你可以在`src/App.test.js`中找到。

当你遇到由更改组件导致的错误时，你将深入了解其中哪些部分在你的应用程序中值得测试。 这可能是引入更具体的测试来判断具体的预期输出或行为的好时机。

如果你想要脱离他们渲染的子组件来测试组件，我们建议使用Enzyme中的`shallow() rendering API`。 你也可以写冒烟测试：
```bash
npm install --save-dev enzyme react-addons-test-utils
```
```js
import React from 'react';
import { shallow } from 'enzyme';
import App from './App';

it('renders without crashing', () => {
  shallow(<App />);
});
```

与以前使用`ReactDOM.render()` 的冒烟测试不同，此测试仅渲染`<App>`，而不会更深入。 例如，即使`<App>`本身渲染的`<Button>` 抛出了错误，此测试也将通过。 浅渲染非常适合独立的单元测试，但你仍然可能需要创建一些完整的渲染测试，以确保组件正确集成。 Enzyme支持[使用`mount()` 全渲染](http://airbnb.io/enzyme/docs/api/mount.html)，还可以使用它来测试状态更改和组件生命周期。

你可以阅读[Enzyme文档](http://airbnb.io/enzyme/)了解更多测试技术。 Enzyme文档使用Chai和Sinon作为断言，但你不必使用它们，因为Jest为spies提供了内置的`expect()` 和`jest.fn()`。

以下是Enzyme文档中的一个例子，该文档声明了特定输出，使用Jest匹配器重写：
```js
import React from 'react';
import { shallow } from 'enzyme';
import App from './App';

it('renders welcome message', () => {
  const wrapper = shallow(<App />);
  const welcome = <h2>Welcome to React</h2>
  // expect(wrapper.contains(welcome)).to.equal(true)
  expect(wrapper.contains(welcome)).toEqual(true)
});
```

所有Jest匹配器[在这里被广泛记录](http://facebook.github.io/jest/docs/expect.html)。
不过，如下所述，你可以使用像[Chai](http://chaijs.com/)这样的第三方断言库。

此外，你可能会发现[jest-enzyme](https://github.com/blainekasten/enzyme-matchers)有助于使用可读的匹配器简化你的测试。 以上`contains`代码用jest-enzyme更简单。
```js
expect(wrapper).toContainReact(welcome)
```

要使用Create React App设置jest-enzyme，请按照[初始化测试环境](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md#initializing-test-environment)的说明导入`jest-enzyme`。
```bash
npm install --save-dev jest-enzyme
```
```js
// src/setupTests.js
import 'jest-enzyme';
```

### Using Third Party Assertino Libraries

我们建议你用`expect()` 的断言和`jest.fn()`作为spies。 如果你遇到问题，[请提交给Jest](https://github.com/facebook/jest/issues/new)，我们会解决这些问题。 我们打算继续使他们更好的React，例如，[美丽打印React元素作为JSX](https://github.com/facebook/jest/pull/1566)。

但是，如果你习惯于其他库，例如[Chai](http://chaijs.com/)和[Sinon](http://sinonjs.org/)，或者如果你现有的代码使用了你想要移植的代码，那么你可以正常导入它们：
```js
import sinon from 'sinon';
import { expect } from 'chat';
```

然后在你的测试中像你通常那样使用它们。

### Initializing Test Environment

> 注意：这个特性需要`react-scripts@0.4.0` 版本以上。

在测试中，如果你的应用程序需要模拟浏览器API，或者在运行测试之前需要全局设置，请将`src/setupTests.js`添加到你的项目中。 它将在运行测试之前自动执行。

例如：
```js
src/setupTests.js
```
```js
const localStorageMoke = {
  getItem: jest.fn(),
  setItem: jest.fn(),
  clear: jest.fn()
};
global.localStorage = localStorageMoke
```

### Focusing and Excluding Tests

你可以用`xit()` 替换`it()` 以临时排除将要执行的测试。
同样，`fit()` 可以让你专注于特定的测试，而无需运行任何其他测试。

### Coverage Reporting

Jest有一个集成的覆盖报告，与ES6工作良好，不需要配置。
运行`npm test -- --coverage`（注意在中间的额外的`--` ）包括覆盖报告如下所示：
{% asset_img Coverage-Reporting.png %}

请注意，测试的运行速度要慢得多，因此建议你从正常的工作流程中分离运行它。

### Continuous Integration

默认情况下，`npm test`使用交互式CLI运行观察器。 但是，您可以强制运行测试一次，并通过设置一个名为`CI`的环境变量来完成该过程。

当使用`npm run build` 创建应用程序的构建时，默认情况下不会检查linter警告。 像`npm test` 一样，你可以通过设置环境变量`CI`来强制构建执行linter警告检查。 如果遇到任何警告，则构建失败。

流行的CI服务器默认已经设置了环境变量CI，但你也可以自己做这个：

### On CI servers

**Travis CI**
1. 按照[Travis Getting started](https://docs.travis-ci.com/user/getting-started/)指南，将你的GitHub 仓库与Travis 同步。你可以需要在个人[资料](https://travis-ci.org/profile)页面手动初始化某些设置。
2. 在你的git 仓库中添加`.travis.yml` 文件。
```js
language: node_js
node_js:
  - 4
  - 6
cache:
  directories:
    - node_modules
scripts:
  - npm test
  - npm run build
```
1. 通过git push 触发第一次构建。
2. [配置你的Travis CI 构建](https://docs.travis-ci.com/user/customizing-the-build/)，如果需要。

### On your own environment

**Windows(cmd.exe)**
```bash
set CI=true&&npm test
```
```bash
set CI=true&&npm run build
```
（注意：空格的缺失是有意的）

**Linux, macOS(Bash)**
```bash
CI=true npm test
```
```bash
CI=true npm run build
```

测试命令将强制Jest运行测试一次，而不启动观察器。
> 如果你发现自己在开发中经常遇到这种情况，请[提出一个问题](https://github.com/facebookincubator/create-react-app/issues/new)来告诉我们你的用例，因为我们希望让观察者有最佳体验，并且可以随时更改工作流程以适应更多的工作流程。

构建命令将检查linter警告，如果找到任何警告，则会失败。

### Disabling jsdom

默认的，生成项目的`package.json` 像这样：
```js
  // ...
  "scripts": {
    // ...
    "test": "react-scripts test --env=jsdom"
  }
```

如果你知道没有一个测试依赖于[jsdom]()，你可以安全地删除`--env=jsdom`，你的测试运行得更快。

为了帮助您解决问题，以下是**需要jsdom**的API列表：

* 任何像`window` 和`document` 的浏览器全局变量
* [`ReactDOM.render()`](https://facebook.github.io/react/docs/top-level-api.html#reactdom.render)
* [`TestUtils.renderIntoDocument()`](https://facebook.github.io/react/docs/test-utils.html#renderintodocument)（上述的一个[简写](https://github.com/facebook/react/blob/34761cf9a252964abfaab6faf74d473ad95d1f21/src/test/ReactTestUtils.js#L83-L91)）
* [Enzyme](http://airbnb.io/enzyme/index.html) 中的[`mount()`](http://airbnb.io/enzyme/docs/api/shallow.html)

相反，下面API**jsdom 不是必须的**
* ['TestUitls.createRenderer()'](https://facebook.github.io/react/docs/test-utils.html#shallow-rendering)（浅渲染）
* [Enzyme](http://airbnb.io/enzyme/index.html) 中的[`shallow()`](http://airbnb.io/enzyme/docs/api/shallow.html)

最后，对于[snapshot testing](http://facebook.github.io/jest/blog/2016/07/27/jest-14.html) jsdom 也不是必须的。

### Snapshot Testing

Snapshot testing是Jest的一个功能，可自动生成组件的文本快照并将其保存在磁盘上，以便在UI输出更改时，你可以在不在组件输出上手动写入任何断言的情况下获得通知。 [详细了解snapshot testing](http://facebook.github.io/jest/blog/2016/07/27/jest-14.html)。

### Editor Integration

如果你使用[Visual Studio Code]()，则有一个[Jest扩展]()可以与Create React App开箱即用。 这在使用文本编辑器时提供了很多类似IDE的功能：使用潜在的故障消息内联显示测试运行的状态，自动启动和停止观察器，并提供一键式快照更新。

{% asset_img Editor-Integration.png %}

## Developing Components in Isolation

通常，在应用程序中，你有很多UI组件，并且它们每个都有许多不同的状态。 例如，一个简单的按钮组件可以具有以下状态：

* 带有文本标签。
* 用表情符号。
* 禁用模式。

通常，如果不运行示例应用程序或一些示例，很难看到这些状态，。

默认情况下，Create React App不包含这些工具，但你可以轻松地将[React Storybook](https://github.com/kadirahq/react-storybook)添加到你的项目中。 **它是一个第三方工具，可让你开发组件，并与你的应用程序隔离，查看所有状态。**

{% asset_img Developing-Components-in-Isolation.gif %}

你也可以将Storybook部署为静态应用。 这样，你的团队中的每个人都可以查看和查看UI组件的不同状态，而无需启动后端服务器或在应用程序中创建一个帐户。

**如何在你的应用中设置Storybook**

首先，全局安装下面npm 包
```bash
npm install -g getstorybook
```

然后，在你的应用目录中运行下面命令：
```bash
getstorybook
```

之后，按照屏幕上的说明。

了解更多React Storybook：

* 屏幕录像：[Getting Started with React Storybook](https://egghead.io/lessons/react-getting-started-with-react-storybook)
* [GitHub Repo](https://github.com/kadirahq/react-storybook)
* [文档](https://getstorybook.io/docs)
* React Storybook 的[Snapshot Testing](https://github.com/kadirahq/storyshots)

## Making a Progressive Web App

你可以按照[此仓库](https://github.com/jeffposnick/create-react-pwa)中的步骤将你的React应用程序转换为[Progressive Web App](https://developers.google.com/web/progressive-web-apps/)。

## Deployment

`npm run build` 为你应用程序的生产构建创建一个`build`目录。 设置你最喜欢的HTTP服务器，以便为你的站点的访问者提供`index.html`，并且请求静态路径像`/static/js/main.<hash>.js`获取`/static/js/main.<hash>.js` 文件的内容。

### Static Server

对于使用[Node](https://nodejs.org/)的环境，处理这种情况的最简单的方法是安装[serve](https://github.com/zeit/serve)并让其处理rest：

```bash
npm install -g serve
serve -s build
```
上面显示的最后一个命令将在端口**5000**上为你的静态站点提供服务。像许多[serve](https://github.com/zeit/serve)的内部设置一样，端口可以使用`-p`或`--port`标志进行调整。

运行此命令以获取可用选项的完整列表：
```bash
serve -h
```

### Other Solutions

你不一定需要静态服务器才能在生产中运行Create React App 项目。 它的工作也能很好集成到一个现有的动态项目中。

以下是使用Node和Express的编程示例：
```js
const express = require('express');
const path = require('path');
const app = express();

app.use(express.static('./build'));

app.get('/', function(req, res){
  res.sendFile(path.join(__dirname, './build', 'index.html'))
});

app.listen(5000)
```

你的服务器软件的选择也不重要。 由于Create React App完全与平台无关，因此无需明确使用Node。

具有静态资源的`build`文件夹是Create React App生成的唯一输出。

但是，如果你使用客户端路由，这还不够。 如果你希望在单页应用程序中支持像`/todos/42` 这样的URL，请阅读下一节。

### Serving Apps with Client-Side Routing

如果在底层，你使用了HTML5 [`pushState` history API](https://developer.mozilla.org/en-US/docs/Web/API/History_API#Adding_and_modifying_history_entries)的路由器（例如，使用`browserHistory`的[React Router](https://github.com/ReactTraining/react-router)），则许多静态文件服务器将失败。 例如，如果你的React Router 中的route 使用`/todos/42`，则开发服务器将正确响应`localhost：3000/todos/42`，但是Express 服务器的生产版本将失败。

这是因为当`/todos/42` 作为新的页面加载时，服务器会查找`build/todos/42`文件，但是找不到。 服务器需要配置为通过`index.html`来响应`/todos/42`的请求。 例如，我们可以修改我们上面的Express示例，为任何未知路径提供`index.html`：

```diff
  app.use(express.static('./build'));

 -app.get('/', function(req, res))
 +app.get('/*', function(req, res)){
   res.sendFile(path.join(__dirname, './build', 'index.html'));
 }
```

如果你使用[Apache]()，则需要在`public`文件夹中创建一个`.htaccess`文件，如下所示：
```
  Options -MultiViews
  RewriteEngine On
  RewriteCond %{REQUEST_FILENAME} !-f
  RewriteRule ^ index.html [QSA,L]
```

运行`npm run build`时，它将被复制到`build`文件夹。

现在，对`/todos/42` 的请求将在开发和生产中都被正确处理。

### Building for Relative Paths

默认情况下，Create React App会生成一个构建，假设你的应用程序是托管在服务器根目录。
要覆盖它，请指定`package.json`中的`homepage`，例如：
```js
  "homepage": "http://mywebsite.com/relativepath"
```

这将使Create React App正确地推断在生成的HTML文件中使用的根路径。

**Serving the Same Build from Different Paths**

> 注意：这个特性需要`react-scripts@0.9.0` 版本以上。

如果你没有使用HTML5 `pushState` history API，或者根本不使用客户端路由，则无需指定服务应用程序的URL。 相反，你可以把它放在你的package.json中：
```js
  "homepage": ".",
```

这将确保所有资源路径都相对于`index.html`。 然后，你可以将你的应用程序从`http://mywebsite.com` 移动到`http://mywebsite.com/relativepath` 甚至是`http://mywebsite.com/relative/path`，而无需重新构建它。

### Azure

请参阅[此博文](https://medium.com/@to_pe/deploying-create-react-app-on-microsoft-azure-c0f6686a4321)，了解如何将React应用程序部署到[Microsoft Azure](https://azure.microsoft.com/)。

### Firebase

如果你尚未运行`npm install -g firebase-tools`，请安装Firebase CLI。 注册一个[Firebase帐户](https://console.firebase.google.com/)并创建一个新项目。 运行`firebase login`并使用以前创建的Firebase帐户登录。

然后从项目的根目录运行`firebase init`命令。 你需要选择**Hosting：Configure and deploy Firebase Hosting sites**，并选择你在上一步骤中创建的Firebase项目。 你将需要同意正在创建的`database.rules.json`，选择`build`作为公共目录，并通过回复`y`来同意**Configuire as a single-page app**。

```
=== Project Setup

   First, let's associate this project directory with a Firebase project.
   You can create multiple project aliases by running firebase use --add,
   but for now we'll just set up a default project.

   ? What Firebase project do you want to associate as default? Example app (example-app-fd690)

   === Database Setup

   Firebase Realtime Database Rules allow you to define how your data should be
   structured and when your data can be read from and written to.

   ? What file should be used for Database Rules? database.rules.json
   ✔  Database Rules for example-app-fd690 have been downloaded to database.rules.json.
   Future modifications to database.rules.json will update Database Rules when you run
   firebase deploy.

   === Hosting Setup

   Your public directory is the folder (relative to your project directory) that
   will contain Hosting assets to uploaded with firebase deploy. If you
   have a build process for your assets, use your build's output directory.

   ? What do you want to use as your public directory? build
   ? Configure as a single-page app (rewrite all urls to /index.html)? Yes
   ✔  Wrote build/index.html

   i  Writing configuration info to firebase.json...
   i  Writing project information to .firebaserc...

   ✔  Firebase initialization complete!
```

现在，在使用`npm run build` 创建生产构建之后，可以通过运行`firebase deploy`进行部署。

```sh
=== Deploying to 'example-app-fd690'...

i  deploying database, hosting
✔  database: rules ready to deploy.
i  hosting: preparing build directory for upload...
Uploading: [==============================          ] 75%✔  hosting: build folder uploaded successfully
✔  hosting: 8 files uploaded successfully
i  starting release process (may take several minutes)...

✔  Deploy complete!

Project Console: https://console.firebase.google.com/project/example-app-fd690/overview
```

更多信息请看[添加Firebase 到你的JavaScript 项目](https://firebase.google.com/docs/web/setup)。

### Github Pages

> 注意：这个特性需要`react-scripts@0.2.0` 版本以上。

**Step 1：Add `homepage` to `package.json`**

**下面的步骤很重要！**
**如果你跳过它，你的应用程序将无法正确部署。**

打开你的`package.json`，添加`homepage` 域：
```js
 "homepage": "https://myusername.github.io/my-app",
```

Create React App 使用`homepage` 来确定构建的HTML文件中的根URL。

**Step 2:
Install `gh-pages` and add 'deploy' to 'scripts' in `package.json`**

现在，每当运行`npm run build`时，你将看到一个备忘单，其中包含如何部署到GitHub Pages的说明。

要发布在 https://myusername.github.io/my-app，请运行：
```sh
 npm install --save-dev gh-pages
```

在`package.json` 中添加下面脚本：
```js
  // ...
  "scripts": {
    // ...
    "predeploy": "npm run build",
    "deploy": "gh-pages -d build"
  }
```

`predeploy` 会在运行`deploy` 之前自动运行。


**Step 3：Deploy the site by running `npm rung deploy`**

然后运行：
```sh
  npm run deploy
```

**Step 4: Ensure your project's settings use `gh-pages`**

最后，确保GitHub项目设置中的GitHub Pages选项设置为使用`gh-pages`分支：

{% asset_img GitHub-Pages.png %}

**Step 5: Optionally, configure the domain**

你可以通过将CNAME文件添加到`public/`文件夹来配置具有GitHub Pages的自定义域。

**Notes on client-side routing**

GitHub Pages 在底层不支持使用HTML5 `pushState` history API的路由器（例如，使用`browserHistory`的React Router）。 这是因为当一个网址载入新的页面时，如http://user.github.io/todomvc/todos/42，其中`/todos/42`是前端路由，GitHub Pages服务器返回404，因为它不知道`/todos/42` 任何事。 如果要将路由器添加到GitHub Pages上托管的项目，以下是一些解决方案：

* 你可以从使用HTML5 history API切换到使用哈希的路由。 如果你使用React Router，你可以切换到`hashHistory` 实现此效果，但是该URL会更长更详细（例如，`http://user.github.io/todomvc/#/todos/42?_k=yknaj`） 。 [阅读](https://github.com/reactjs/react-router/blob/master/docs/guides/Histories.md#histories)有关React Router中不同历史记录实现的更多信息。
* 或者，你可以使用一个技巧来教导GitHub Pages通过使用特殊的重定向参数重定向到你的`index.html`页面来处理404.html。 在部署项目之前，你需要将一个包含重定向代码的`404.html`文件添加到`build`文件夹中，并且你需要添加将redirect参数处理的代码到`index.html`。 你可以[在本指南中](https://github.com/rafrex/spa-github-pages)找到此技术的详细说明。

### Heroku

使用[Heroku Buildpack for Create React App](https://github.com/mars/create-react-app-buildpack)。
你可以在[Deploying React with Zero Configuration](https://blog.heroku.com/deploying-react-with-zero-configuration) 找到介绍。

**Resolving Heroku Deployment Errors**

有时`npm run build` 在本地工作，但在部署期间通过Heroku失败。 以下是最常见的情况。

**"Module not found:Error:Cannot resolve 'file' or 'directory'"**

如果你得到这样的东西：
```
remote: Failed to create a production build. Reason:
remote: Module not found: Error: Cannot resolve 'file' or 'directory'
MyDirectory in /tmp/build_1234/src
```

这意味着你需要确保`import`的文件或目录的文字大小符合你在文件系统或GitHub上看到的文件或目录。

这很重要，因为Linux（Heroku使用的操作系统）区分大小写。 所以`MyDirectory`和`mydirectory`是两个不同的目录，因此，尽管项目在本地构建成，但是区别在于破坏Heroku 远程上的`import`语句。

**"Could not find a required file."**

如果从包中排除或忽略必需的文件，你将看到类似于以下错误：
```
remote: Could not find a required file.
remote:   Name: `index.html`
remote:   Searched in: /tmp/build_a2875fc163b209225122d68916f1d4df/public
remote:
remote: npm ERR! Linux 3.13.0-105-generic
remote: npm ERR! argv "/tmp/build_a2875fc163b209225122d68916f1d4df/.heroku/node/bin/node" "/tmp/build_a2875fc163b209225122d68916f1d4df/.heroku/node/bin/npm" "run" "build"
```

在这种情况下，请确保该文件位于正确的大小写，并且在本地`.gitignore`或`~/.gitignore_global`上不被忽略。

### Modulus

请参阅[Modulus博客文章](http://blog.modulus.io/deploying-react-apps-on-modulus)，了解如何将你的react应用部署到Modulus。

### Netlify

**To do a manual deploy to Netlify's CDN**
```sh
npm install netlify-cli
netlify deploy
```

选择`build` 作为部署路径。

**To setup continuous delivery:**

使用此设置，当你push a git或open a pull request 时，Netlify将构建和部署：

1. [启动一个新的netlify项目](https://app.netlify.com/signup)
2. 选择你的Git托管服务并选择你的仓库
3. 单击`Build your site`

**Support for client-side routing:**

要支持`pushState`，请确保使用以下重写规则创建一个`public/_redirects`文件：
```
/ * /index.html 200
```

构建项目时，Create React App会将`public`文件夹内容放入构建输出。

### Now

[now](https://zeit.co/now)提供零配置的单命令部署。

1. now可以通过推荐的[桌面工具](https://zeit.co/download)或通过node 的`npm install -g now`。
2. 通过运行`npm install --save serve`来安装`serve`。
3. 将此行添加到`package.json`中的`scripts`中：
```
  "now-start": "serve build/",
```
4. 在从你的项目目录中运行`now`。 你的输出中将会显示一个`now.sh`URL，如下所示：
```
> Ready! https://your-project-dirname-tpspyhtdtk.now.sh (copied to clipbord)
```
构建完成后，将该URL粘贴到浏览器中，你将看到已部署的应用程序。

[本文](https://zeit.co/blog/now-static)提供了详细信息。

### S3 and CloudFront

请参阅[此博文](https://medium.com/@omgwtfmarc/deploying-create-react-app-to-s3-or-cloudfront-48dae4ce0af)，介绍如何将你的React应用程序部署到Amazon Web Services [S3](https://aws.amazon.com/s3)和[CloudFront](https://aws.amazon.com/cloudfront/)。

### Surge

如果你尚未运行`npm install -g surge`，请安装Surge CLI。 运行`surge`命令并登录或创建一个新帐户。

当询问项目路径时，请确保指定`build`文件夹，例如：
```js
  project path: /path/to/project/build
```

请注意，为了支持使用HTML5 `pushState` API的路由器，你可能需要将构建文件夹中的`index.html`重新命名为`200.html`，然后再部署到Surge。 这样[可以确保每个URL都回退该文件](https://surge.sh/help/adding-a-200-page-for-client-side-routing)。

## Advanced Configuration

你可以通过在shell中设置环境变量或[.env](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md#adding-development-environment-variables-in-env)来调整各种开发和生产设置。

Variable | Development | Production | Usage
:--- | :---: | :---: | :---
BROWSER | :white_check_mark: | :x: | 默认情况下，Create React App 将打开默认系统浏览器，在Mac OS上支持Chrome。 指定[浏览器](https://github.com/sindresorhus/opn#app)来覆盖此行为，或将其设置为`none`以完全禁用它。
HOST | :white_check_mark: | :x: | 默认情况下，开发Web服务器绑定到`localhost`。 你可以使用此变量来指定不同的主机。
PORT | :white_check_mark: | :x: | 默认情况下，开发Web服务器将尝试在端口3000上侦听或提示你尝试下一个可用端口。 你可以使用此变量来指定不同的端口。
HTTPS | :white_check_mark: | :x: | 当设置为 `true` 时, Create React App 将以`https` 模式运行开发Web服务器。
PUBLIC_URL | :x: | :white_check_mark: | Create React App 假定你的应用程序托管在Web服务器的根目录或在[`package.json`（`homepage`）](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md#building-for-relative-paths)中指定的子路径。 通常，Create React App 忽略hostname。 你可以使用此变量强制将资源逐字引用到你提供的URL（包含hostname）。 当使用CDN托管你的应用程序时，这可能特别有用。
CI | :large_orange_diamond: | :white_check_mark: | 当设置为`true` 时, Create React App 将警告视为构建中的失败。 它也使test runner 处于非观察者模式. 大多数CI 默认设置此标志。

## Troubleshooting

### `npm start` doesn't  detect changes

当`npm start`运行时保存文件，浏览器将刷新更新的代码。
如果没有发生这种情况，请尝试以下解决方法之一：

* 如果你的项目在Dropbox文件夹中，请尝试将其移出。
* 如果观察者看不到名为`index.js`的文件，并且你通过文件夹名称引用它，则[需要重新启动观察者](https://github.com/facebookincubator/create-react-app/issues/1164)，这是Webpack错误导致的。
* 一些像Vim和IntelliJ这样的编辑器有一个“安全写入”功能，目前会打断观察者。你需要禁用它。按照[“使用支持安全写入的编辑器”](https://webpack.github.io/docs/webpack-dev-server.html#working-with-editors-ides-supporting-safe-write)中的说明进行操作。
* 如果你的项目路径包含括号，请尝试将项目移动到没有它们的路径。这是由[Webpack观察者错误](https://github.com/webpack/watchpack/issues/42)引起的。
* 在Linux和macOS上，你可能需要[调整系统设置](https://webpack.github.io/docs/troubleshooting.html#not-enough-watchers)以允许更多观察者。
* 如果项目在虚拟机中运行，如虚拟机（Virtual Vagrant），则在项目目录中创建一个`.env`文件，如果它不存在，并向其添加`CHOKIDAR_USEPOLLING=true`。这样可以确保下次运行`npm start`时，观察者会根据需要在VM中使用轮询模式。

如果这些解决方案没有帮助，请在[此主题中](https://github.com/facebookincubator/create-react-app/issues/659)留下评论。

### `npm test` hangs on macOS Sierra

如果你运行`npm test`，并且控制台在打印`react-scripts test --env=jsdom`之后卡住，则可能是你的[Watchman](https://facebook.github.io/watchman/)安装问题，如[facebookincubator/create-react-app＃713]()https://github.com/facebookincubator/create-react-app/issues/713中所述。

我们建议你先删除项目中的`node_modules`并运行`npm install`（或者`yarn`如果你使用的话）。 如果没有帮助，你可以尝试以下问题中提到的许多解决方法之一：

* [facebook/jest#1767](https://github.com/facebook/jest/issues/1767)
* [facebook/watchman#358](https://github.com/facebook/watchman/issues/358)
* [ember-cli/ember-cli#6259](https://github.com/ember-cli/ember-cli/issues/6259)

据悉，安装Watchman 4.7.0或更新版本可以解决此问题。 如果你使用[Homebrew](http://brew.sh/)，则可以运行这些命令来更新它们：
```sh
watchman shutdown-server
brew update
brew reinstall watchman
```

你可以在Watchman文档页面上找到[其他安装方法](https://facebook.github.io/watchman/docs/install.html#build-install)。

如果仍然没有帮助，请尝试运行`launchctl unload -F ~/Library/LaunchAgents/com.github.facebook.watchman.plist`。

还有报告说，卸载Watchman可以解决问题。 所以如果没有其他帮助，请从系统中删除它，然后重试。

### `npm run build` silently fails

据报道，在没有swap space的机器上，`npm run build` 可能会失败，这在云环境中很常见。 如果[症状匹配](https://github.com/facebookincubator/create-react-app/issues/1133#issuecomment-264612171)，请考虑为构建的机器添加一些swap space，或在本地构建项目。

### `npm run build` fails on Heroku

这可能是文件名大小写敏感的问题。 请参阅[本节](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md#resolving-heroku-deployment-errors)。

## Someting Missing?

如果你在这个页面上有更多的"How To" 的想法，请[让我们知道](https://github.com/facebookincubator/create-react-app/issues)或[贡献一些！](https://github.com/facebookincubator/create-react-app/edit/master/packages/react-scripts/template/README.md)
