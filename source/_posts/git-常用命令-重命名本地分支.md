---
title: git 常用命令 重命名本地分支
date: 2022-04-22 09:15:14
tags:
  - Git
  - 技术
categories: 技术
---

> 子曰：git 常用命令 重命名本地分支

**0x00：前言**
开发时经常会遇到需要修改 Git 分支名字的时候，以便后期维护，统一管理。

**0x01：本地分支重命名**
```bash
git branch -m oldName  newName
```

<!--more-->

**0x02：将重命名的分支推送到远程**
```bash
git push origin newName
```

**0x03：删除远程的旧分支**
```bash
git push --delete origin oldName
```