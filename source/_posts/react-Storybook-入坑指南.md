---
title: react Storybook 入坑指南
date: 2017-03-26 13:22:36
tags:
  - React
  - Storybook
  - 技术
categories: 技术
comments: true
---

## 简介

[Storybook](https://getstorybook.io) 是React 组件UI 开发环境，其作用在于让前端工程师专注在样式、组件功能的开发过程
中，不必担心应用程序特定的依赖关系和环境准备。

## 快速上手

```bash
npm i -g getstorybook
cd my-react-app
getstorybook
```

<!--more-->

## Q&A

一、问题描述：

 环境：
     OS：window7
     nodejs：v6.3.0
     npm：3.10.3
步骤：
运行`npm -i -g getstorybook` 之后，创建了一个`my-react-app` 文件夹，然后在`my-react-app` 中运行`getstorybook` 报错，如下图所示：
{% asset_img getstorybook-error,jpg %}

解决方案：

第一步、`my-react-app` 需要是一个react 项目，因此需要安装`react` 和`react-dom` 在你的项目中，运行如下命令进行项目的创建，以及依赖的安装

```bash
npm init --yes
npm i --save react react-dom
```

再次运行`getstorybook` 此时出现如下问题：
{% asset_img getstorybook-error-2,jpg %}

第二步、此时需要重新运行`npm i` 命令，有一个babel 相关的警告
{% asset_img getstorybook-error-3.jpg %}

因为警告基本可以忽略，所以继续运行`getstorybook` 出现如下问题
{% asset_img getstorybook-error-4.jpg %}

第三步、提示已经安装了storybook，如果想要重新安装storybook，需要运行`getstorybook -f`，因为不需要重新安装，所以直接`npm run storybook` 命令
{% asset_img getstorybook-error-5.jpg%}

第四步、上述提示已经成功运行了，所以可以直接在浏览器输入http://localhost:6006 打开查看效果了：
{% asset_img getstorybook-error-6.jpg%}

## 参考文档

1. [react Storybook 官网](https://getstorybook.io)
