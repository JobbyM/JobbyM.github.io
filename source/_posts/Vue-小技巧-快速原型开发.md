---
title: Vue 小技巧-快速原型开发
date: 2021-02-24 14:43:01
tags:
  - 技术
  - Vue
  - Tree
categories: 技术
---

> 子曰：万里之行，始于足下。

## 场景
平常开发项目以 Vue 为主，需要测试一些功能时，

<!--more-->

## 解决方案

1.在原有项目上新建一个git 分支
```
git checkout -b feature-20210224
```
2.新建一个路由页面，然后在这个页面上进行实现。
3.在实现完成之后需要将这个分支删除
```
git branch -d feature-20210224
```

### 问题

这会导致一些问题：
1.如果忘记执行第三步进行删除分支，会导致分支比较多
2.在多个项目中，代码比较分散，技术积累不宜保留下来

## 优化方案
1.使用 `Vue CLI` 的快速原型开发，可以解决上述的几个问题

## 快速原型开发
使用 `vue serve` 和 `vue build` 命令对单个 `*.vue` 文件进行快速原型开发

1.安装一个全局的扩展
```
npm install -g @vue/cli-service-global --registry=https://registry.npm.taobao.org
```
2.新建一个 `App.vue` 文件
```bash
mkdir sfc-vue && cd sfc-vue
touch App.vue
```
```js
<template>
  <h1>快速原型开发</h1>
</template>
```
3.在 sfc-vue 目录下运行如下命令
```bash
vue serve
```
4.启动浏览器访问上一步运行的网络地址 `http://127.0.0.1:8080` ，就可以看到如下效果了
{% asset_img 0.png %}


## 参考文档
1.[Vue CLI 快速原型开发](https://cli.vuejs.org/zh/guide/prototyping.html)

