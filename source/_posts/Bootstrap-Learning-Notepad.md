---
title: Bootstrap Learning Notepad
date: 2016-11-10 11:32:30
tags:
  - css
  - Bootstrap
  - 技术
categories: 技术
comments: true
---

## 介绍
1. Bootstrap 是最受欢迎的HTML、CSS 和JS 框架，用于开发响应式布局、异步设备优先的WEB 项目。
2. 为所有开发者、所有应用场景而设计。Bootstrap 让前端开发更快速、简单。所有开发者都能快速上手、所有设备都可以适配、所有项目都适用。

<!--more-->

## 起步
### 下载
1. 使用Bootstrap 中文网提供的免费CDN 加速服务
``` html
<!-- 新 Bootstrap 核心 CSS 文件 -->
<link rel="stylesheet" href="http://cdn.bootcss.com/bootstrap/3.3.0/css/bootstrap.min.css">
<!-- 可选的Bootstrap主题文件（一般不用引入） -->
<link rel="stylesheet" href="http://cdn.bootcss.com/bootstrap/3.3.0/css/bootstrap-theme.min.css">
<!-- jQuery文件。务必在bootstrap.min.js 之前引入 -->
<script src="http://cdn.bootcss.com/jquery/1.11.1/jquery.min.js"></script>
<!-- 最新的 Bootstrap 核心 JavaScript 文件 -->
<script src="http://cdn.bootcss.com/bootstrap/3.3.0/js/bootstrap.min.js"></script>
```

### 基本模版
1. 一个最简单的Bootstrap 页面
```html
<!DOCTYPE html>
<html lang="zh-cn">
  <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>Bootstrap 101 Template</title>

    <!-- Bootstrap -->
    <link href="css/bootstrap.min.css" rel="stylesheet">

    <!-- HTML5 shim and Respond.js for IE8 support of HTML5 elements and media queries -->
    <!-- WARNING: Respond.js doesn't work if you view the page via file:// -->
    <!--[if lt IE 9]>
      <script src="http://cdn.bootcss.com/html5shiv/3.7.2/html5shiv.min.js"></script>
      <script src="http://cdn.bootcss.com/respond.js/1.4.2/respond.min.js"></script>
    <![endif]-->
  </head>
  <body>
    <h1>你好，世界！</h1>

    <!-- jQuery (necessary for Bootstrap's JavaScript plugins) -->
    <script src="http://cdn.bootcss.com/jquery/1.11.1/jquery.min.js"></script>
    <!-- Include all compiled plugins (below), or include individual files as needed -->
    <script src="js/bootstrap.min.js"></script>
  </body>
</html>
```

## 全局CSS样式
1. 设置全局CSS 样式；基本的HTML 元素均可以通过class 设置样式并得到增强效果；还有先进的栅格系统。

### 概述
#### 布局容器
1. Bootstrap 需要为页面内容和栅格系统包裹一个`.container` 容器。我们提供了两个作此用处的类。注意，由于`padding` 等属性原因，这两种容器不能互相嵌套。
2. `.container` 类用于固定宽度并支持响应式布局的容器。
``` html
<div class="container">
  ...
</div>
```
3. `.container-fluid` 类用于100% 宽度，占据全部视口（viewport）的容器
```html
<div class="container-fluid">
  ...
</div>
```

### 栅格系统
1. Bootstrap 提供了一套响应式、移动设备优先的流式栅格系统，随着屏幕或视口（viewport）尺寸的增加，系统会自动分为12 列。

#### 简介
1. 栅格系统用于通过一系列的行（row）与列（column）的组合来创建页面布局，你的内容就可以放入这些创建好的布局中。

