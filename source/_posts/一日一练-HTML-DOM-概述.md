---
title: 一日一练-HTML DOM 概述
date: 2018-12-14 08:36:41
tags:
  - 技术
  - 一日一练
  - DOM
categories: 技术
---

## 什么是DOM？
文档对象模型（Document Object Model，DOM）是一个独立于语言的，使用XML 和HTML 文档操作的应用程序接口（API）。在浏览器中，主要与HTML 文档打交道，在网页应用中检索XML 文档也很常见。DOM APIs 主要用于访问这些文档中的数据。

<!--more-->

## DOM 的实现
尽管DOM 是与语言无关的API，在浏览器中的接口却是以JavaScript 实现的。客户端大多数脚本程序与文档打交道，DOM 就成为JavaScript 代码日常行为中重要的组成部分。

浏览器通常要求DOM 实现和JavaScript 实现保持相互独立。例如，在Internet Explorer 中，被称为JScript 的JavaScript 实现位于库文件jscirpt.dll 中，而DOM 实现位于另一个库mshtml.dll（内部代码Trident）。这种分离技术允许其他技术和语言，如VBScript，受益于Trident 所提供的DOM 和渲染功能。Safari 使用WebKit 的WebCore 处理DOM 和渲染，具有一个分离的JavaScriptCore 引擎（最新版本中的绰号是SquirrelFish）。Google Chrome 也使用WebKit 的WebCore 库渲染页面，但实现了自己的JavaScript 引擎V8。在Firefox 中，JavaScript 实现采用Spider-Monkey（最新版中称为TraceMonkey），与其Gecko 渲染引擎相分离。

## 参考文档
1.《高性能JavaScript》
