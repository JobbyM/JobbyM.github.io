---
title: git stash 的使用
date: 2018-09-27 18:34:57
tags:
  - Git
  - 技术
categories: 技术
comments: true
---

> 子曰：merge 使用

在工作协作开发中，使用`git` 进行版本管理，下面整理了使用`git stash` 的流程

## 流程

1.首先是在`develop` 分支上进行开发，对内容进行了修改，但是并没有进行提交，此时状态为：
```bash
git status
```
提示如下
```
On branch develop
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   aaa.txt

no changes added to commit (use "git add" and/or "git commit -a")
```
2.此时需要跳到`master` 分支上进行开发，但是你不想提交你现在正在进行的工作，所以你需要存在这些变更`git stash`
```bash
git stash
```
提示如下
```
Saved working directory and index state WIP on develop: 61b5a09 abb page
```
此时调用`git status`
```bash
git status
```
提示显示
```
On branch develop
nothing to commit, working tree clean
```
3.此时再切换到`master` 分支进行处理其他事务
```bash
git checkout master
```
处理完其他事务之后，进行提交
```
git add .
git commit -m 'some msg'
git push origin master
```
4.再次切换回`develop` 分支进行处理
```bash
git checkout develop
```
5.查看现在的存储列表
```bash
git stash list
```
提示如下
```
stash@{0}: WIP on develop: 61b5a09 abb page
```
6.重新应用储藏，同时立刻将其从堆栈中移走
```bash
git stash pop
```
提示如下
```
On branch develop
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   abb/aaa.txt

no changes added to commit (use "git add" and/or "git commit -a")
Dropped refs/stash@{0} (8a2838ea4ee8c037685f27922586388e49f3ec76)
```
现在就可以继续在之前的存储上进行开发了

<!-- more -->

## 命令
1.`git stash`
保存当前工作进度，会把暂存区和工作区的改动保存起来。执行完这个命令后，在运行`git status`命令，就会发现当前是一个干净的工作区，没有任何改动。使用`git stash save 'message...'`可以添加一些注释
2.`git stash list`
显示保存进度的列表。也就意味着，`git stash`命令可以多次执行。
3.`git stash pop [–index] [stash_id]`
 * `git stash pop` 恢复最新的进度到工作区。git默认会把工作区和暂存区的改动都恢复到工作区。通过`git stash pop`命令恢复进度后，会删除当前进度。
 * `git stash pop --index` 恢复最新的进度到工作区和暂存区。（尝试将原来暂存区的改动还恢复到暂存区）
 * `git stash pop stash@{stash_id}`恢复指定的进度到工作区。`stash_id`是通过`git stash list`命令得到的
4.`git stash apply [–index] [stash_id]`
除了不删除恢复的进度之外，其余和`git stash pop` 命令一样。
5.`git stash drop [stash_id]`
删除一个存储的进度。如果不指定`stash_id`，则默认删除最新的存储进度。
6.`git stash clear`
删除所有存储的进度。

## 参考文档
1.[Git工具-存储（stashing）](https://git-scm.com/book/zh/v1/Git-%E5%B7%A5%E5%85%B7-%E5%82%A8%E8%97%8F%EF%BC%88Stashing%EF%BC%89)
2.[使用git stash命令保存和恢复进度](https://blog.csdn.net/daguanjia11/article/details/73810577)
