---
title: creating a react app from scratch
date: 2018-07-26 08:41:43
comments: true
tags:
  - 技术
  - React
  - 翻译
categories: 技术
---

> 子曰：从0 开始搭建React 应用
> 本文翻译自[Jedai Saboteur](https://blog.usejournal.com/@JedaiSaboteur?source=post_header_lockup) [creating a react app from scratch](https://blog.usejournal.com/creating-a-react-app-from-scratch-f3c693b84658)

{% asset_img react_0.png %}
<!--more-->

React 并不是 **真正意义上** 的开箱即用。node（截至我们在本教程中使用的v.9.3.0 版本）还没有完全支持，React 使用的关键字和语法。它需要相当多繁琐的设置才能弄清楚，因此Facebook 提供了一个选项，可以轻松启动React应用程序。

问题是，[create-react-app](https://github.com/facebook/create-react-app)抽象了很多让React 应用程序的工作 -- 至少没有弹出它可以手动调整的选项。你想要自己实现的原因有很多，至少要知道它在做什么。

我说过，启动React 应用程序有几个障碍。首先是node 无法处理所有语法（例如`import/export` 和`jsx`）。第二个问题是，你需要构建文件或在开发过程中以某种方式为你的应用程序提供服务 -- 这在后一种情况下尤为重要。

幸运的是，我们可以使用`Babel` 和`Webpack` 处理这些问题。

## 设置
首先，为React 应用程序创建一个新目录。 然后，使用`npm init` 初始化项目，并在你选择的编辑器中打开它。这是也可以`git init`。在新项目文件夹中，创建以下结构：
```
.
+-- public
+-- src
```

稍微考虑一下，我们最终想要构建我们的应用程序，我们可能希望从提交中排除构建版本和我们的node 模块，所以让我们继续添加`.gitignore` 文件，不包括（至少）目录`node_modules` 和`dist`。

我们的`public` 目录将处理任何静态资源，最重要的是包含我们的`index.html` 文件，该文件将用于呈现你的应用程序。 以下代码来自`react` 文档，进行了微调。 你可以将以下HTML标记复制到`public` 目录内的新文件`index.html` 中。
```
<!-- sourced from https://raw.githubusercontent.com/reactjs/reactjs.org/master/static/html/single-file-example.html -->
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    <title>React Starter</title>
  </head>
  <body>
    <div id="root"></div>
    <noscript>
      You need to enable JavaScript to run this app.
    </noscript>
    <script src="../dist/bundle.js"></script>
  </body>
</html>
```

需要注意的最第10 行，这是我们的`React` 应用程序将要挂载的节点，以及第14行，它引用了我们（即将成为）构建的`React` 应用程序。你可以任意命名构建的脚本，在本教程中我将使用`bundle.js`。

现在我们已经设置了HTML页面，我们就可以正式开始了。我们还需要设置一些东西。首先，我们需要确保编写的代码可以编译，因此我们需要`Babel`。

## Babel
继续运行`npm install --save-dev babel-core@6.26.3 babel-cli@6.26.0 babel-preset-env@1.7.0 babel-preset-react@6.24.1`。

`babel-core` 是主要的babel包 - 我们需要这个让babel 对我们的代码进行任何转换。`babel-cli` 允许你从命令行编译文件。`babel-preset-env` 和`babel-preset-react` 都是转换特定代码风格的预设 - 在这种情况下，`env` 预设允许我们将ES6+ 转换为更传统的javascript，而`react` 预设将`JSX` 转换为更为传统的javascript。

在项目根目录中，创建一个名为`.babelrc` 的文件。在这里，我们告诉babel 我们将使用`env` 和`react` 预设。
```
{
  "presets": ["env", "react"]
}
```

如果你只需要转换特定功能或者你需要的某些功能未被`env` 包含，Babel也有大量可用的插件可供使用。我们现在不会担心这些，你可以在[这里](https://babeljs.io/docs/plugins/) 了解更多。

## Webpack
现在我们需要获取和配置Webpack。我们需要更多的软件包，你需要将它们保存为dev 依赖项：`npm install --save-dev webpack@4.12.0 webpack-cli@3.0.8 webpack-dev-server@3.1.4 style -loader@0.21.0 css-loader@0.28.11 babel-loader@7.1.4`。

Webpack使用加载器处理不同类型的文件以进行打包。它还可以与我们将用于在开发中为React 项目提供服务的开发服务器一起使用，并在重新加载的浏览器页面来（保存）更改的React 组件。 为了使用它们，我们需要配置Webpack 来使用我们的加载器并准备开发服务器。

在项目的根目录下创建一个名为`webpack.config.js` 的新文件。这个文件导出`webpack` 的配置对象。
```
const path = require("path");
const webpack = require("webpack");

module.exports = {
  entry: "./src/index.js",
  mode: "development",
  module: {
    rules: [
      {
        test: /\.(js|jsx)$/,
        exclude: /(node_modules|bower_components)/,
        loader: 'babel-loader',
        options: { presets: ['env'] }
      },
      {
        test: /\.css$/,
        use: [ 'style-loader', 'css-loader' ]
      }
    ]
  },
  resolve: { extensions: ['*', '.js', '.jsx'] },
  output: {
    path: path.resolve(__dirname, "dist/"),
    publicPath: "/dist/",
    filename: "bundle.js"
  },
  devServer: {
    contentBase: path.join(__dirname, "public/"),
    port: 3000,
    publicPath: "http://localhost:3000/dist/",
    hotOnly: true
  },
  plugins: [ new webpack.HotModuleReplacementPlugin() ]
};
//  source from https://gist.github.com/paradoxinversion/a529d12db704bb78248368c202a2cd2d#file-react-app-tutorial-webpack-config-js
```

让我们快速浏览一下：[entry](https://webpack.js.org/configuration/entry-context/#entry)（第5行）告诉Webpack 我们的应用程序在哪里开始以及从哪里开始打包我们的文件。下一行让webpack 知道我们正在开发模式下工作 - 这使我们不必在运行开发服务器时添加模式标志。

[module](https://webpack.js.org/configuration/module) 对象有助于根据给定的[rules](https://webpack.js.org/configuration/module#module-rules) 数组定义导出的javascript 模块的转换方式以及包含哪些模块。

我们的第一条规则就是转换我们的`ES6` 和`JSX` 语法。 [test](https://webpack.js.org/configuration/module#rule-test) 和[exclude](https://webpack.js.org/configuration/module#rule-exclude) 属性是匹配文件的条件。在这种情况下，它将匹配`node_modules` 和`bower_components` 目录之外的任何内容。 由于我们也将转换我们的`.js` 和`.jsx` 文件，因此我们需要指示Webpack 使用Babel。最后，我们指定要在[option](https://webpack.js.org/configuration/module#rule-options-rule-query) 中使用`env` 预设。

下一条规则是处理CSS。由于我们不对CSS 进行预处理或后处理，因此我们只需要确保将`style-loader` 和`css-loader` 添加到[use](https://webpack.js.org/configuration/module/#rule-use) 属性中。`css-loader` 需要样式加载器才能工作。`loader` 是`use` 属性的简写，当只使用一个加载器。

[resolve](https://webpack.js.org/configuration/resolve) 属性允许我们指定Webpack 将解析哪些扩展 - 这允许我们导入模块而无需添加其后缀。

[output](https://webpack.js.org/configuration/output) 属性告诉Webpack 将打包代码放在何处。[publicPath](https://webpack.js.org/configuration/output#output-publicpath) 属性指定`bundle` 应该进入的目录，并告诉`webpack-dev-server` 从哪里提供文件。

[publicPath](https://webpack.js.org/configuration/output#output-publicpath) 属性是一个特殊属性，可以帮助我们使用`dev-server`。它指定了目录的公共URL - 至少就`webpack-dev-server` 而言，它是知情的。 如果设置不正确，你将得到404，因为服务器将无法从正确的位置提供你的文件！

我们在[devServer](https://webpack.js.org/configuration/dev-server) 属性中设置了`webpack-dev-server`。这对我们的需求来说并不需要太多 - 只是我们提供静态文件的位置（例如我们的`index.html`）和我们想要运行服务器的端口。 请注意，`devServer` 也有[publicPath](https://webpack.js.org/configuration/dev-server/#devserver-publicpath-) 属性。此`publicPath` 告诉服务器我们的打包代码实际上在哪里。

最后一点可能有点令人困惑 - 请注意这里：[output.publicPath](https://webpack.js.org/configuration/output/#output-publicpath) 和[devServer.publicPath](https://webpack.js.org/configuration/dev-server/#devserver-publicpath-) 是不同的。请多次阅读这两个条目。

最后，因为我们想要使[热模块替换](https://webpack.js.org/guides/hot-module-replacement/)，所以我们不必经常刷新以查看我们的更改。我们就此文件所做的就是在[plugins](https://webpack.js.org/configuration/plugins/) 属性中实例化插件的新实例，并确保在`devServer` 中将`hotOnly` 设置为`true`。不过，我们仍然需要在HMR工作之前在React中再设置一个东西。

我们完成了复杂的设置。现在让我们让React 工作吧！

## React
首先，我们还需要两个包：[react@16.4.1](https://reactjs.org/) 和[react-dom@16.4.1](https://www.npmjs.com/package/react-dom)。 继续将它们保存为常规依赖项。

我们需要告诉我们的React 应用程序在哪里挂载DOM（在我们的`index.html`中）。在`src` 目录中创建一个名为`index.js` 的文件。这是一个非常小的文件，为你的React 应用程序做了很多。让我们一探究竟。
```js
import React from "react";
import ReactDOM from "react-dom";
import App from "./App.js";
ReactDOM.render(
  <App />,
  document.getElementById("root")
);
// source from https://gist.github.com/paradoxinversion/d0a20939d24847669ee4284aaf484265#file-react-app-tutorial-index-js
```

`ReactDOM.render` 是告诉React 要呈现什么以及在何处呈现它的函数 - 在这个例子中，我们正在渲染一个名为`App` 的组件（我们很快就会创建），并且它将使用ID 为`root` 的DOM元素上进行渲染（`index.html` 的第10行）。

现在，在`src` 中创建另一个名为`App.js` 的文件。 如果你使用`create-react-app` 与React 合作过，那么这部分应该非常熟悉。这个文件只是一个React 组件。
```js
import React, { Component} from "react";
import "./App.css";

class App extends Component{
  render(){
    return(
      <div className="App">
        <h1> Hello, World! </h1>
      </div>
    );
  }
}

export default App;
// source from https://gist.github.com/paradoxinversion/db69a47eb4176b94f76b5504a8bdf092#file-react-app-tutorial-app-js
```

虽然我们还在这里，但我确实提到webpack 也处理CSS（我们要求它是我们的组件）。让我们在`src` 目录中添加一个非常简单的样式表。
```css
.App {
  margin: 1rem;
  font-family: Arial, Helvetica, sans-serif;
}
/* source from https://gist.github.com/paradoxinversion/c2b9b602aaffd9881539614e4d962817#file-react-app-tutorial-app-css */
```

你的最终项目结构应如下所示，除非你在此过程中更改了一些名称：
```
.
+-- public
| +-- index.html
+-- src
| +-- App.css
| +-- App.js
| +-- index.js
+-- .babelrc
+-- .gitignore
+-- package-lock.json
+-- package.json
+-- webpack.config.js
```

我们现在有一个功能正常的React 应用！我们可以通过在终端中执行`webpack-dev-server --mode development` 来启动我们的dev 服务器。 我建议将它放在`package.json` 的`start` 脚本中，以减少按键次数。

## 完成HMR
如果你现在运行服务器，会发现你的更改实际上没有导致客户端发生任何事情。

那么，HMR 需要知道实际取代什么，目前我们还没有给它任何东西。为此，我们将使用react 团队提供给我们的包：`react-hot-loader@4.3.3`。

你可以将其安装为常规依赖项 - 根据文档
> 注意：你可以安全地将react-hot-loader 安装为常规依赖项而不是dev依赖项，因为它会自动确保它不会在生产中执行且占用空间很小。

现在，在`App.js` 中导入`react-hot-loader`，并通过修改代码将导出的对象标记为热重新加载，如下所示。
```js
import React, { Component} from "react";
import {hot} from "react-hot-loader";
import "./App.css";

class App extends Component{
  render(){
    return(
      <div className="App">
        <h1> Hello, World! </h1>
      </div>
    );
  }
}

export default hot(module)(App);
// source from https://gist.github.com/paradoxinversion/dbfa5e5038ea30fcd929248bd3489dfe#file-react-app-tutorial-app-js
```

当你运行应用程序时，现在更改代码应在保存后立即更新客户端。

## 最后细节
你可能会注意到有关启动项目的一些有趣（或可能令人吃惊）：构建的文件永远不会显示在你的`dist` 目录中。`webpack-dev-server` 实际上是从内存中提供打包的文件 - 一旦服务器停止，它们就会消失。要实际构建文件，我们将正确使用webpack - 使用以下命令在`package.json`中添加一个名为`build`的脚本：`webpack --mode development`。你可以用`production` 替换`development`，但如果你完全省略`--mode`，它将回退到后者并给你一个警告。

这几乎涵盖了能够呈现基本React 应用程序所需的一切，而无需触摸`create-react-app`。还有更多要添加到实现中以使其更完整 - 例如，图像未设置为由Webpack处理，[但是有一个加载器](https://webpack.js.org/loaders/file-loader/)。我会把这个实现留给你。毕竟，如果你不需要或想要提供文件。

我希望这篇文章能够帮助我们了解React 应用程序的工作原理以及基础知识的工作原理。我没有深入研究Babel和Webpack的细节，但请浏览整篇文章中的任何无数链接或者直接进入他们的文档。它们是令人敬畏的工具，乍一看似乎比实际上更令人生畏，它们可以帮助你将开发提升到一个新的水平。

[随意查看我在github上的实现](https://github.com/paradoxinversion/react-starter)（它更深入一点）或在[推特上打个招呼](https://twitter.com/JedaiSaboteur)。

本文于2018年4月24日编辑，包括2018年5月13日的软件包版本，以反映有关webpack-dev-server的错误修正，以及2018年6月16日，以反映React和本指南中使用的其他软件包的更新。
