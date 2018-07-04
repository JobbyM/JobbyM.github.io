---
title: 一日一练-JS 了解几种跨域技术
date: 2018-07-03 18:45:42
tags:
---

> 子曰：了解几种跨域机制

## 图像Ping
0x00：定义
图像Ping 是与服务器进行简单、单向的跨域通信的一种方式。请求的数据是通过查询字符串形式发送的，而响应可以是任意内容，但通常是像素图或204 响应。通过图像Ping，浏览器得不到任何具体的数据，但通过侦听load 和error 事件，它能知道响应是什么时候收到。

0x01：例子
```js
var img = new Image()
img.onload = img.onerror = function () {
  alert('Done!')
}
img.src = 'http://www.example.com/test?name=Nicholas'
```
这里创建了一个Image 实例，然后将onload 和onerror 事件处理程序指定为同一个函数。这样无论是什么响应，只要请求完成，就能得到通知。请求从设置src 属性那一刻开始，而这个例子在请求中发送了一个name 参数。

0x02: 作用
图像Ping 最常用于跟踪用户点击页面或动态广告曝光次数。图像Ping 有两个主要的缺点，一是只能发送GET 请求，而是无法访问服务器的响应文本。因此，图像Ping 只能用于浏览器与服务器间的简单通信。

## JSONP
0x00：定义
JSONP 是JSON with Padding（填充式JSON或参数式JSON）的简写，是应用JSON 的一种新方法。JSONP 看起来与JSON 差不多，只不过是被包含在函数调用中的JSON。
```
callback({"name": "Nicholas"})
```
JSONP 由两部分组成：回调函数和数据。回调函数是当响应到来时应该在页面中调用的函数。回调函数的名字一般是在请求中指定的。而数据就是传入回调函数中的JSON 数据。

0x01：例子
这是应该典型的JSONP 请求
```js
http://freegeoio.net/json/?callback=handleResponse
```
这个URL 是在请求应该JSONP 地理定位服务。通过查询字符串来指定JSONP 服务的回调参数，这里指定的回调函数的名字叫`handleResponse()`
JSONP 是通过动态创建`<script>` 元素来使用的，使用时可以为`src` 属性指定应该跨域URL。`<script>` 元素和`<img>` 元素都有能力不受限制地从其他域加载资源。因为JSONP 是有效的JavaScript 代码，所以在请求完成后，即在JSONP 响应加载到页面中以后，就会立即执行。
```js
function handleResponse (response) {
  alert("You're at IP address " + reponse.ip + ", which is in " +
    response.city + ", " + response.region_name)
}
var script = document.createElement('script')
script.src = 'http://freegeoio.net/json/?callback=handleResponse'
document.body.insertBefore(script, document.body.firstChild)
```

0x02: 作用
与图像Ping 相比，优点在于能够直接访问响应文本，支持在浏览器与服务器之间双向通信。
缺点：需要确保其他域的安全可靠，以及确定JSONP 请求是否失败并不容易。

## Comet
0x00：定义
Comet 指的一种更高级的Ajax 技术（也被称为“服务器推送”）。Ajax 是一种从页面向服务器请求数据的技术，而Comet 则是一种服务器向页面推送数据的技术。Comet 能够让信息近乎实时地被推送到页面上，非常适合处理体育比赛的分数和股票报价。

0x01：实现
有两种实现Comet 的方式：长轮询和流。
1.短轮询：浏览器定时向服务器发送请求，看看有没有更新的数据。下图是短轮询的时间线
{% asset_img cors_0.jpg %}
2.长轮询：页面发生器一个服务器的请求，然后服务器一直保持连接打开知道有数据可发送。发送完数据之后，浏览器关闭连接，随即由发起一个到服务器的新请求。这一过程在页面打开期间一直保持不断。下面是长轮询的时间线
{% asset_img cors_1.jpg %}
3.HTTP流：流不同于轮询，因为它在页面的整个生命周期内只使用一个HTTP 连接。具体来说，就是浏览器向服务器发送一个请求，而服务器保持连接打开，然后周期性地向浏览器发送数据。
所有服务器端语言都支持打印到输出缓存然后刷新（将输出缓存中的内容一次性全部发送到客户端）的功能。而这正是实现HTTP 流的关键所在。

