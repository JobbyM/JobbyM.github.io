---
title: Git 学习笔记
date: 2016-11-08 10:35:19
tags:
  - Git
  - 技术
categories: 技术
comments: true
---

## 介绍
1. Git 是由Linux 之父Linus Tovalds 为了更好地管理linux 内核开发而创立的分布式版本控制/原件配置管理软件。
2. 简单来说，Git 是一个管理你的“代码的历史记录”的工具

<!--more-->

## 名词解释
1. 版本回退
  * HEAD 指向的版本就是当前版本，因此，Git 允许我们在版本的历史之间穿梭，使用命令<code>git reset --hard commit_id</code>。
  * 穿梭前，用<code>git log</code>可以查看提交历史，以便确定要回退到哪个版本。
  * 要重返未来，用<code>git reflog</code>查看命令历史，以便确定要回到未来的哪个版本。
2. 工作区（Working Directory）
  就是在电脑里能看到的目录
3. 版本库（Repository）
  工作区里有一个隐藏目录.git ，这个不算工作区，而是Git 的版本库。
  Git 的版本库里存了很多东西，其中最重要的就是称为stage（或者index）的暂存区，还有Git 为我们自动创建的一个分支master，以及指向master 的一个指针HEAD。
  {% asset_img git.jpg  git %}
  我们把文件往Git 版本库里添加的时候，是分两步执行的：
  1. 第一步是用<code>git add</code> 把文件添加进去，实际上就是把文件修改添加到暂存区；
  2. 第二步是用<code>git commit</code> 提交更改，实际上就是把暂存区的所有内容提交到当前分支。
