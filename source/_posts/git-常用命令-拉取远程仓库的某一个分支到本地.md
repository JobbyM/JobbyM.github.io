---
title: git 常用命令 拉取远程仓库的某一个分支到本地
date: 2021-02-07 10:14:31
tags:
  - Git
  - 技术
categories: 技术
comments: true
---

> 子曰：git 常用命令 拉取远程仓库的某一个分支到本地

在工作协作开发中，使用`git` 进行版本管理，下面整理了拉取远程仓库的某一个分支到本地

<!--more-->

## 本地有分支
1. 本地项目中有其他分支`master`
```bash
git branch
--master
```

2. 需要拉取远端的`dev` 分支
```bash
git checkout -b 本地分支名  origin/远端分支名
git checkout -b dev origin/dev
```

## 本地没有分支
1. 本地没有远端的`dev` 分支
```
git clone -b 远程分支名  仓库地址
git clone -b dev https://github.com/JobbyM/JobbyM.github.io.git
```