在现代浏览器中，通过侦听`readstatechange` 事件以及检测`readyState` 的值是否为3，就可以利用XHR 对象实现HTTP 流。随着不断从服务器接收数据，`readState` 的值就会周期性地变为3。当`readyState` 值变为3 时，`responseText` 属性中会保存接收到的所有数据。

## 服务器发送事件
0x00：定义
SSE（Server-Sent Events，服务器发送事件）是围绕只读Comet 交互推出的API 或者模式。SSE API 用于创建到服务器的单向连接，服务器通过这个连接可以发送任意数量的数据。服务器响应的MIME 类型必须是`text/event-stream`，而且是浏览器中的JavaScript API能解析格式输出。

0x01: SSE API
SSE 的JavaScript API 与其他传递消息的JavaScript API 很相似。要预定新的事件流，首先要创建一个新的`EventSource` 对象，并传进一个入口点：
```js
var source = new EventSource('myevents.php')
```
注意，传入的URL 必须与创建对象的页面同源（相同的URL 模式、域及端口）。`EventSource` 的实例有一个`readyState` 属性，值为0 表示正连接到服务器，值为1 表示打开连接，值为2 表示关闭连接。
另外，还有以下三个事件。
1.`open`：在建立连接时触发。
2.`message`：在从服务器接收到新事件时触发。
3.`error`：无法建立连接时触发。

`onmessage` 事件处理程序的使用
```js
source.onmessage = function (event) {
  var data = event.data
  // 处理数据
}
```
服务器发回的数据以字符串形式保存在`event.data` 中。
默认情况下，`EventSource` 对象会保持与服务器的活动连接。如果连接断开，还会重新连接。这就意味着SSE 适合长轮询和HTTP 流。如果想强制立即断开连接并且不再重新连接，可以调用`close()` 方法。
```js
source.close()
```

0x02：事件流
所谓的服务器事件会通过一个持久的HTTP 响应发送，这个响应的MIME 类型为`text/event-stream`。响应的格式是纯文本，最简单的情况是每个数据项都带有前缀`data:`，例如：
```js
data: foo

data: bar

data: foo
data: bar
```
对以上响应而言，事件流中的第一个`message` 事件返回的`event.data` 值为`foo`，第二个`message` 事件返回的`event.data` 值为`bar`，第三个`message` 事件返回的`event.data` 值为`foo\nbar`（注意中间的换行符）。对于多个连续的以`data:` 开头的数据行，将作为多段数据解析，每个值之间以一个换行符分割。只能在包含`data:`的数据行后面有空行时，才会触发`message` 事件，因此在服务器上生成事件流时不能忘了多添加这一行。
通过`id:` 前缀可以给特定的事件指定一个关联的ID，这个ID 行位于`data:` 行前面或皆可：
```js
data: foo
id: 1
```
设置了ID 后，`EventSource` 对象会跟踪上一次触发的事件。如果连接断开，会向服务器发送一个包含名为`Last-Event-ID` 的特殊HTTP 头部的请求，以便服务器知道下一次该触发哪个事件。在多次连接的事件流中，这种机制可以确保浏览器以正确的顺序收到连接的数据段。

