---
title: 一日一练-浏览器 【转载】cookie 和 session
date: 2021-08-24 15:36:56
tags:
  - 技术
  - 一日一练
  - 浏览器
  - cookie
  - session
  - 转载
categories: 技术
---

本文转载自[cookie和session的详解与区别](https://www.cnblogs.com/l199616j/p/11195667.html)，有部分删减，看原文请到原地址。
　
**会话（Session）**跟踪是 Web 程序中常用的技术，用来跟踪用户的整个会话。常用的会话跟踪技术是 Cookie 与 Session。**Cookie 通过在客户端记录信息确定用户身份，Session 通过在服务器端记录信息确定用户身份。**

本章将系统地讲述 Cookie 与 Session 机制，并比较说明什么时候不能用 Cookie，什么时候不能用 Session。

<!--more-->

## Cookie 机制

在程序中，会话跟踪是很重要的事情。理论上，**一个用户的所有请求操作都应该属于同一个会话**，而另一个用户的所有请求操作则应该属于另一个会话，二者不能混淆。

而 Web 应用程序是使用 HTTP 协议传输数据的。**HTTP 协议是无状态的协议。一旦数据交换完毕，客户端与服务器端的连接就会关闭，再次交换数据需要建立新的连接。这就意味着服务器无法从连接上跟踪会话。** 要跟踪该会话，必须引入一种机制。

Cookie 就是这样的一种机制。它可以弥补 HTTP 协议无状态的不足。在 Session 出现之前，基本上所有的网站都采用 Cookie 来跟踪会话。

### 什么是 Cookie
Cookie 意为“甜饼”，是由 W3C 组织提出，最早由 Netscape 社区发展的一种机制。目前 Cookie 已经成为标准，所有的主流浏览器如 IE、Netscape、Firefox、Opera 等都支持 Cookie。

由于 HTTP 是一种无状态的协议，服务器单从网络连接上无从知道客户身份。怎么办呢？**就给客户端们颁发一个通行证吧，每人一个，无论谁访问都必须携带自己通行证。这样服务器就能从通行证上确认客户身份了**。这就是 Cookie 的工作原理。

Cookie 实际上是一小段的文本信息。客户端请求服务器，如果服务器需要记录该用户状态，就使用 response 向客户端浏览器颁发一个 Cookie。客户端浏览器会把 Cookie 保存起来。当浏览器再请求该网站时，浏览器把请求的网址连同该 Cookie 一同提交给服务器。服务器检查该 Cookie，以此来辨认用户状态。服务器还可以根据需要修改 Cookie 的内容。
{% asset_img 1.jpg %}

###  记录用户访问次数
Java 中把 Cookie 封装成了 javax.servlet.http.Cookie 类。每个 Cookie 都是该 Cookie 类的对象。服务器通过操作 Cookie 类对象对客户端 Cookie 进行操作。通过 request.getCookie() 获取客户端提交的所有 Cookie（以 Cookie[] 数组形式返回），通过 response.addCookie(Cookiecookie) 向客户端设置 Cookie。

Cookie 对象使用 key-value 属性对的形式保存用户状态，一个 Cookie 对象保存一个属性对，一个 request 或者 response 同时使用多个Cookie。

###  Cookie 的不可跨域名性
很多网站都会使用 Cookie。例如，Google 会向客户端颁发 Cookie，Baidu 也会向客户端颁发 Cookie。那浏览器访问 Google 会不会也携带上 Baidu 颁发的 Cookie 呢？或者 Google 能不能修改 Baidu 颁发的 Cookie 呢？

答案是否定的。**Cookie 具有不可跨域名性。**根据 Cookie 规范，浏览器访问 Google 只会携带 Google 的 Cookie，而不会携带 Baidu的Cookie。Google 也只能操作 Google 的 Cookie，而不能操作 Baidu 的 Cookie。

### Unicode 编码：保存中文
中文与英文字符不同，中文属于 Unicode 字符，在内存中占 4 个字符，而英文属于 ASCII 字符，内存中只占 2 个字节。Cookie 中使用Unicode 字符时需要对 Unicode 字符进行编码，否则会乱码。

### BASE64 编码：保存二进制图片
Cookie 不仅可以使用 ASCII 字符与 Unicode 字符，还可以使用二进制数据。例如在 Cookie 中使用数字证书，提供安全度。使用二进制数据时也需要进行编码。

注意：本程序仅用于展示 Cookie 中可以存储二进制内容，并不实用。由于浏览器每次请求服务器都会携带 Cookie，因此 Cookie 内容不宜过多，否则影响速度。Cookie 的内容应该少而精。

### 设置 Cookie 的所有属性

熟悉名 | 描述
---|---|---
name | 该 Cookie 的名称。Cookie 一旦创建，名称便不可更改
value | 该 Cookie 的值。如果值为 Unicode 字符，需要为字符编码。如果值为二进制数据，则需要使用 BASE64 编码
maxAge | 该 Cookie 失效的时间，单位秒。如果为正数，则该 Cookie 在 maxAge 秒之后失效。如果为负数，该 Cookie 为临时 Cookie，关闭浏览器即失效，浏览器也不会以任何形式保存该 Cookie。如果为 0，表示删除该 Cookie。默认为 –1
secure | 该 Cookie 是否仅被使用安全协议传输。安全协议。安全协议有 HTTPS，SSL 等，在网络上传输数据之前先将数据加密。默认为false
path | 该 Cookie 的使用路径。如果设置为 “/sessionWeb/”，则只有 contextPath 为 “/sessionWeb” 的程序可以访问该 Cookie。如果设置为 “/”，则本域名下 contextPath 都可以访问该 Cookie。注意最后一个字符必须为 “/”
domain | 可以访问该 Cookie 的域名。如果设置为 “.google.com”，则所有以 “google.com” 结尾的域名都可以访问该 Cookie。注意第一个字符必须为 “.”
comment | 该 Cookie 的用处说明。浏览器显示 Cookie 信息的时候显示该说明
version | 该 Cookie 使用的版本号。0 表示遵循 Netscape 的 Cookie 规范，1 表示遵循 W3C 的 RFC 2109 规范

### cookie 的域名
Cookie 是不可跨域名的。域名 www.google.com 颁发的 Cookie 不会被提交到域名 www.baidu.com。这是由 Cookie 的隐私安全机制决定的。隐私安全机制能够禁止网站非法获取其他网站的 Cookie。

正常情况下，同一个一级域名下的两个二级域名如 www.google.com 和 images.google.com 也不能交互使用 Cookie，因为二者的域名并不严格相同。如果想所有 google.com 名下的二级域名都可以使用该 Cookie，需要设置 Cookie 的 domain 参数

### cookie 的安全属性
HTTP 协议不仅是无状态的，而且是不安全的。使用 HTTP 协议的数据不经过任何加密就直接在网络上传播，有被截获的可能。使用 HTTP 协议传输很机密的内容是一种隐患。如果不希望 Cookie 在 HTTP 等非安全协议中传输，可以设置 Cookie 的 secure 属性为 true。浏览器只会在HTTPS 和 SSL 等安全协议中传输此类 Cookie。

## Session 机制
除了使用 Cookie，Web 应用程序中还经常使用 Session 来记录客户端状态。**Session 是服务器端使用的一种记录客户端状态的机制**，使用上比 Cookie 简单一些，相应的也**增加了服务器的存储压力**。

### 什么是 Session
Session 是另一种记录客户状态的机制，不同的是 Cookie 保存在客户端浏览器中，而 Session 保存在服务器上。客户端浏览器访问服务器的时候，服务器把客户端信息以某种形式记录在服务器上。这就是 Session。客户端浏览器再次访问时只需要从该 Session 中查找该客户的状态就可以了。

如果说 **Cookie 机制是通过检查客户身上的“通行证”来确定客户身份的话，那么 Session 机制就是通过检查服务器上的“客户明细表”来确认客户身份。Session 相当于程序在服务器上建立的一份客户档案，客户来访的时候只需要查询客户档案表就可以了。**

### Session 的生命周期
Session 保存在服务器端。**为了获得更高的存取速度，服务器一般把 Session 放在内存里。每个用户都会有一个独立的 Session。如果Session 内容过于复杂，当大量客户访问服务器时可能会导致内存溢出。因此，Session 里的信息应该尽量精简。**

**Session 在用户第一次访问服务器的时候自动创建。**需要注意只有访问 JSP、Servlet 等程序时才会创建 Session，只访问 HTML、IMAGE 等静态资源并不会创建 Session。

**Session 生成后，只要用户继续访问，服务器就会更新 Session 的最后访问时间，并维护该 Session。**用户每访问服务器一次，无论是否读写Session，服务器都认为该用户的 Session“活跃（active）”了一次。

 

### Session 的有效期
由于会有越来越多的用户访问服务器，因此 Session 也会越来越多。**为防止内存溢出，服务器会把长时间内没有活跃的 Session 从内存删除。这个时间就是 Session 的超时时间。如果超过了超时时间没访问过服务器，Session 就自动失效了。**

### Session 对浏览器的要求
虽然 Session 保存在服务器，对客户端是透明的，它的正常运行仍然需要客户端浏览器的支持。这是因为 Session 需要使用 Cookie 作为识别标志。HTTP 协议是无状态的，Session 不能依据 HTTP 连接来判断是否为同一客户，因此服务器向客户端浏览器发送一个名为 JSESSIONID 的Cookie，它的值为该 Session 的 id（也就是 `HttpSession.getId()` 的返回值）。Session 依据该 Cookie 来识别是否为同一用户。

该 Cookie 为服务器自动生成的，它的 maxAge 属性一般为 –1，表示仅当前浏览器内有效，并且各浏览器窗口间不共享，关闭浏览器就会失效。

因此同一机器的两个浏览器窗口访问服务器时，会生成两个不同的 Session。但是由浏览器窗口内的链接、脚本等打开的新窗口（也就是说不是双击桌面浏览器图标等打开的窗口）除外。这类子窗口会共享父窗口的 Cookie，因此会共享一个 Session。

如果客户端浏览器将 Cookie 功能禁用，或者不支持 Cookie 怎么办？例如，绝大多数的手机浏览器都不支持 Cookie。Java Web 提供了另一种解决方案：URL 地址重写。

### URL 地址重写
URL 地址重写是对客户端不支持 Cookie 的解决方案。URL 地址重写的原理是将该用户 Session 的 id 信息重写到 URL 地址中。服务器能够解析重写后的 URL 获取 Session 的 id。这样即使客户端不支持 Cookie，也可以使用 Session 来记录用户状态。HttpServletResponse 类提供了 `encodeURL(Stringurl)` 实现 URL 地址重写，

## cookie 和 session 的区别

1. cookie 数据存放在客户的浏览器上，session 数据放在服务器上。
2. cookie 不是很安全，别人可以分析存放在本地的 COOKIE 并进行 COOKIE 欺骗，考虑到安全应当使用 session。
3. 设置 cookie 时间可以使 cookie 过期。但是使用 `session-destory()`，我们将会销毁会话。
4. session 会在一定时间内保存在服务器上。当访问增多，会比较占用你服务器的性能考虑到减轻服务器性能方面，应当使用 cookie。
5. 单个 cookie 保存的数据不能超过 4K，很多浏览器都限制一个站点最多保存 20 个 cookie。(Session 对象没有对存储的数据量的限制，其中可以保存更为复杂的数据类型)