---
title: git archive 命令打包
date: 2018-06-15 14:54:17
tags:
  - 技术
  - git
  - JS
categories: 技术
---

> 子曰：git archive命令竟然可以进行打包处理

在查看阮一峰的[常用 Git 命令清单](http://www.ruanyifeng.com/blog/2015/12/git-cheat-sheet.html) 时，发现最后一条
```bash
# 生成一个可供发布的压缩包
$ git archive
```
发现了用法，就需要在本地尝试一下。

<!--more-->

## 语法
`archive` 的语法
```
git archive [--format=<fmt>] [--list] [--prefix=<prefix>/] [<extra>]
	      [-o <file> | --output=<file>] [--worktree-attributes]
	      [--remote=<repo> [--exec=<git-upload-archive>]] <tree-ish>
	      [<path>…​]
```

## 用例
1. 在当前目录，将本地`master` 分支大包为zip 格式的`out.zip` 压缩包。
```bash
git archive --format zip --output out.zip master
```
2. 创建一个Zip存档，其中包含当前分支上最新提交的内容。请注意，输出格式是由输出文件的扩展名推断的。
```bash
git archive -o latest.zip HEAD
```

## 参考文档
1. [常用 Git 命令清单 阮一峰](http://www.ruanyifeng.com/blog/2015/12/git-cheat-sheet.html)
2. [git-archive](https://git-scm.com/docs/git-archive)
3. [git-archive 腾讯云 中文版](https://cloud.tencent.com/developer/section/1138638)