## Web Sockets
0x00：定义
Web Sockets 的目标是在一个单独的持久连接上提供全双工、双向通信。在JavaScript 中创建了Web Socket 之后，会有一个HTTP 请求发送到浏览器以发起连接。在取得服务器响应后，建立的连接会使用HTTP 升级为HTTP 协议交换为Web Socket 协议。也就是说，使用标准的HTTP 服务器无法实现Web Sockets，只有支持这种协议的专门服务器才能正常工作。
由于Web Sockets 使用了自定义的协议，所以URL 模式也略有不同。未加密的连接不再是`http://` 而是`ws://`；加密的连接也不是`https://`而是`wss://`。
使用自定义协议而非HTTP 协议的好处是，能够在客户端和服务器之间发送非常少量的数据，由于传递的数据包很小，因此Web Sockets 非常适合移动应用。

0x01：Web Sockets API
要创建Web Socket，先实例一个WebSocket 对象并传入要连接的URL：
```js
var socket = new WebSocket('ws://www.example.com/server.php')
```
注意，必须给WebSocket 构造函数传入绝对URL。同源策略对Web Sockets 不适用，因此可以通过它打开到任何站点的连接。

实例化了WebSocket 对象后，浏览器就会马上尝试连接。WebSocket 也有一个表示当前状态的`readyState` 属性。
1.WebSocket.OPENING（0）：正在建立连接。
2.WebSocket.OPEN（1）：已经建立连接。
3.WebSocket.CLOSING（2）：正在关闭连接。
4.WebSocket.CLOSE（3）：已经关闭连接。
`readyState` 的值永远从0 开始。
要关闭Web Socket 连接，可以在任何时候调用`close()` 方法。
```js
socket.close()
```
调用了`close()` 之后，`readyState` 的值立即变为2（正在关闭），而在关闭连接后就会变成3。

0x02：发送和接收数据
Web Socket 打开之后，就可以通过连接发送和接收数据。要向服务器发送数据，使用`send()` 方法并传入任意字符串，例如：
```js
var socket = new WebSocket('ws://www.example.com/server.php')
socket.send('Hello world!')
```
因为Web Socket 只能通过连接发送纯文本数据，所以对于复杂的数据结构，在通过连接发送之前，必须进行序列化。下面的例子展示了先将数据序列化为一个JSON 字符串，然后再发送到服务器：
```js
var message = {
  time: new Date(),
  text: 'Hello world!',
  clientId: 'asdfp'
}
socket.send(JSON.stringify(message))
```
接下来，服务器要读取其中的数据，就要解析接收到的JSON 字符串。
当服务器向客户端发来消息时，WebSocket 对象就会触发`message` 事件。这个`message` 事件与其他传递消息的协议类似，也是把返回的数据保存在`event.data` 属性中。
```js
socket.onmessage = function (event) {
  var data = event.data
  // 处理数据
}
```
与通过`send()` 发送到服务器的数据一样，`event.data` 中返回的数据也是字符串。如果你想要得到其他格式的数据，必须手动解析这些数据。

0x03：其他事件
WebSocket 对象还有其他三个事件，在建立生命周期的不同阶段触发。
1.open: 在成功建立连接时触发。
2.error: 在发生错误时触发，连接不能持续。
3.close: 在连接关闭时触发。
WebSocket 对象不支持DOM 2 级事件侦听器，因此必须使用DOM 0 级语法分别定义每个事件处理程序。
```js
var socket = new WebSocket('ws://www.example.com/server.php')

socket.onopen = function () {
  alert('Connection established.')
}

socket.onerror = function () {
  alert('Connection error.')
}

socket.onclose = function () {
  alert('Connection closed.')
}
```
在这三个事件中，只有`close` 事件的`event` 对象有额外的信息。这个事件的事件对象有三个额外的属性：`wasClean`、`code` 和`reason`。其中，`wasClean` 是一个布尔值，表示连接是否已经明确地关闭；`code` 是服务器返回的数值状态码；而`reason` 是一个字符串，包含服务器发回的消息。可以把这些信息显示给用户，也可以记录到日志中以便将来分析。
```js
socket.onclose = function (event) {
  console.log('Was clean? ' + event.wasClean + " Code=" + event.code + " Reason=" + event.reason)
}
```