#### 媒体查询
1. 在栅格系统中，我们在Less 文件中使用以下媒体查询（media query）来创建关键的分界点阈值。
```html
/* 超小屏幕（手机，小于 768px） */
/* 没有任何媒体查询相关的代码，因为这在 Bootstrap 中是默认的（还记得 Bootstrap 是移动设备优先的吗？） */

/* 小屏幕（平板，大于等于 768px） */
@media (min-width: @screen-sm-min) { ... }

/* 中等屏幕（桌面显示器，大于等于 992px） */
@media (min-width: @screen-md-min) { ... }

/* 大屏幕（大桌面显示器，大于等于 1200px） */
@media (min-width: @screen-lg-min) { ... }
```
2. 我们偶尔也会在媒体查询代码中包含max-width 从而将CSS 的影响限制在更小屏幕大小之内。
```html
@media (max-width: @screen-xs-max) { ... }
@media (min-width: @screen-sm-min) and (max-width: @screen-sm-max) { ... }
@media (min-width: @screen-md-min) and (max-width: @screen-md-max) { ... }
@media (min-width: @screen-lg-min) { ... }
```

#### 栅格参数
1. 通过下表可以详细查看Bootstrap 的栅格系统是如何在多种屏幕设备上工作的。
<table border="1" cellpadding="1" cellspacing="0"><tbody>
<tr><td></td><td>超小屏幕 手机（<768px）</td><td>小屏幕 平板（>=768px）</td><td>中等屏幕 桌面显示器（>=992px）</td><td>大屏幕 大桌面显示器 （>=1200px）</td></tr><tr><td>栅格系统行为</td><td>总是水平排列</td><td colspan="3">开始是堆叠在一起的，当大于这些阈值时将变为书评排列</td></tr><tr><td>.container 最大宽度</td><td>None（自动）</td><td>750px</td><td>970px</td><td>1170px</td></tr><tr><td>类前缀</td><td>.col-xs- </td><td>.col-sm- </td><td>.col-md- </td><td>.col-lg- </td></tr><tr><td>列（column）数</td><td colspan="4">12</td></tr><tr><td>最大列（column）宽</td><td>自动 </td><td>~62px </td><td>~81px</td><td>~97px</td></tr><tr><td>槽（gutter）宽</td><td colspan="3">30px（每列左右均由15px）</td></tr><tr><td>可嵌套</td><td colspan="4">是</td></tr><tr><td>偏移（offsets）</td><td colspan="4">是</td></tr><tr><td>列排列</td><td colspan="4">是</td></tr></tbody></table>

### 排版
#### 标题
1. HTML 中的所有标题标签，`<h1>` 到`</h6>` 均可使用。另外，还提供了 `.h1` 到`.h6` 类，为的是给内联（inline）属性的文本赋予标题的样式。

#### 页面主体
1. Bootstrap 将全局`font-size` 设置为**14px**，`line-height`设置为**1.428**。这些属性直接赋予`<body>`元素和所有段落元素。另外，`<p>`（段落）还被设置等于1/2 行高（即10px）的底部外边距（margin）
```html
<p>...</p>
```
#### 内联文本元素
1. 标志文本`<mark>`
2. 被删除的文本`<del>`
3. 无用文本`<s>`
4. 插入文本`<ins>`
5. 带下划线的文本`<u>`
6. 小号文本`<small>`
  对于不需要强调的inline 或block 类型的文本，使用`<small>` 标签包裹，其内的文本将被设置为父容器字体大小的85%。标题元素中嵌套的`<small>` 元素被设置不同的`font-size`.
7. 着重`<strong>`
8. 斜体`<em>`

#### 对齐
1. 通过文本对齐`.text-left`等等类，可以简单方便的将文字重新对齐。

#### 改变大小写
1. 通过`.text-lowercase`，`.text-uppercase`，`.text-capitalize` 这几个类可以改变文本的大小写。

#### 缩略语
1. 当鼠标悬停在缩写和缩写词上时就会显示完整内容，Bootstrap 实现了对HTML 的`<abbr>` 元素的增强样式。缩略语元素带有`title`属性，外观表现为嗲有较浅的虚线框，鼠标移植上面时会变成带有“问号”的指针。如果想看完整的内容可以把鼠标悬停在缩略语上，但需要包含title 属性。

#### 地址
1. 让联系信息以最近日常使用的格式呈现。在每行结尾添加`<br>`可以保留需要的样式。
```html
    <address>
      <strong>Twitter, Inc.</strong><br>
      795 Folsom Ave, Suite 600<br>
      San Francisco, CA 94107<br>
      <abbr title="Phone">P:</abbr> (123) 456-7890
    </address>

    <address>
      <strong>Full Name</strong><br>
      <a href="mailto:#">first.last@example.com</a>
    </address>
```

