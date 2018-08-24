---
title: '[Gulp] Gulp for Beginners'
date: 2018-08-17 08:21:10
comments: true
tags:
  - 技术
  - Gulp
  - 翻译
categories: 技术
---

> 子曰：[Gulp] Gulp for Beginners

关于本文：
原文：https://css-tricks.com/gulp-for-beginners/
作者：@[ZELL LIEW](https://css-tricks.com/author/zellwk/)

[Gulp](http://gulpjs.com/)是一个工具，可以帮助你完成Web 开发的几项任务。它通常用于执行前端任务，例如：

* 运行Web 服务器
* 保存文件时自动重新加载浏览器
* 使用Sass 或LESS 等预处理器
* 优化CSS，JavaScript 和图像等资源

<!--more-->

除此之外，你甚至可以用Gulp 构建一个静态站点生成器（我已经完成了！）。所以，Gulp非常强大，但是如果你想创建自己的定制构建过程，你需要学习如何使用Gulp。

这就是本文的目的。它可以让你上手Gulp 的基础知识，之后自己可以深入。

在了解Gulp 之前，先谈谈为什么你需要使用Gulp 而不是其他类似的工具。

## 为什么是Gulp？（Why Gulp?）
像Gulp 这样的工具通常被称为“构建工具（build tools）”，因为它们是运行构建网站任务的工具。目前最流行的两种构建工具是Gulp 和[Grunt](http://gruntjs.com/)。（Chris [有一篇关于Grunt入门的帖子]）(https://24ways.org/2013/grunt-is-not-weird-and-hard/)。[Broccoli](https://github.com/broccolijs/broccoli) 专注于资源编译，这是最常见的构建工具任务之一。

已经有多篇文章介绍了Grunt 和Gulp 之间的区别，以及为什么使用一个而不是另一个的原因。如果你想要了解更多信息，可以查看[这篇](http://www.zell-weekeat.com/choosing-a-build-tool/)以及[这篇](http://sixrevisions.com/web-development/grunt-vs-gulp/)文章。 [Brunch](http://brunch.io/) 同样关注于资源，它打包一些常见的任务，如服务器和文件观察器（file watcher）。

区别在于你如何使用它们来配置工作流程。与Grunt 相比，Gulp 配置往往更短更简单。Gulp也往往跑得更快。

让我们继续讨论如何使用Gulp 设置工作流程。

## 我们正在建立什么（What we're setting up）
到本文结束时，你将拥有一个完成在本文开头的任务工作流：

* 运行Web 服务器
* 将Sass 编译为CSS
* 保存文件时自动刷新浏览器
* 优化生产的所有资源（CSS，JS，字体和图像）

还将学习如何将不同的任务链接到易于理解和执行的简单命令中。

首先安装Gulp。

## 安装Gulp（Installing Gulp）
在安装Gulp之前，需要安装Node.js（Node）。

如果尚未安装Node，可以通过从[Node 官网](https://nodejs.org/) 下载安装。

完成安装Node 后，可以在命令行中使用以下命令安装Gulp：
```bash
npm install gulp -g
```

使用的`npm install` 命令将Gulp 安装到计算机上。

此命令中的`-g` 标志告诉`npm` 将Gulp 全局安装到计算机上，这允许你在系统的任何位置使用gulp 命令。

Mac 用户在命令中需要额外的`sudo` 关键字，因为需要管理员权限才能全局安装Gulp。

现在已经安装了Gulp，接下来创建一个使用Gulp 的项目。

## 创建Gulp 项目（Creating a Gulp Project）
首先，我们将创建一个名为`project` 的文件夹作为项目根目录。 从该目录中运行`npm init` 命令：
```bash
# ... from within our project folder
npm init
```

`npm init` 命令为项目创建一个`package.json` 文件，该文件存储有关项目的信息，例如项目中使用的依赖项（Gulp 是依赖项的示例）。

`npm init` 会提示你：
```
{
  "name": "project",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC"
}
```

创建`package.json` 文件后，我们可以使用以下命令将Gulp 安装到项目中：
```bash
npm install gulp --save-dev
```

这次，我们将Gulp 安装到`project` 中而不是全局安装，这就是为什么命令存在一些差异。

`sudo` 关键字不是必需的，因为我们没有全局安装Gulp，所以`-g` 也没有必要。我们添加了`--save-dev`，它告诉计算机在`package.json` 中添加`gulp` 作为开发依赖项。
```
{
  "name": "project",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "gulp": "^3.9.1"
  }
}
```

如果在命令执行完毕后检查项目文件夹，则应该看到Gulp已创建`node_modules` 文件夹。还应该在`node_modules` 中看到`gulp` 文件夹。
{% asset_img node-modules.png %}

我们已经准备好开始使用Gulp 工作了。在我们这样做之前，我们必须清楚将如何使用Gulp，其中一部分是确定目录结构。

## 确定文件夹结构（Determining Folder Structor）
Gulp 足够灵活，可以处理任何文件夹结构。在为项目调整之前，你只需了解内部工作原理。

对于本文，我们将使用通用webapp 的结构：
```
|- app/
----|- css/
----|- fonts/
----|- images/
----|- index.html
----|- js/
----|- scss/
|- dist/
|- gulpfile.js
|- node_modules/
|- package.json
```

在此结构中，我们将在`app` 文件夹进行开发，而`dist`（“发行（distribution）”）文件夹用于包含生产环境的优化文件。

`app` 用于开发目的，所有的代码都将放在`app` 中。

在我们处理Gulp 配置时，我们必须牢记这个文件夹结构。现在，让我们首先在`gulpfile.js` 中创建第一个Gulp任务，该文件存储所有Gulp配置。

## 编写你的第一个Gulp 任务
使用Gulp的第一步是在gulpfile 中引入（使用`require`）。
```js
var gulp = require('gulp')
```

`require` 语句告诉Node在`node_modules` 文件夹中查找名为`gulp`的包。找到包后，我们将其内容赋值给变量`gulp`。

我们现在可以开始使用这个`gulp` 变量编写gulp任务了。gulp任务的基本语法是：
```js
gulp.task('task-name', function () {
  // Stuff here
})
```

`task-name` 是指任务的名称，只要你想在Gulp 中运行任务，就可以使用该名称。你可以在命令行中编写`gulp task-name` 运行任务。

为了测试它，让我们创建一个输出`Hello Zell！`的`hello` 任务。
```js
gulp.task('hello', function () {
  console.log('Hello Zell')
})
```

我们可以在命令行中使用`gulp hello` 运行此任务。
```bash
gulp hello
```

命令行将输出`Hello Zell！`的日志。
{% asset_img gulp_0.jpg %}

Gulp任务通常比这更复杂。它通常包含两个额外的Gulp 方法，以及各种Gulp 插件。

这是一个真正的任务可能的样子：
```js
gulp.task('task-name', function () {
  return gulp.src('source-files') // Get source files with gulp.src
    .pipe(aGulpPlugin()) // Sends it through a gulp plugin
    .pipe(gulp.dest('destination')) // Outputs the file in the destination folder
})
```

一个真正的任务需要两个额外的gulp方法--`gulp.src` 和`gulp.dest`。

`gulp.src` 告诉Gulp 任务用于任务的文件，而`gulp.dest` 告诉Gulp任务完成后输出文件的位置。

让我们尝试构建一个真正的任务，我们将Sass文件编译成CSS文件。

## 使用Gulp 进行预处理（Preprocessing with Gulp）
使用[gulp-sass](https://www.npmjs.com/package/gulp-sass)插件将Sass编译为CSS。可以像安装gulp 一样使用`npm install` 命令将`gulp-sass`安装到项目中。

使用`--save-dev` 标志来确保`gulp-sass` 被添加到`package.json`中的`devDependencies`。
```bash
npm install gulp-sass --save-dev
```

我们使用`require` 从`node_modules` 文件夹中引入`gulp-sass`，就像我们使用`gulp`一样，然后才能使用插件。
```js
var gulp =  require('gulp')
// Requires the gulp-sasss plugin
var sass = require('gulp-sass')
```

我们可以通过用`sass()` 替换`aGulpPlugin()` 来使用`gulp-sass`。由于任务是为了将Sass 编译成CSS，所以我们将其命名为`sass`。
```js
gulp.task('sass', function () {
  return gulp.src('source-files')
    .pipe(sass()) // Using gulp-sass
    .pipe(gulp.dest('destination'))
})
```

我们需要为`sass` 任务提供源文件和目标文件，在`app/scss` 文件夹中创建一个`styles.scss` 文件。该文件将添加到`gulp.src` 中的`sass` 任务中。

我们想将最终的`styles.css` 文件输出到`app/css` 文件夹，该文件夹是`destination` 的`gulp.dest`。
```js
gulp.task('sass', function () {
  return gulp.src('app/scess/styles.scss')
    .pipe(sass()) // Converts Sass to CSS with glup-sasss
    .pipe(gulp.dest('app/css'))
})
```

我们想要测试`sass` 任务是否按照我们的期望运行。为此，我们可以在`styles.scss` 中添加Sass 函数。
```scss
// styles.scss
.testing {
  width: percentage(5/7);
}
```

如果在命令行中运行`gulp sass`，现在应该能够看到在`app/css` 中创建了`styles.css` 文件。此外，代码中的`percentage(5/7)` 被计算为`71.42857％`。
```css
/* styles.css */
.testing {
  width: 71.42857%;
}
```

这就是`sass` 任务的作用！

有时我们需要能够同时将多个`.scss` 文件编译到CSS中。我们可以在Node globs 的帮助下完成。

FYI：Gulp-sass 使用LibSass 将Sass 转换为CSS。它比基于Ruby 的方法快得多。你仍然可以使用[gulp-ruby-sass](https://github.com/sindresorhus/gulp-ruby-sass)或[gulp-compass](https://www.npmjs.com/package/gulp-compass) 来使用Gulp 的Ruby 方法。

## Node 的Glob（Globbing in Node）
Glob 是匹配文件的模式，允许你将多个文件添加到`gulp.src` 中。它就像正则表达式，但专门用于文件路径。

使用glob 时，计算机会检查指定模式的文件名和路径。如果模式存在，则匹配文件。

使用Gulp 的大多数工作流程往往只需要4 种不同的通配模式：

1.`*.scss`：`*` 模式是一个匹配当前目录中任何模式的通配符。在这种情况下，我们匹配根文件夹（`project`）中以`.scss` 结尾的任何文件。
2.`**/*.scss`：这是`*` 模式的一个更极端的版本，它匹配根文件夹和任何子目录中以`.scss` 结尾的任何文件。
3.`!not-me.scss`：`!` 表示Gulp 应该从匹配中排除模式，如果必须从匹配模式中排除文件，这将非常有用。在这种情况下，`not-me.scss` 将从匹配中排除。
4.`*.+(scss|sass)`：加号`+` 和括号`()` 允许Gulp 匹配多个模式，不同的模式由管道`|` 符分隔。在这种情况下，Gulp 将匹配根文件夹中以`.scss` 或`.sass` 结尾的任何文件。

现在我们知道glob，我们可以用`scss/**/*.scss` 模式替换`app/scss/ styles.scss`，它匹配`app/scss` 中具有`.scss`扩展名的任何文件或子目录。
```js
gulp.task('sass', function () {
  return gulp.src('app/scss/**/*.scss') // Gets all files ending with .scss in app/scss and children dirs
    .pipe(sass())
    .pipe(gulp.dest('app/css'))
})
```

在`app/scss` 中找到的任何其他Sass 文件将自动包含在具有此更改的`sass` 任务中。如果将`print.scss` 文件添加到项目中，`print.css` 将在`app/css` 中生成。
{% asset_img second-stylesheet.png %}

现在我们用一个命令将所有Sass 文件编译成CSS 文件。问题是，我们每次想要将Sass 编译成CSS 时必须手动运行`gulp sass`。

幸运的是，只要通过名为“watching” 的过程保存文件，我们就可以告诉Gulp 自动运行`sass` 任务。

## 监视Sass 文件的变化
Gulp 为我们提供了一种监视`watch` 方法，用于检查文件是否已保存。`watch` 方法的语法是：
```js
// Gulp watch syntax
gulp.watch('files-to-watch', ['tasks', 'to', 'run'])
```

如果我们想要保存所有Sass 文件并在保存Sass 文件时运行`sass` 任务，我们只需要用`app/scss/**/*.scss` 替换`files-to-watch`，和`['sass']` 替换`['tasks', 'to', 'run']`：
```js
// Gulp watch syntax
gulp.watch('app/scss/**/*.scss', ['sass'])
```

但更常见的是，我们希望同时监视多种类型的文件。为此，我们可以将多个监视进程组合到一个监视`watch` 任务中：
```js
gulp.task('watch', function () {
  gulp.watch('app/scss/**/*.scss', ['sass'])
  // Other watchers
})
```

如果你现在运行`gulp watch` 命令，你会看到Gulp 立即开始监视。
{% asset_img gulp-watch-start.png %}

并且每当你保存`.scss` 文件时它都会自动运行`sass` 任务。
{% asset_img watch-compile.gif %}

让我们更进一步，只要我们在[浏览器同步（Browser Sync）](http://www.browsersync.io/) 的帮助下保存`.scss` 文件，就让Gulp 重新加载浏览器。

## 使用浏览器同步实时重新加载（Live-reloading with Browser Sync）
Browser Sync 可帮助我们轻松实现实时重新加载的Web 服务器，有助于简化Web 开发。 它还具有其他功能，例如[跨多个设备同步操作](http://addyosmani.com/blog/browser-sync/)。

我们首先要安装Browser Sync：
```bash
npm install browser-sync --save-dev
```

你可能已经注意到我们安装Browser Sync 时没有`gulp-` 前缀。这是因为Browser Sync 与Gulp 一起使用，因此我们不必使用插件。

我们使用`require` 引入，使用Browser Sync。
```js
var browserSync = require('browser-sync').create()
```

我们需要创建一个`browserSync` 任务，以使Gulp 能够使用Browser Sync 启动服务器。由于我们正在运行服务器，因此我们需要让Browser Sync 了解服务器根目录的位置。在我们的例子中，它是`app`文件夹：
```js
gulp.task('browserSync', function () {
  browserSync.init({
    server: {
      baseDir: 'app'
    }
  })
})
```

我们还必须稍微改变`sass` 任务，以便每当运行sass 任务时，Browser Sync 都可以将新的CSS 样式（更新CSS）注入浏览器。
```js
gulp.task('sass', function () {
  return gulp.src('app/scss/**/*.scss') // // Gets all files ending with .scss in app/scss
    .pipe(sass())
    .pipe(gulp.dest('app/css'))
    .pipe(browserSync.reload({
      stream: true
    }))
})
```

我们已完成配置Browser Sync。现在，我们必须同时运行`watch` 和`browserSync` 任务才能进行实时重新加载。

打开两个命令行窗口并分别运行`gulp browserSync` 和`gulp watch` 会很麻烦，所以让我们让Gulp 一起运行它们，告诉`watch` 任务必须在允许`watch` 运行之前完成`browserSync`。

我们可以通过向`watch` 任务添加第二个参数来实现。语法是：
```js
gulp.task('watch', ['array', 'of', 'tasks', 'to', 'complete', 'before', 'watch'], function () {

})
```

这个例子中，我们正在添加`browserSync` 任务。
```js
gulp.task('watch', ['browserSync'], function () {
  gulp.task('app/scss/**/*.scss', ['sass'])
  // Other watchers
})
```

我们还希望在`watch` 之前确保`sass` 运行，因此每当我们运行Gulp 命令时CSS 就已经是最新的。
```js
gulp.task('watch', ['browserSync, 'sass''], function () {
  gulp.task('app/scss/**/*.scss', ['sass'])
  // Other watchers
})
```

现在，如果你在命令行中运行`gulp watch`，Gulp 应该同时启动`sass` 和`browserSync` 任务。完成这两项任务后，`watch` 将会运行。
{% asset_img bs-watch.png %}

同时，还会弹出一个指向`app/index.html` 的浏览器窗口。如果更改`styles.scss` 文件，浏览器自动重新加载。
{% asset_img bs-change-bg.gif %}

在我们结束这个实时重新加载部分之前还有一件事。 既然我们已经在监视要重新加载的`.scss`文件了，为什么不进一步，当保存了任何HTML 或JavaScript 文件时，重新加载浏览器？

我们可以通过添加两个监视进程，并在保存文件时调用`browserSync.reload` 函数来实现：
```js
gulp.task('watch', ['browserSync, 'sass''], function () {
  gulp.task('app/scss/**/*.scss', ['sass'])
  // Reloads the browser whenever HTML or JS files change
  gulp.task('app/*.html', browserSync.reload)
  gulp.task('app/js/**/*.js', browserSync.reload)
})
```

到目前为止，在本教程中我们已经处理了三件事：

1.启动Web 服务器来开发
2.使用Sass 预处理器
3.保存文件时重新加载浏览器

让我们在下一节中介绍优化资源的部分。我们将从优化CSS 和JavaScript 文件开始。

## 优化CSS 和JavaScript 文件（Optimizing CSS and JavaScript files）
当我们尝试优化用于生产的CSS 和JavaScript 文件时，开发人员需要执行两项任务：压缩和连接。

开发人员在自动执行此过程时遇到的一个问题是难以以正确的顺序连接脚本。

假设我们在`index.html` 中包含了3个脚本标记。
```html
<body>
    <!-- other stuff -->
    <script src="js/lib/a-library.js"></script>
    <script src="js/lib/another-library.js"></script>
    <script src="js/main.js"></script>
</body>
```

这些脚本位于两个不同的目录中。使用[gulp-concatenate](https://www.npmjs.com/package/gulp-concat)等传统插件将它们连接起来很困难。

值得庆幸的是，有一个有用的Gulp 插件，[gulp-useref](https://www.npmjs.com/package/gulp-useref) 解决了这个问题。

通过查找以`<!-- build:` 开头并以`<!--endbuild->` 结尾的注释，Gulp-useref将任意数量的CSS 和JavaScript 文件连接到一个文件中。它的语法是：
```html
<!-- bulid:<type> <path> -->
... HTML Markup, list of script / link tags.
<!-- endbuild -->
```

`<type>`可以是`js`，`css` 或`remove`。最好将`type` 设置为你尝试连接的文件类型。如果将`type` 设置为`remove`，Gulp 将删除整个构建块而不生成文件。

`<path>` 这里指的是生成的文件的目标路径。

我们希望在`js` 文件夹中生成最终的JavaScript文件，如`main.min.js`。因此，标记将是：
```html
<!-- bulid:js js/main.min.js -->
<script src="js/lib/a-library.js"></script>
<script src="js/lib/another-library.js"></script>
<script src="js/main.js"></script>
<!-- endbuild -->
```

现在让我们在gulpfile 中配置gulp-useref 插件。我们必须安装插件并在gulpfile 中引入它。
```bash
npm install gulp-useref --save-dev
```
```js
var useref = require('gulp-useref')
```

设置`useref` 任务与我们之前完成的其他任务类似。这是代码：
```js
gulp.task('useref', function () {
  return gulp.src('app/*.html')
    .pipe(useref())
    .pipe(gulp.dest('dist'))
})
```

现在，如果运行此`useref` 任务，Gulp 将运行3个脚本标记并将它们连接到`dist/js/main.min.js`。
{% asset_img main-min.png %}

但是，目前该文件尚未压缩（minified）。我们必须使用[gulp-uglify](https://www.npmjs.com/package/gulp-uglify) 插件来帮助压缩JavaScript 文件。我们还需要一个名为[gulp-if](https://github.com/robrich/gulp-if) 的第二个插件，以确保我们只尝试压缩JavaScript 文件。
```bash
npm install gulp-uglify --save-dev
```
```js
// Other require...
var uglify = require('gulp-uglify')
var gulpIf = require('gulp-if')

gulp.task('useref', function () {
  return gulp.src('app/*.html')
    .pipe(useref())
    // Minifies only if it's a JavaScript file
    .pipe(gulpIf('*.js', uglify()))
    .pipe(gulp.dest('dist'))
})
```

每当你运行`useref` 任务时，Gulp现在应该自动压缩`main.min.js` 文件。

我还没有用Gulp-useref 揭示的一个简洁的事情就是它会自动将`<!-- build:` 和`<!--endbuild->` 中的所有脚本更改为一个指向`js/main.min.js` 的单个JavaScript 文件。
{% asset_img useref-html.png %}

很棒，不是吗？

我们也可以使用相同的方法来连接任何CSS 文件（如果你决定添加多个CSS 文件）。我们将遵循相同的过程并添加`build` 注释。
```html
<!-- build:css css/styles.min.css -->
<link rel="stylesheet" href="css/styles.css">
<link rel="stylesheet" href="css/another-stylesheet.css">
<!-- endbuild -->
```

我们也可以压缩连接的CSS 文件。我们需要使用一个名为[gulp-cssnano](https://www.npmjs.com/package/gulp-cssnano) 插件来帮助我们进行压缩。
```bash
npm install gulp-cssnano
```
```js
var cssnano = require('gulp-cssnano')

gulp.task('useref', function () {
  return gulp.src('app/*.html')
    .pipe(useref())
    .pipe(gulpIf('*.js', uglify()))
    // Minifies only if it's a CSS file
    .pipe(gulpIf('*.css', cssnano()))
    .pipe(gulp.dest('dist'))
})
```

现在，每当运行`useref` 任务时，你将获得一个优化的CSS 文件和一个优化的JavaScript 文件。

让我们继续前进并优化图像。

## 优化图片（Optimizing Images）
你现在可能已经猜到了; 我们需要使用[gulp-imagemin](https://www.npmjs.com/package/gulp-imagemin) 来帮助我们优化图像。
```bash
npm install gulp-imagemin --save-dev
```
```js
var imagemin = require('gulp-imagemin')
```

我们可以在`gulp-imagemin` 的帮助下压缩`png`，`jpg`，`gif`甚至`svg`。让我们为此优化过程创建一个`images` 任务。
```js
gulp.task('images', function () {
  return gulp.src('app/images/**/*.+(png|jpg|gif|svg)')
    .pipe(imagemin())
    .pipe(gulp.dest('dist/images'))
})
```

由于可以不同地优化不同的文件类型，你可能希望向`imagemin` 添加选项以自定义每个文件的优化方式。

例如，你可以通过将隔行扫描选项键设置`interlaced` 为`true` 来创建[interlaced](interlaced) GIF。
```js
gulp.task('images', function () {
  return gulp.src('app/images/**/*.+(png|jpg|jpeg|gif|svg)')
    .pipe(imagemin({
      // Setting interlaced to true
      interlaced: true
    }))
    .pipe(gulp.dest('dist/images'))
})
```

只要你愿意，也可以使用其他选项。

然而，优化图像是一个非常缓慢的过程，除非必要，否则你不想重复。为此，我们可以使用[gulp-cache](https://www.npmjs.com/package/gulp-cache) 插件。
```bash
npm install gulp-cache --save-dev
```
```js
var cache = require('gulp-cache')

gulp.task('images', function () {
  return gulp.src('app/images/**/*.+(png|jpg|jpeg|gif|svg)')
    // Caching images that ran through imagemin
    .pipe(cache(imagemin({
      interlaced: true
    })))
    .pipe(gulp.dest('dist/images'))
})
```

我们差不多完成了优化过程。 还有一个文件夹`fonts` 目录，需要从`app`目录转移到`dist`。我们现在就这样做。

## 复制Fonts 到Dist（Copying Fonts to Dist）
由于字体文件已经过优化，我们不需要做任何事情。我们所要做的就是将字体复制到`dist` 中。

我们可以使用Gulp 简单地通过指定`gulp.src` 和`gulp.dest` 来复制文件，而无需任何插件。
```js
gulp.task('fonts', function () {
  retur gulp.src('app/fonts/**/*')
    .pipe(gulp.dest('dist/fonts'))
})
```

现在，无论何时运行`gulp fonts`，Gulp 都会将`fonts` 从`app` 复制到`dist`。
{% asset_img font-copy.png %}

我们现在在gulpfile 中有6 个不同的任务，并且每个任务都必须使用命令行单独调用，这有点麻烦，所以我们希望将所有内容组合成一个命令。

在我们这样做之前，让我们看一下如何自动清理生成的文件。

## 自动清理生成的文件（Cleaning up generated files automatically）
由于我们是自动生成文件，因此我们需要确保不再使用的文件在我们不知情的情况下不会保留。

此过程称为清理（或简称为删除文件）。

我们必须使用[del](https://www.npmjs.com/package/del) 来帮助我们进行清理。
```bash
npm install del --save-dev
```
```js
var del = require('del')
```

`del` 函数接收一个node glob（通配符）数组，告诉它要删除哪些文件夹。

使用Gulp 任务进行设置几乎就像我们的第一个“hello”示例。
```js
gulp.task('clean:dist', function () {
  retur del.sync('dist')
})
```

现在每当`gulp clean:dist` 运行时，Gulp 会为你删除`dist` 文件夹。

注意：我们不必担心删除`dist/images` 文件夹，因为`gulp-cache` 已经在本地系统上存储了图像的缓存。

要清除本地系统的缓存，可以创建一个名为`cache:clear` 的单独任务

```js
gulp.task('cache:clear', function (callback) {
  return cache.clearAll(callback)
})
```

哎呀，有点绕嘴。让我们把所有的任务结合起来吧！

## 组合Gulp 任务（Combining Gulp tasks）
让我们总结一下我们做了什么。到目前为止，我们已经创建了两组不同的Gulp 任务。

第一组用于开发过程，我们将Sass 编译为CSS，监视更改，并相应地重新加载浏览器。

第二组用于优化过程，我们为生产网站准备好所有文件。我们在此过程中优化了CSS，JavaScript 和图像等资源，并将字体从`app` 复制到`dist`。

我们已经使用`gulp watch` 命令将第一组任务组合成一个简单的工作流程：
```js
gulp.task('watch', ['browserSync', 'sass'], function () {
  // ... watchers
})
```

第二组包含我们为创建生产网站而需要运行的任务。这包括`clean:dist`，`sass`，`useref`，`images`和`fonts`。

如果我们按照同样的思路，我们可以创建一个`build` 任务，将所有内容组合在一起。
```js
gulp.task('build', [`clean`, `sass`, `useref`, `images`, `fonts`], function () {
  console.log('Building files')
})
```

不幸的是，我们无法以这种方式编写`build` 任务，因为Gulp 同时激活第二个参数中的所有任务。

`useref`, `images` 甚至`fonts` 都有可能在`clean` 之前完成，这意味着整个`dist`文件夹被删除。

因此，为了确保在其余任务之前完成清理，我们需要使用一个名为[Run Sequence](https://www.npmjs.com/package/run-sequence) 的额外插件。
```bash
npm install run-sequence --save-dev
```

以下是使用`run sequence` 的语法：
```js
var runSequence = require('run-sequence')

gulp.task('task-name', function (callback) {
  runSequence('task-one', 'task-two', 'task-three', callback)
})
```

当调用`task-name` 时，Gulp 将首先运行`task-one`。当`task-one` 完成时，Gulp将自动启动`task-two`。最后，当`task-two` 完成时，Gulp 将运行`task-three`。

如果将任务放在数组中，Run Sequence 还允许同时运行任务：
```js
gulp.task('task-name', function (callback) {
  runSequence('task-one', ['tasks', 'two', 'run', 'in', 'parallel'], 'task-three', callback)
})
```

在这种情况下，Gulp首先运行`task-one`。当`task-one` 完成时，Gulp 同时运行第二个参数中的每个任务。必须在运行`task-three` 之前完成第二个参数中的所有任务。

所以我们现在可以创建一个任务来确保`clean:dist` 先运行，然后是所有其他任务：
```js
gulp.task('build', function (callback) {
  runSequence('clean:dist',
    ['sass', 'useref', 'images', 'fonts'],
    callback
  )
})
```

为了保持一致，我们还可以与第一组建立相同的序列。我们这次使用`default` 作为任务名称：
```js
gulp.task('default', function (callback) {
  runSequence(['sass', 'browserSync', 'watch'],
    callback
  )
})
```

为何`default`？ 因为当你有一个名为`default` 的任务时，只需输入`gulp` 命令就可以运行它，这样可以节省一些输入。

最后，这是我们所做的所有工作的[github repo](https://github.com/zellwk/gulp-starter-csstricks)！

## 打包（Wrapping it up）
我们已经完成了Gulp 的基础知识，并创建了一个工作流程，能够将Sass 编译成CSS，同时同时监视HTML 和JS 文件的变化。我们可以在命令行中使用`gulp` 命令运行此任务。

我们还构建了第二个任务`build`，它为生产网站创建了一个`dist` 文件夹。我们将Sass 编译成CSS，优化了所有资源，并将必要的文件夹复制到`dist` 文件夹中。要运行此任务，我们只需在命令行中键入`gulp build`。

最后，我们有一个`clean` 的任务，从生成的`dist` 文件夹中清除所创建的任何图像缓存，允许我们删除任何无意中保留在`dist` 中的旧文件。

到目前为止，我们已经创建了一个强大的工作流，足以满足大多数Web 开发人员的需求。 Gulp 和工作流程还有很多，我们可以探索，以使这个过程更好。以下是一些想法：

用于开发：

* 使用[Autoprefixer](https://www.npmjs.com/package/gulp-autoprefixer) 编写无前缀（vendor-free）的CSS 代码
* 添加[Sourcemaps](https://www.npmjs.com/package/gulp-sourcemaps) 以便于调试
* 用[sprity](https://www.npmjs.com/package/sprity) 创造精灵图
* 使用[gulp-changed](https://www.npmjs.com/package/gulp-changed) 仅编译已更改的文件
* 用[Babel](https://www.npmjs.com/package/gulp-babel) 或[Traceur](https://www.npmjs.com/package/gulp-traceur) 编写ES6
* 使用[Browserify](http://browserify.org/)，[webpack](https://github.com/webpack/webpack) 或[jspm](https://github.com/jspm) 模块化Javascript 文件
* 使用[Handlebars](https://www.npmjs.com/package/gulp-handlebars) 或[Swig](https://www.npmjs.com/package/gulp-swig) 等模板引擎模块化HTML
* 使用[require-dir](https://www.npmjs.com/package/require-directory) 将gulpfile 拆分为较小的文件
* 使用[gulp-modernizr](https://www.npmjs.com/package/gulp-modernizr) 自动生成Modernizr 脚本

为了优化：

* 使用[unCSS](https://www.npmjs.com/package/gulp-uncss) 删除未使用的CSS
* 使用[CSSO](https://www.npmjs.com/package/gulp-csso) 进一步优化CSS
* 使用[Critical](https://www.npmjs.com/package/critical) 生成内联CSS以提高性能

除了开发或优化过程之外，还可以使用[gulp-jasmine](https://www.npmjs.com/package/gulp-jasmine) 添加编写JavaScript单元测试，甚至可以使用[gulp-rync](https://www.npmjs.com/package/gulp-rsync) 自动将`dist` 文件夹部署到生产服务器上。

正如您所看到的，即使我们创建的工作流程做了很多事情，但还有很多工作要做。创建一个适合您需求的工作流程可能会非常令人兴奋和充实，但如果你是新手，可能会有很多需要考虑的因素。

Gulp 还有很多内容，不可能在这篇博文中，甚至是一系列帖子中进行报道。这就是为什么我写了一本关于[自动化你的工作流程（automating your workflow）](https://automateyourworkflow.com/)的书，如果你有兴趣了解更多信息，[我邀请你免费获取10章](https://automateyourworkflow.com/):)

请在下面的评论中告诉我您对本文的看法！哦，是的，如果你对工作流程有任何疑问，请随时[与我联系](http://www.zell-weekeat.com/contact)。我很乐意回复！
