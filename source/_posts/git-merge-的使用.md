---
title: git merge 的使用
date: 2018-07-12 14:51:31
tags:
  - Git
  - 技术
categories: 技术
comments: true
---

> 子曰：merge 使用

在工作协作开发中，使用`git` 进行版本管理，下面整理了使用`git merge` 的流程

## 流程

1.首先是在`develop` 分支上进行开发
```js
git checkout develop // 切换到
```
2.将`develop` 分支上的内容进行提交
```js
git add .
git commit
```
3.`master` 分支获取远端最新版本
```js
git fetch origin master
git reset --hard origin/master
```
4.在`develop` 分支上需要使用`git merge` 命令合并`master` 最新内容
```js
git checkout develop  // 切换到develop 分支
git merge --no-ff master // 把master的修改merge到develop。注意：建议merge的时候总是用 --no-ff 选项
```
5.将`develop` 修改提交到远端
```js
git status
git push origin develop
```

<!--more-->

## merge 时的参数
1.快速合并   只快速合并（如果有冲突就失败）
```js
--ff  --ff--only
```
2.非快速合并
```js
--no-ff   
```
3.将合并过来的分支的所有不同的提交，当做一次提交，提交过来
```js
--squash  
```

`--ff` 和`--no-ff` 的区别是，当解决完冲突后，`--no-ff`会生成一次commit
```
eg: Merge branch 'suit' into master
```