#### 引用
1. 在你的文档中引用其他来源的内容
```html
    <blockquote class="blockquote-reverse">
      <p>Lorem ipsum dolor sit amet, consectetur adipiscing elit. Integer posuere erat a ante.</p>
      <footer>Someone famous in <cite title="Source Title">Source Title</cite></footer>
    </blockquote>
```

#### 列表
1. 无序列表
2. 有序列表
3. 无样式列表
4. 内联列表
5. 描述
  `.dl-horizontal` 可以让`<dl>`内的短语及其描述排列在一行。开始是像`<dl>`的默认样式堆叠在一起，随着导航条逐渐展开而排列在一行。
```html
    <dl class="dl-horizontal">
      <dt>Description lists</dt>
      <dd>A description list is perfect for defining terms.</dd>
    </dl>
    <dl class="dl-horizontal">
      <dt>Euismod</dt>
      <dd>Vestibulum id ligula porta felis euismod semper eget lacinia odio sem nec elit.
Donec id elit non mi porta gravida at eget metus.</dd>
    </dl>
    <dl class="dl-horizontal">
      <dt>Malesuada porta</dt>
      <dd>Etiam porta sem malesuada magna mollis euismod.</dd>
    </dl>
```

### 代码
#### 内联代码
1. 通过`<code>`标签包裹内联样式的代码片段。

#### 用户输入
1. 通过`<kbd>`标签标记用户通过键盘输入的内容

#### 代码块
1. 多行代码可以使用`<pre>` 标签。需要将尖括号做转义处理。

#### 变量
1. 通过`<var>` 标签标记变量

#### 程序输出
1. 通过`<samp>`标签来标记程序输出的内容

### 表格
#### 基本实例
1. 为任意`<table>` 标签添加`.table` 类可以为其赋予基本的样式--少量的内补（padding）和水平方向的分割线。
```html
<table class="table">
  ...
</table>
```
#### 条纹状表格
1. 通过 `.table-striped` 类可以给`<tbody>` 之内的每一行增加斑马条纹样式。
```html
<table class="table table-striped">
  ...
</table>
```
#### 带边框的表格
1. 添加`.table-bordered` 类为表格和其中的每个单元格增加边框
```html
<table class="table table-bordered">
  ...
</table>
```
#### 鼠标悬停
1. 通过添加`.table-hover`类可以让`<tbody>` 中的每一行对鼠标悬停状态做出响应。
```html
<table class="table table-hover">
  ...
</table>
```
#### 紧缩表格
1. 通过添加`.table-condensed` 类可以让表格更加紧凑，单元格中的内补（padding）均会减半。
```html
<table class="table table-condensed">
  ...
</table>
```
#### 状态类
1. 通过这些状态类可以我i行或单元格设置颜色。

**Class**|**描述**
---|---
`.active`| 鼠标悬停在行或单元格上时所设置的颜色
`.success`| 标识成功或积极的动作
`.info`| 标识普通的提示信息或动作            
`.warning`| 标识警告或需要用户注意              
`.danger`| 标识危险或潜在的带来负面影响的动作

```html
<!-- On rows -->
<tr class="active">...</tr>
<tr class="success">...</tr>
<tr class="warning">...</tr>
<tr class="danger">...</tr>
<tr class="info">...</tr>

<!-- On cells (`td` or `th`) -->
<tr>
  <td class="active">...</td>
  <td class="success">...</td>
  <td class="warning">...</td>
  <td class="danger">...</td>
  <td class="info">...</td>
</tr>
````
#### 响应式表格
1. 将任何`.table` 元素包裹在`.table-responsive` 元素内，即可创建响应式表格，岂会在小屏幕设备上（小于768px）水平滚动。当屏幕大于768px 宽度时，水平滚动条消失。
```html
<div class="table-responsive">
  <table class="table">
    ...
  </table>
