---
title: GitHub Pages 入门
date: 2016-10-31 09:48:56
comments: true
tags:
  - 技术
  - 静态独立博客
  - GitHub Pages
  - Git
categories: 技术
---
## 一、简介
这是一篇使用GitHub Pages 的简单入门
1. GitHub Pages
  GitHub Pages 可以被认为是用户编写的、托管在GitHub 上的静态网页。
  {% cq %} Websites for you and your projects. Hosted directly from your GitHub repository. Just edit, push, adn your changes are live. {% endcq %}

<!--more-->

## 二、入门
1. 创建一个仓库
  首先是去GitHub 上创建一个新的仓库并且命名为username.github.io，其中username 是你在GitHub 上的用户名。（必须确保username.github.io 同你的用户名完全匹配，否则将会出错）
2. 克隆这个仓库
```cmd
$ git clone https://github.com/username/username.github.io.git
```
3. Hello World
  进入项目文件夹并且添加一个index.html 文件
```cmd
$ cd username.github.io
$ echo "Hello World" > index.html
```
4. 提交
  添加、注释、并且提交你的个改变
```cmd
$ git add --all
$ git commit -m "Initial commit"
$ git push -ur origin master
```
5. 完成
  启动一个浏览器访问 https://username.github.io

## 三、参考文档
1. [GitHub Pages 官网](https://pages.github.com/)
