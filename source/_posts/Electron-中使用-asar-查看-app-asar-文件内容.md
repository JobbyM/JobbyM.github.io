---
title: Electron 中使用 asar 查看 app.asar 文件内容
date: 2021-03-09 16:40:01
tags:
  - 技术
  - Electron
  - asar
categories: 技术
---

> 子曰：万里之行，始于足下。

在 `win-unpacked/resources/` 下生成了 `app.asar` 文件，这是一个用 asar 压缩后的文件。 我们可以解压看下里面是什么：

## 查看 asar
1. 安装 `asar`
```bash
npm install -g asar --registry=https://registry.npm.taobao.org
```

2. 解压到 `./app` 文件夹下
```bash
asar extract app.asar ./app
```

3. 解压之后的目录如下
```
-- css
-- js
-- img
-- background.js
-- favicon.ico
-- index.html
-- package.json
```