</div>
```

### 表单
#### 基本实例
1. 单独的表单控件会被自动赋予一些全局样式。所有设置了`.form-control` 类的`<input>`、`<textarea>` 和`<select>`元素都将被默认设置为宽度属性为`width: 100%;`。将`label` 元素和前面提到的控件包裹在`.form-group` 中可以获得最好的排列。
```html
    <form>
      <div class="form-group">
        <label for="exampleInputEmail1">Email address</label>
        <input type="email" class="form-control" id="exampleInputEmail1" placeholder="Enter email">
      </div>
      <div class="form-group">
        <label for="exampleInputPassword1">Password</label>
        <input type="password" class="form-control" id="exampleInputPassword1" placeholder="Password">
      </div>
      <div class="form-group">
        <label for="exampleInputFile">File input</label>
        <input type="file" id="exampleInputFile">
        <p class="help-block">Example block-level help text here.</p>
      </div>
      <div class="checkbox">
        <label>
          <input type="checkbox"> Check me out
        </label>
      </div>
      <button type="submit" class="btn btn-default">Submit</button>
    </form>
```
#### 内联表单
1. 为`<form>` 元素添加`.form-inline` 类可使其内容左对齐并且表现为`inline-block` 级别的空间。**只适用于视口（viewport）至少在768px 宽度时（视口宽度再小的话就会是表单折叠）。**
```html
<form class="form-inline" role="form">
  <div class="form-group">
    <label class="sr-only" for="exampleInputEmail2">Email address</label>
    <input type="email" class="form-control" id="exampleInputEmail2" placeholder="Enter email">
  </div>
  <div class="form-group">
    <div class="input-group">
      <div class="input-group-addon">@</div>
      <input class="form-control" type="email" placeholder="Enter email">
    </div>
  </div>
  <div class="form-group">
    <label class="sr-only" for="exampleInputPassword2">Password</label>
    <input type="password" class="form-control" id="exampleInputPassword2" placeholder="Password">
  </div>
  <div class="checkbox">
    <label>
      <input type="checkbox"> Remember me
    </label>
  </div>
  <button type="submit" class="btn btn-default">Sign in</button>
</form>
```
#### 水平排列的表单
1. 通过为表单添加`.form-horizontal` 类，并联合使用Bootstrap 预置的栅格类，可以将`label` 标签和控件组水平并排布局。这样做将改变`.form-group` 的行为，使其表现为栅格系统中的行（row），因此就无需再额外添加`.row`了。
```html
<form class="form-horizontal" role="form">
  <div class="form-group">
    <label for="inputEmail3" class="col-sm-2 control-label">Email</label>
    <div class="col-sm-10">
      <input type="email" class="form-control" id="inputEmail3" placeholder="Email">
    </div>
  </div>
  <div class="form-group">
    <label for="inputPassword3" class="col-sm-2 control-label">Password</label>
    <div class="col-sm-10">
      <input type="password" class="form-control" id="inputPassword3" placeholder="Password">
    </div>
  </div>
  <div class="form-group">
    <div class="col-sm-offset-2 col-sm-10">
      <div class="checkbox">
        <label>
          <input type="checkbox"> Remember me
        </label>
      </div>
    </div>
  </div>
  <div class="form-group">
    <div class="col-sm-offset-2 col-sm-10">
      <button type="submit" class="btn btn-default">Sign in</button>
    </div>
  </div>
