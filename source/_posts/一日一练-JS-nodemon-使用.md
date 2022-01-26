---
title: 一日一练-JS nodemon 使用
date: 2022-01-07 11:50:44
tags:
  - 技术
  - 一日一练
  - JS
  - nodemon
categories: 技术
---

{% asset_img 1.png %}
[Nodemon](https://nodemon.io/) 监视源代码中的任何更改，并自动重新启动服务器。
终端命令安装
```bash
npm install -g nodemon # or using yarn: yarn global add nodemon
```

也可以作为开发依赖
```bash
npm install --save-dev nodemon # or using yarn: yarn add nodemon -D
```

## 特点
* 自动重启应用程序。
* 检测要监视的默认文件扩展名。
* 默认支持 `node` ，但易于运行任何可执行文件，如 `python、ruby、make` 等。
* 忽略特定的文件或目录。
* 查看特定的目录。
* 与服务器应用程序或一次性运行实用程序和 REPL 配合使用。
* 可通过 `node` 的 `require` 语句编写脚本。
* 开源，可在 [github](https://github.com/remy/nodemon/) 上获得。

<!--more-->

## 使用
nodemon 会包装你的应用程序，这样就可以把通常会传递给应用程序的所有参数传递 nodemona：
```bash
nodemon [your node app]
```

在本地 8080 端口，监听 server.js
```bash
nodemon ./server.js localhost 8080
```

## 运行非 node 脚本
```bash
nodemon --exec "python -v" ./app.py
```

## 监听多个目录
```bash
nodemon --watch app --watch libs app/server.js
```

## 配置可观察的文件扩展名
nodemon 默认观察的的的文件是 `.js`，`.mjs`，`.coffee`，`.litcoffee` 和 `.json`。如果你使用 `--exec` 选项监控 `app.py`，nodemon 将监控扩展名为 `.py` 的文件。 但是，也可以使用 `-e`（或 `--ext`）开关指定你自己的列表，如下所示：
```bash
nodemon -e js,pug
```

现在 nodemon 将在对目录（或子目录）中扩展名为 `.js`、`.pug `的文件进行任何更改时重新启动。

## 忽略文件
```bash
nodemon --ignore lib/ --ignore tests/
```

```bash
nodemon --ignore lib/app.js
```

```bash
nodemon --ignore 'lib/*.js'
```

nodemon 默认忽略 `.git`, `node_modules`, `bower_components`, `.nyc_output`, `coverage` 和 `.sass-cache` 目录。

## 延迟重启
延迟 10 s
```bash
nodemon --delay 10 server.js
```

延迟 2.5 s
```bash
nodemon --delay 2.5 server.js
```

延迟 2500 ms
```bash
nodemon --delay 2500ms server.js
```