---
title: git 本地分支与远程分支的关联
date: 2018-06-20 10:59:55
tags:
  - 技术
  - git
  - JS
categories: 技术
---

> 子曰：了解协作开发

1.克隆代码
```bash
$ git clone https://github.com/master-dev.git
```
注意：这个git 路径是无效的，只是一个示例。

2.进入目录，查看所有分支
```bash
git branch --all
```
默认只有当前master分支。

3.创建本地新的分支developer 分支
```bash
git branch developer
```
此时查看分支
```bash
$ git branch --all
*master
 songzhipeng
```
这是会看到master和developer，而且master上会有一个星号。
这个时候developer是一个本地分支，远程仓库不知道它的存在。
本地分支可以不同步到远程仓库，我们可以在developer开发，然后merge到master，使用master同步代码。

4.发布developer 分支
发布developer 分支是指同步developer 分支的代码到远程服务器。
```bash
git push origin developer:developer
```

## 参考文档
1. [知乎 作者：Color](https://www.zhihu.com/question/21995370/answer/19975870)
