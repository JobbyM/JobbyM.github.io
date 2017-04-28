---
title: 如何开发由Create-React-App 引导的应用（三）
date: 2017-04-28 10:03:58
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
3. {% post_link 如何开发由Create-React-App-引导的应用（二） 如何开发由Create-React-App 引导的应用（二）%}
4. {% post_link 如何开发由Create-React-App-引导的应用（四） 如何开发由Create-React-App 引导的应用（四）%}

## Integrating with an API Backend

这些教程将帮助您将应用程序与在另一个端口上运行的API后端集成，使用`fetch()`来访问它。

### Node

看看[这个教程](https://www.fullstackreact.com/articles/using-create-react-app-with-a-server/)。 您可以在这里找到配套的[GitHub存储库](https://github.com/fullstackreact/food-lookup-demo)。

### Ruby on Rails

看看[这个教程](https://www.fullstackreact.com/articles/how-to-get-create-react-app-to-work-with-your-rails-api/)。 您可以在这里找到配套的[GitHub存储库](https://github.com/fullstackreact/food-lookup-demo-rails)。

<!--more-->

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
