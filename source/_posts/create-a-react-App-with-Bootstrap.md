---
title: create a react App with Bootstrap
date: 2016-11-15 17:30:59
comments: true
tags:
  - 技术
  - create-react-app
  - react
  - SPA
  - Bootstrap
categories: 技术
---
## 简介
利用create-react-app、Bootstrap、react 创建SPA。

<!--more-->

## 开发流程
1. 利用create-react-app 创建模版。
2. 在项目目录下运行。
```cmd
npm start
```
3. 修改`public/index.html` 文件，利用Bootstrap 构建页面。
```
<!doctype html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link rel="shortcut icon" href="%PUBLIC_URL%/favicon.ico">
    <!-- 新 Bootstrap 核心 CSS 文件 -->
    <link rel="stylesheet" href="http://cdn.bootcss.com/bootstrap/3.3.0/css/bootstrap.min.css">
    <title>Question & Answer</title>
    <style>
      #add-question-btn {
        position: absolute;
        right: 20px;
      }
      .vote-count {
        display: block;
      }
      form[name="addQuestion"] .btn{
        margin: 20px 0 0 15px;
      }
    </style>
  </head>
  <body>
    <div class="jumbotron text-center">
      <div class="container">
        <h1>Question & Answer</h1>
        <button id="add-question-btn" class="btn btn-success">添加问题</button>
      </div>
    </div>
    <div class="media-list container" id="questions">
      <form action="" class="clearfix" name="addQuestion">
        <div class="form-group">
          <label for="qtitle">问题</label>
          <input type="text" class="form-control" id="qtitle" placeholder="您的问题标题">
        </div>
        <div class="form-group">
          <label for="qdesc" class="src-only" >问题描述</label>
          <textarea name="qdesc" id="qdesc" rows="3" class="form-control" placeholder="问题的描述"></textarea>
        </div>
        <button class="btn btn-success pull-right">确认</button>
        <button class="btn btn-default pull-right">取消</button>
      </form>
      <div class="media">
        <div class="media-left">
          <button class="btn btn-default">
            <span class="glyphicon glyphicon-chevron-up"></span>
            <span class="vote-count">10</span>
          </button>
          <button class="btn btn-default">
            <span class="glyphicon glyphicon-chevron-down"></span>
          </button>
        </div>
        <div class="media-body">
          <h4 class="media-heading">媒体标头</h4>
          <p>只是一些文本。这是一些示例文本。只是一些文本。这是一些示例文本。只是一些文本。这是一些示例文本。只是一些文本。这是一些示例文本。只是一些文本。这是一些示例文本。只是一些文本。这是一些示例文本。只是一些文本。这是一些示例文本。只是一些文本。这是一些示例文本。只是一些文本。这是一些示例文本。</p>
        </div>
      </div>
      <div class="media">
        <div class="media-left">
          <button class="btn btn-default">
            <span class="glyphicon glyphicon-chevron-up"></span>
            <span class="vote-count">10</span>
          </button>
          <button class="btn btn-default">
            <span class="glyphicon glyphicon-chevron-down"></span>
          </button>
        </div>
        <div class="media-body">
          <h4 class="media-heading">媒体标头</h4>
          <p>只是一些文本。这是一些示例文本。只是一些文本。这是一些示例文本。只是一些文本。这是一些示例文本。只是一些文本。这是一些示例文本。只是一些文本。这是一些示例文本。只是一些文本。这是一些示例文本。只是一些文本。这是一些示例文本。只是一些文本。这是一些示例文本。只是一些文本。这是一些示例文本。</p>
        </div>
      </div>
      <div class="media">
        <div class="media-left">
          <button class="btn btn-default">
            <span class="glyphicon glyphicon-chevron-up"></span>
            <span class="vote-count">10</span>
          </button>
          <button class="btn btn-default">
            <span class="glyphicon glyphicon-chevron-down"></span>
          </button>
        </div>
        <div class="media-body">
          <h4 class="media-heading">媒体标头</h4>
          <p>只是一些文本。这是一些示例文本。只是一些文本。这是一些示例文本。只是一些文本。这是一些示例文本。只是一些文本。这是一些示例文本。只是一些文本。这是一些示例文本。只是一些文本。这是一些示例文本。只是一些文本。这是一些示例文本。只是一些文本。这是一些示例文本。只是一些文本。这是一些示例文本。</p>
        </div>
      </div>
    </div>
    <!-- jQuery文件。务必在bootstrap.min.js 之前引入 -->
    <script src="http://cdn.bootcss.com/jquery/1.11.1/jquery.min.js"></script>
    <!-- 最新的 Bootstrap 核心 JavaScript 文件 -->
    <script src="http://cdn.bootcss.com/bootstrap/3.3.0/js/bootstrap.min.js"></script>
  </body>
</html>
```
4. 拆分成如下组件
	1. QuestionItem.js
	2. QuestionList.js
	3. QuestionForm.js
	4. QuestionApp.js
	5. ShowAddButton.js
5. 将上述各个组件进行简单初始化
```jsx
import React, { Component } from 'react';
import './ShowAddButton.css';

class ShowAddButton extends Component {
  render() {
    return (
      <button id="add-question-btn" className="btn btn-success">添加问题</button>
    )
  }
}

export default ShowAddButton;
```
6. 添加显示隐藏Form 表单的功能

## 参考文档
1. [模仿知乎界面的简答React Demo](https://github.com/tsrot/react-zhihu/tree/product)
