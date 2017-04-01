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
