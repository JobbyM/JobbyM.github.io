---
title: '[译]NodeJS 入门'
date: 2018-12-07 10:39:40
tags:
  - 技术
  - NodeJS
  - 翻译
categories: 技术
---

{% asset_img how-to-use-nodejs_md.jpg%}

> 特别声明：此篇文章内容来源于[@Aditya Sridhar](https://github.com/aditya-sridhar) 的[How to use NodeJS without frameworks and external libraries](https://adityasridhar.com/posts/how-to-use-nodejs-without-frameworks-and-external-libraries)

NodeJS最常用于Express 框架。许多其他外部库也使用了NodeJS。

这些框架和库与NodeJS 一起使用的原因是使开发更容易，更快捷。

在处理任何实际项目时，最好在需要时使用框架和库来实现更快的开发😄

也就是说，在这篇文章中，我将展示如何在不使用Express 框架或任何其他外部库的情况下使用NodeJS 构建简单REST API。本文仅使用NodeJS 本身提供的那些功能。

这样做的原因是为了展示NodeJS 如何在没有框架和库的情况下使用😄。这也将更好地了解NodeJS 的工作原理

<!--more-->

## 前期准备
按照[https://nodejs.org](https://nodejs.org) 安装NodeJS。

## 代码
我的[github 仓库](https://github.com/aditya-sridhar/simple-rest-apis-nodejs-without-frameworks)中提供了本文的代码。

## 从代码开始😄
创建一个名为`simple-rest-apis-nodejs-without-frameworks` 的文件夹。 这将是我们的NodeJS 项目文件夹。

进入项目文件夹并使用`npm init` 将项目转换为NodeJS 项目。执行此操作的命令是
```bash
cd simple-rest-apis-nodejs-without-frameworks
npm init
```

## package.json
运行`npm init` 后，将在项目文件夹中创建`package.json` 文件。

`package.json` 包含有关项目的信息，如项目名称，版本，描述等。另外，`package.json` 是添加Node 依赖项的地方。在本文中，我们不会有任何依赖关系，因为我们只使用NodeJS 本身提供的功能。

## 第一个API
### server.js
在项目文件夹中创建一个名为`server.js` 的文件。这将是我们应用的起点。

将以下代码复制到`server.js`中
```js
const hostname = '127.0.0.1'
const port = 3000

const server = require('./controller.js')

server.listen(port, hostname, () => {
  console.log(`Server running at https://${hostname}:${port}/`)
})
```

这段代码实际上依赖于一个名为`controller.js` 的文件，我们将很快添加它。 此代码告诉服务器需要侦听`localhost` 上的`3000` 端口

服务器创建在`controller.js` 中完成

### controller
这是我们将创建服务器并定义其余端点的文件。创建一个名为`controller.js` 的文件

让我们先在`controller.js` 中创建一个GET 端点
```js
const http = require('http')
const url = require('url')

module.exports = http.createServer((req, res) => {

  var service = require('./service.js')
  const reqUrl = req.parse(req.url, true)

  // GET Endpoint
  if (reqUrl.pathname == '/sample' && req.method === 'GET') {
    console.log('Request Type: ' +
      req.method + ' Endpoint: ' +
      reqUrl.pathname)

    service.sampleRequest(req, res)
  }
})
```

首先导入`http` 和`url` 模块。这些模块由NodeJS 本身提供。

`http` 模块可以创建Web 应用程序。它支持客户端和服务器操作。

`url` 模块用于解析网址

`http.createServer((req, res) => {` 表示需要创建一个http 服务器，`其请求为req，响应为res`

`module.exports` 用于将此文件导出为模块。这就是我们可以使用`const server = require('./controller.js')` 在`server.js` 中导入`controller.js` 的原因

可以看出，这个文件需要`service.js`，我们稍后会讨论。

代码`const reqUrl = req.parse(req.url, true)` 获取请求`url` 并解析它，以便我们可以在其上运行一些`url` 函数。

我们要创建的第一个端点是一个`GET` 端点，端点`url`为`/sample`

为了进行`url` 路由，我们将使用`if else条件`

行`if (reqUrl.pathname == '/sample' && req.method === 'GET') {` 检查请求的`url` 是否为`/sample`，并检查请求类型是否为`GET`

此`get` 请求的逻辑出现在`service.sampleRequest(req, res)`中,是`service.js` 中定义的函数

### service.js
这是实际的`api` 逻辑将存在的地方。创建一个名为`service.js` 的文件。

将以下代码复制到`service.js` 中
```js
const url = require('url')

exports.sampleRequest = function (req, res) {
  const reqUrl = url.parse(req.url, true)
  var name = 'World'
  if (reqUrl.query.name) {
    name = reqUrl.query.name
  }

  var response = {
    'text': 'Hello ' + name
  }

  res.statusCode = 200
  res.setHeader('Content-Type', 'application/json')
  res.end(JSON.stringify(response))
}
```

此代码检查请求`URL` 是否具有名为`name` 的查询参数，并将其存储在`name` 变量中。如果不存在查询参数，则默认为字符串`World`

响应状态设置为`200`，响应的内容类型为`JSON`，最后使用`res.end(JSON.stringify(response))` 返回响应

因为`响应变量` 是一个`JSON` 对象，我们在它上面使用`JSON.stringify` 将它转换为字符串，然后再发回`http` 响应

现在我们可以使用该命令运行应用程序
```bash
node server.js
```

### Testing
为了测试端点使用`postman`。你可以在[这里](https://www.getpostman.com/)下载

在`postman` 中选择`Get` 请求并输入`URL` 为`http://localhost:3000/sample?name=aditya` 并点击发送
{% asset_img get_name.jpg %}

此请求的输出如下所示
```js
{
  'text': 'Hello aditya'
}
```

现在输入网址为`http://localhost:3000/sample` 并点击发送

此请求的输出如下所示
```js
{
  'text': 'Hello World'
}
```

## 第二个API
在本节中，我们将构建第二个API，它是一个`POST`请求。此外，如果用户点击一些随机网址，我们将需要指出它是无效路由。我们也将在这里添加逻辑。

### controller.js
将`controller.js` 中的代码更新为如下所示的代码
```js
const http = require('http')
const url = require('url')

module.exports = http.createServer((req, res) => {

  var service = require('./service.js')
  const reqUrl = url.parse(req.url, true)

  // GET Endpoint
  if (reqUrl.pathname == '/sample' && req.method === 'GET') {
    console.log('Request Type:' +
      req.method + ' Endpoint: ' +
      reqUrl.pathname)

    service.sampleRequest(req, res)
    // POST Endpoint
  } else if (reqUrl.pathname == '/test' && req.method === 'POST'){
    console.log('Request Type:' +
      req.method + ' Endpoint: ' +
      reqUrl.pathname)

    service.testRequest(req, res)
  } else {
    console.log('Request Type:' +
      req.method + ' Invalid Endpoint: ' +
      reqUrl.pathname)

    service.invalidRequest(req, res)
  }
})
```

post端点将具有url `/test`。此代码具有检查`/test` 端点的条件。`/test` 端点的逻辑将在`service.testRequest(req, res)` 这是在`service.js`

此代码还具有无效路由的`else 条件`。无效路由的逻辑在`service.invalidRequest(req, res)` 中处理

### service.js
将以下代码添加到`service.js`。不要删除`service.js` 中的现有代码。在现有代码下面添加此代码。
```js
exports.testRequest = function (req, res) {
  body = ''

  req.on('data', function (chunk) {
    body += chunk
  })

  req.on('end', function () {

    postBody = JSON.parse(body)

    var response = {
      'text': 'Post Request Value is ' + postBody.value
    }

    res.statusCode = 200
    res.setHeader('Content-Type', 'application/json')
    res.end(JSON.stringify(response))
  })
}
```

对于`POST` 请求，在调用端点时会给出输入`POST` body。

在代码中，我们需要从请求中获取此`POST` body。

以下代码执行此操作
```js
req.on('data', function (chunk) {
  body += chunk
})
```

请求以`流steam` 的形式出现。此代码获取数据流并继续将其附加到`body`。

`req.on('end', function () {` 仅在流式传输完成并且收到完整的`post body`后执行。

`postBody = JSON.parse(body)` 此代码将输入的`post body` 转换为JSON格式，以便我们可以使用其中的值。

在代码中我们在`postBody`中使用`value` 字段。

响应的设置类似于我们为`GET` 请求所做的操作。

### 无效请求的逻辑
将以下代码添加到`service.js`。不要删除`service.js` 中的现有代码。在现有代码下面添加此代码。
```js
exports.invalidRequest = function (req, res) {
  res.statusCode = 404
  res.setHeader('Content-Type', 'text/plain')
  res.end('Invalid Request')
}
```

对于无效请求，状态设置为`404`，内容类型设置为`text`。发回的实际内容是`Invalid Request`

### Testing
进入`postman`。选择请求类型为`POST` 并键入以下URL `http://localhost:3000/test`。
{% asset_img post_request.jpg %}

同时选择如图所示的`Body`，`raw`和`application/json`

输入`Post body`如下
```js
{
  'value': 'nodejs'
}
```

点击`postman` 发送

API 输出如下
```js
{
  'text': 'Post Request Value is nodejs'
}
```

你也可以尝试使用无效请求。在`postman` 中选择`GET` 并输入URL `http://localhost:3000/test123`

对此的响应将是`Invalid Text`

## 恭喜😄
你现在知道如何在不使用任何框架或外部库的情况下在NodeJS 中创建REST API

在真实项目中，尽可能使用框架或库来使开发更容易，更快捷

在[LinkedIn](https://www.linkedin.com/in/aditya1811)中与我联系或在[Twitter](https://www.twitter.com/adityasridhar18)上关注我
