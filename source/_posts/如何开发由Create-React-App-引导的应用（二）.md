---
title: 如何开发由Create-React-App 引导的应用（二）
date: 2017-04-28 10:03:49
comments: true
tags:
  - 技术
  - Create React App
  - 翻译
categories: 技术
---

> 此文章是翻译[How to develop apps bootstrapped with Create React App](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md) 官方文档

## 系列文章

1. {% post_link 如何开发由Create-React-App-引导的应用  如何开发由Create-React-App 引导的应用 %}
2. {% post_link 如何开发由Create-React-App-引导的应用（一） 如何开发由Create-React-App 引导的应用（一）%}
3. {% post_link 如何开发由Create-React-App-引导的应用（三） 如何开发由Create-React-App 引导的应用（三）%}
4. {% post_link 如何开发由Create-React-App-引导的应用（四） 如何开发由Create-React-App 引导的应用（四）%}

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

<!--more-->

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

**对于React 这并不是必需的**，但是许多人发现这个特性很方便。你可以在[这里](https://medium.com/seek-ui-engineering/block-element-modifying-your-javascript-components-d7f99fcab52b)阅读这种方法的好处。但是，您应该意识到，这使得你的代码更适合Webpack而不是他构建工具和环境。

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
