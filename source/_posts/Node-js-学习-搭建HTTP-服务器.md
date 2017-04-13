---
title: Node.js 学习-搭建HTTP 服务器
comments: true
tags:
  - 技术
  - Node.js
categories: 技术
---

## 基础

使用Node.js 搭建HTTP 服务器非常简单，可以简单的分为三步：

**第一步**、编写`app.js` 文件
```js
var http = require('http')

http.createServer(function(req, res){
  res.writeHead(200, {'Content-Type': 'text/plain'})
  res.end('Hello World\n')
}).listen(8080, '127.0.0.1')

console.log('Server running at 127.0.0.1:8080')
```
上面代码第一行`var http = require('http')`，表示加载`http` 模块。然后调用`http` 模块的`createServer` 方法，创建一个服务器实例。

`createServer` 方法接受一个函数作为参数，该函数的`req` 表示客户端的HTTP 请求；`res` 表示服务器端的HTTP 响应。`res.writeHead` 方法用来写入HTTP 响应的头信息；`res.end` 方法用来写入HTTP 响应的具体内容，已经响应完成后关闭本次对话。最后的`listen(8080)` 表示启动服务器实例，监听本机的8080 端口。

<!--more-->

**第二步**、用nodejs 运行`app.js`
```bash
node app.js
```
 {% asset_img node_server_app.jpg %}

**第三步**、在浏览器中访问http://127.0.0.1:8080 就可以看到结果了
 {% asset_img node_server_run_app.jpg %}

## 进阶

上面的例子非常简单，服务器收到请求后生成网页，也可以是事先写好网页文件，然后利用`fs` 模块读取网页文件，将其返回。

**第一步**、编写`data.txt`文件，其内容为简单的'Hello World'
```js
Hello World
```

**第二步**、编写`app.js` 文件

首先需要引入用到的几个模块：http 协议模块、文件系统模块、url 解析模块、路径解析模块。
```js
var http = require('http')
var fs = require('fs')
var url = require('url')
var path = require('path')
```

接着调用`http` 模块的`createServer` 方法，创建一个服务器实例。通过`listen(8080,'127.0.0.1')` 启动服务器。
```js
http.createServer(function(req, res){
  // 主要代码...

}).listen(8080, '127.0.0.1')
```

最后`createServer` 方法接受的函数参数，需要进行着重处理。按照下面流程进行处理：
1、根据`req.url` 获取`pathname`（路径名称）；
2、通过`path.join()` 进行指定`realPath`（文件路径）；
3、以及通过`path.parse().ext` 获取`ext`（后缀名）;
4、通过`fs.readFile` 处理获得的文件。如果出现错误，则返回404 页面；否则则根据`ext`，获得对应的`Content-Type` 类型，这里使用switch 语句进行处理的

下面是完整代码：
```js
var http = require('http')
var fs = require('fs')

http.createServer(function(req, res){
  fs.readFile('data.txt', function(err, data){
    res.writeHead(200, {'Content-Type': 'text/plain'})
    res.end(data)
  })
}).listen(8080, '127.0.0.1')

console.log('Server running at 127.0.0.1:8080')
```
上面代码第二行`var fs = require('fs')`，表示加载`fs` 模块。然后调用`fs` 模块的`readFile` 方法，进行文件的读取。

`readFile` 方法的一个参数是文件的路径；第二个参数是读取完成后的回调函数。该函数的第一个参数是发生错误时的错误对象，第二个参数是代表文件内容的。

**第四步**、用nodejs 运行`app.js`
```bash
node app.js
```

**第五步**、在浏览器中访问http://127.0.0.1:8080 就可以看到结果了

## 高阶

上面进阶的例子也只是可以读取文件，然后返回给浏览器。下面我们实现一个具有以下功能的HTTP 服务器：
1、显示以`.html`结尾的Web 页面
2、显示图片资源
3、直接打开以`.js`、`.css`、`.txt`等文件

**第一步**、编写`app.js` 文件
```js
var http = require('http')
var fs = require('fs')
var url = require('url')
var path = require('path')

http.createServer(function(req, res){

	var pathname = url.parse(req.url).pathname;
	var realPath = path.join("./",pathname); // 指定目录
	var ext = path.parse(pathname).ext;
	ext = ext.slice(1)

	fs.readFile(realPath, function(err, data){
		if(err){
			res.writeHead(404, {'Content-Type':'text/html'})
			res.end("<h1>404 Not Found</h1>")
			return;
		}

		var contentType = ''
		switch(ext){
			case "css":
				contentType = "text/css"; break;
			case "gif":
				contentType = "image/gif"; break;
			case "html":
				contentType = "text/html"; break;
			case "ico":
				contentType = "image/x-icon"; break;
			case "jpeg":
				contentType = "image/jpeg"; break;
			case "jpg":
				contentType = "image/jpg"; break;
			case "js":
				contentType = "text/javascript"; break;
			case "png":
				contentType = "image/png"; break;
			case "txt":
				contentType = "text/plain"; break;
			default:
				contentType = 'text/plain';
		}

		res.writeHead(200, {'Content-Type': contentType})
		res.end(data)
	})
}).listen(8080, '127.0.0.1')

console.log('Server running at 127.0.0.1:8080')
```

**第二步**、用nodejs 运行`app.js`
```bash
node app.js
```

**第三步**、在浏览器中访问不同的网址会有不同的效果
1. 访问一个存在的文件，例如：http://localhost:8080/1.html 会显示1.html 这个页面
1. 访问一个不存在的文件，例如：http://localhost:8080/2.html 会显示404 错误页面
2. 访问一个存在的图片，例如：http://localhost:8080/1.jpg 会显示这个图片


## 参考文档

1. [ruanyifeng nodejs http 模块](http://javascript.ruanyifeng.com/nodejs/http.html)
