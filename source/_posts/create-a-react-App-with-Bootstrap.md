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
{% asset_img QA_App.jpg %}
<!--more-->

### 利用create-react-app 创建模版。
```cmd
create-react-app react-QA-app
```

### 在项目目录下运行。
```cmd
npm start
```

### 修改`public/index.html` 文件，利用Bootstrap 构建页面。
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

### 拆分成如下组件
1. QuestionItem.js
2. QuestionList.js
3. QuestionForm.js
4. QuestionApp.js
5. ShowAddButton.js

### 将上述各个组件进行简单初始化
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

### 添加显示隐藏Form 表单的功能

#### 功能描述：
1. 点击“添加问题”显示表单
2. 点击表单上的“取消”按钮隐藏表单

#### 功能实现：
1. 利用`this.state` 进行实现，添加一个事件`onToggleForm`
2. QuestionApp.js 文件中进行初始化`state`，
```jsx
  constructor(props){
    super(props)
    this.state = {
      onToggleForm: false
    }
  }
```
  同时提供`onToggleForm` 方法
```jsx
  onToggleForm(){
    this.setState({
      formDisplay: !this.state.formDisplay
    })
  }
```
  在`ShowAddButton` 和`QuestionForm` 组件上添加组件属性
```jsx
<ShowAddButton onToggleForm={()=>this.onToggleForm()}/>
<QuestionForm
            formDisplay={this.state.formDisplay}
            onToggleForm={()=>this.onToggleForm()}/>
          <QuestionList />
```
3. ShowAddButton.js 也需要添加相应的属性
```jsx
<button id="add-question-btn" className="btn btn-success" onClick={()=>this.props.onToggleForm()}>添加问题</button>
```
4. QuestionForm.js 上也要添加属性
```jsx
<button className="btn btn-default pull-right" onClick={()=>this.props.onToggleForm}>取消</button>
```
  同时需要增加一个style
```jsx
    let styleObj = {
      display: this.props.formDisplay ? 'block' : 'none'
    }
    return (
      <form action="" className="clearfix" name="addQuestion" style={styleObj}>
```

### 提供默认的列表数据进行展示
1. QuestionApp.js 中提供数据`this.state.questions`，将数据传入QuestionList 组件
2. QuestionList.js 中接受`this.props.questions` 进行数据的处理
```jsx
 <QuestionItem
                key={q.id}
                questionKey={q.id}
                title={q.title}
                desc={q.description}
                voteCount={q.voteCount}/>
```
3. QuestionItem.js 中将每一个数据展示在对应的位置上

### 实现通过form 表单进行数据的添加
1. QuestionApp.js 中提供`onNewQuestion`, 将其传入QuestionForm 组件
2. QuestionForm.js 中处理onSumit 事件，在此事件方法中调用`onNewQuestion` 方法

### 实现对点赞的事件处理
1. QuestionApp.js 中提供`onVote`
```jsx
  onVote(key, newCount){
      let newQuestion = this.state.questions.map(function(v, k, q){
        if(v.id === key){
          v.voteCount = newCount;
        }
        return v;
      })

      this.setState({
        questions: newQuestion
      })
  }
```
  同时将此方法传入QuestionList 组件
```jsx
          <QuestionList
            questions={this.state.questions}
            onVote={(key, newCount)=>this.onVote(key, newCount)}/>
```
2. 在QuestionList 组件中，将其传入QuestionItem 组件
```jsx
    let questionsComp = questions.map(function(q){
      return <QuestionItem
                key={q.id}
                questionKey={q.id}
                title={q.title}
                desc={q.description}
                voteCount={q.voteCount}
                onVote={(key, newCount)=>onVote(key, newCount)}/>
    })
```
3. 在QuestionItem 组件中，在`voteUp` 方法中进行调用
```jsx
  voteUp(){
    let newCount = parseInt(this.props.voteCount + 1,10)
    this.props.onVote(this.props.questionKey, newCount)
  }
```
  在button 按钮上添加`voteUp` 事件
```jsx
          <button className="btn btn-default" onClick={()=>this.voteUp()}>
```

## 参考文档
1. [模仿知乎界面的简答React Demo](https://github.com/tsrot/react-zhihu/tree/product)
