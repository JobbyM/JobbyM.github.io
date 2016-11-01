---
title: createing a Single Page Application
date: 2016-11-01 11:54:30
comments: true
tags:
  - 技术
  - create-react-app
  - react
  - SPA
categories: 技术
---
## 一、简介
这是一篇使用create-react-app 的简单入门
1. create-react-app
  {% cq %} Create React App is the best way to starting building a new React single page application {% endcq %}

<!--more-->

## 二、入门

1. 运行npm install -g create-react-app
```cmd
$ npm install -g create-react-app
D:\git\nvmw\v6.3.0\create-react-app -> D:\git\nvmw\v6.3.0\node_modules\create-react-app\index.js
D:\git\nvmw\v6.3.0
`-- create-react-app@0.6.0
  +-- chalk@1.1.3
  | +-- ansi-styles@2.2.1
  | +-- escape-string-regexp@1.0.5
  | +-- has-ansi@2.0.0
  | | `-- ansi-regex@2.0.0
  | +-- strip-ansi@3.0.1
  | `-- supports-color@2.0.0
  +-- cross-spawn@4.0.2
  | +-- lru-cache@4.0.1
  | | +-- pseudomap@1.0.2
  | | `-- yallist@2.0.0
  | `-- which@1.2.11
  |   `-- isexe@1.1.2
  +-- minimist@1.2.0
  +-- path-exists@2.1.0
  | `-- pinkie-promise@2.0.1
  |   `-- pinkie@2.0.4
  `-- semver@5.3.0
```
2. 使用create-react-app my-app
```cmd
$ npm install -g create-react-app
D:\git\nvmw\v6.3.0\create-react-app -> D:\git\nvmw\v6.3.0\node_modules\create-react-app\index.js                                                      
D:\git\nvmw\v6.3.0
`-- create-react-app@0.6.0
  +-- chalk@1.1.3
  | +-- ansi-styles@2.2.1
  | +-- escape-string-regexp@1.0.5
...
Success! Created my-app at D:\nodejs\my-app
Inside that directory, you can run several commands:
  npm start
    Starts the development server.
  npm run build
    Bundles the app into static files for production.
  npm test
    Starts the test runner.
  npm run eject
    Removes this tool and copies build dependencies, configuration files
    and scripts into the app directory. If you do this, you can’t go back!
We suggest that you begin by typing:
  cd my-app
  npm start
Happy hacking!
```
3. 进入应用并启动
```cmd
$ cd my-app
$ npm start

Starting the development server...
Compiled successfully!
The app is running at:
  http://localhost:3000/
Note that the development build is not optimized.
To create a production build, use npm run build.
```
  {% asset_img welcometoreactapp.jpg %}

## 三、参考文档

1. [create-react-app 官网](https://github.com/facebookincubator/create-react-app)
2. [react 官网](https://facebook.github.io/react/docs/installation.html)
