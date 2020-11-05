---
title: git cherry-pick 的使用
date: 2020-11-05 17:49:52
tags:
  - Git
  - 技术
categories: 技术
comments: true
---

> 子曰：摘樱桃的使用

在工作协作开发中，使用`git` 进行版本管理，下面整理了使用`git cherry-pick` 的流程

<!--more-->

## 目的
`git cherry-pick` 命令的作用，就是将指定的提交（commit）应用于其他分支。
```
git cherry-pick <commitHash>
```

## 流程
1.代码仓库有`master` 和`feature` 两个分支。`feature` 分支上是新的功能开发。

2.将当前分支切换到`master` 分支
```js
git checkout master // 切换到
```

3.将`feature` 分支上的功能提交到`master`
```js
git cherry-pick <commitHash>
```

## 参考文章

1. [git cherry-pick 教程 ruanyifeng](http://www.ruanyifeng.com/blog/2020/04/git-cherry-pick.html)