</form>
```
#### 被支持的控件
1. 表单布局实例中展示了其所支持的标准表单控件。

##### 输入框
1. 包括大部分表单控件、文本输入域控件，还支持所有HTML5 类型的输入控件：`text`、`password`、`datetime`、`datetime-local`、`date`、`month`、`time`、`week`、`number`、`email`、`url`、`search`、`tel` 和`text`。

##### 文本域
1. 支持多行文本的表单控件。可根据需要改变`rows` 属性。

##### 多选和单选框
1. 多选框（checkbox）用于选择列表中的一个或多个选项，而单选框（radio）用于从多个选项中选择一个。
2. 设置了`disable` 属性的单选或多选框都能被赋予合适的样式。对于和多选或单选框联合使用的`<label>` 标签，如果也希望将悬停于上方的鼠标设置为**禁止点击**的样式，请将`.disabled` 类赋予'.radio'、'.radio-inline'、'.checkbox'、'.checkbox-inline' 或'<fieldset>'。

##### 下拉列表（select）
1. 使用默认选项或添加`multiple` 属性可以同时显示多个选项

##### 静态控件
1. 如果需要在表单中将一行纯文本和`label` 元素放置于同一行，为`<p>` 元素添加`.form-control-static` 类即可。

##### 焦点状态
1. 我们将某些表单控件的默认`outline ` 样式移除，然后对`:focus` 状态赋予`box-shadow` 属性。

##### 禁用状态
1. 为输入框设置`disabled` 属性可以防止用户输入，并能对外观做一些修改，使其更直观。

##### 只读状态
1. 为输入框设置`readonly` 属性可以禁止用户输入，并且输入框的样式也是禁用状态。

##### 校验状态
1. Bootstrap 对表单控件的校验状态，如error、warning 和success 状态，都定义了样式。使用时，添加`.has-warning`、`.has-error` 或`.has-success` 类到这些控件的父元素即可。任何包含在此元素之内的`.control-label`、`.form-control` 和`.help-block` 元素都将接受这些校验状态的样式。

##### 控件尺寸
1. 通过`.input-lg` 类似的类可以为控件设置高度，通过`.col-lg-*` 类似的类可以为控件设置宽度

##### 辅助文本
1. 针对表单控件的“块（block）”级辅助文本。
```html
<span class="help-block">A block of help text that breaks onto a new line and may extend beyond one line.</span>
```

### 按钮
#### 预定义样式
1. 使用下面列出的类`.btn-default`，`.btn-default`， `.btn-primary`， `.btn-success`， `.btn-info`， `.btn-warning`，`.btn-danger`，`.btn-link`   可以快速创建一个带有预定义样式的按钮
```html
    <button class="btn btn-warningi">Warning</button>
```

#### 尺寸
1. 使用`.btn-lg`，`.btn-sm` 或`.btn-xs`可以获得不同尺寸的按钮。
```btn
     <button class="btn btn-default btn-lg">Large button</button>
```
2. 通过给按钮添加`.btn-block` 类可以将其拉伸至父元素100% 的宽度，而且按钮也变成了块级（block）元素

#### 激活状态
1. 当按钮处于激活状态时，其表现为被按压下去（颜色更深、边框颜色更深、内向投射阴影）。对于`<button>` 元素，是通过`:active` 状态实现的。对于`<a>` 元素，是通过`.active` 类实现的。然而，你还可以将`.active` 应用到`<button>` 上，并通过编程的方式使其处于激活状态。
```html
<button class="btn btn-warning btn-lg active">Large button</button>
```

#### 禁用状态
通过为按钮的背景设置`opacity` 属性就可以呈现出无法点击的效果。
1. button 元素：为`<button>`元素添加`disabled` 属性，使其表现出禁用状态。
2. 链接（`<a>`）元素：为基于`<a>` 元素创建的按钮添加`disabled` 类。

#### 按钮类
1. 为`<a>`、`<button>` 或`<input>` 元素应用按钮类。
```html
      <a href="" class="btn btn-default" role="button">Link</a>
      <button class="btn btn-default">Button</button>
      <input type="button" class="btn btn-default" value="Input">
      <input type="submit" class="btn btn-default" value="Submit">
```

### 图片
#### 响应式图片
1. 在Bootstrap 版本3 中，通过为图片添加 `.img-responsive` 类可以让图片支持响应式布局。其实质是为图片设置了`max-width: 100%;` 和 `height: auto` 属性，从而让图片在其父元素中更好的缩放。
```html
<img src="..." class="img-responsive" alt="Responsive image">
```

#### 图片形状
1. 通过为`<img>` 元素添加相应的类，可以让图片呈现不同的形状。
```
<img src="..." alt="..." class="img-rounded">
<img src="..." alt="..." class="img-circle">
<img src="..." alt="..." class="img-thumbnail">
```

### 辅助类

### 响应式工具
1. 为了加快对移动设备友好的页面开发工作，利用媒体查询功能并使用这些工具类可以方便的针对不同设备展示或隐藏页面内容。另外还包含了针对打印机显示或隐藏内容的工具类
2. 有针对性的使用这些工具类，从而避免为同一网站创建完全不同的版本。相反，通过使用这些工具类可以在不同设备上提供不同的展示形式。

## 参考文档
1. [Bootstrap 中文网](http://v3.bootcss.com/)
