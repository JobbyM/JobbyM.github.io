---
title: Setup and Installation of GitBook
date: 2017-03-08 09:44:05
tags:
  - GitBook
  - 翻译
  - 技术
categories: 技术
comments: true
---

# Setup and Installation of Gitbook

GitBook  安装完毕只需要几分钟就可以了。

> 此文章是翻译[Setup and Installation of Gitbook](https://toolchain.gitbook.com/setup.html)这篇GitBook官方文档。

## GitBook.com

[GitBook.com](https://www.gitbook.com/) 是一个易于书写、出版和托管数据的解决方案。它是发布内容和协作的最简单的解决方案。

它很好的结合[GiBoook Editor](https://www.gitbook.com/editor)

<!--more-->

## Local Installation（本地安装）

### Requirements（必要条件）

安装GitBook 是简单和容易的。你的系统只需要满足下面两个要求：

* NdeJS（推荐版本为v4.0.0及以上 ）
* Windows、Linux、Unix 或者Mac OS X

### Install with NPM（使用NPM 安装）

通过**NPM**安装GitBook 是最好的方式。在终端窗口中，简单地运行下面命令去安装GitBook：

```cmd
$ npm install gitbook-cli -g
```

`gitbook-cli` 是在同一个系统上安装和使用多版本GitBook 的工具。它将自动安装需要的GitBook 版本去构建一本书。

### Create a book（创建一本书）

GitBook 可以设置一个样板书（boilerplate book）：

```cmd
$ gitbook init
```

如果你想要在一个新目录中创建一本书，你也能够实现通过运行`gitbook init ./directory`

预览并且启动服务使用：

```cmd
$ gitbook server
```

或者构建静态网站：

```cmd
$ gitbook build
```

### Install pre-releases （安装旧版本）

使用`gitbook-cli` 可以容易的下载和安装GitBook 的其它版本去测试你的书：

```cmd
$ gitbook fetch beta
```

使用`gitbook ls -remote` 列出可用的安装版本

### Debugging （调试）

你可以使用选项 `--log=debug` 和`--debug` 去获得友好的错误信息（通过堆栈跟踪）。例如：

```cmd
$ gitbook build ./ --log=debug --debug
```

## 参考文档

1. [gitbook setup](https://toolchain.gitbook.com/setup.html)
