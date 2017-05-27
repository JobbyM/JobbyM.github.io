---
title: 在Heroku 上部署Node.js 应用
date: 2017-05-27 16:34:14
comments: true
tags:
  - 技术
  - Heroku
  - Node.js
categories: 技术
---

## 前言

工作之余，按照[nswbmw](https://github.com/nswbmw) 的[一起学Node.js](https://github.com/nswbmw/N-blog)教程，一步一步实现了一个Express + MongoDB 的多人博客，并部署到了Heroku 上。

本篇教程就是在[4.15 部署](https://github.com/nswbmw/N-blog/blob/master/book/4.15%20%E9%83%A8%E7%BD%B2.md)的基础上，按照自己的实际操作记录的部署过程。

<!--more-->

## 部署到Heorku

1. 首先在github.com 上新建一个my-blog 的repo， **注意：除了Repository name 什么也不要填写** 点击`Create repository` 创建成功
2. 本地进入`D:\nodejs\my-blog` 添加远端仓库
```
D:\nodejs\my-blog>git remote add origin https://github.com/JobbyM/my-blog.git
```
3. 提交代码
```
D:\nodejs\my-blog>git push -u origin master
```
4. 新建分支heroku，master 分支为[一起学Node.js](https://github.com/nswbmw/N-blog) 教程的[4.15 部署](https://github.com/nswbmw/N-blog/blob/master/book/4.15%20%E9%83%A8%E7%BD%B2.md) 之前的内容
```
D:\nodejs\my-blog>git checkout -b heroku
```
5. 删除掉`.gitignore` 中的
```
config/*
!config/default.*
```
  因为我们无法登录到Heroku 主机创建production 配置文件，所以这里将productin 配置也上传到Heroku。
6. 打开index.js，将`app.listen` 修改为
```
var port = process.env.PORT || config.port
app.listen(port, function(){
     console.log(`${pkg.name} listening on port ${port}`)
})
```
7. 新增Heroku 需要的Procfile 文件，内容为
```
web: npm run heroku
```
  同时修改package.json 文件，在scripts 中新增如下命令
```
"heroku": "NODE_ENV=production node index"
```
8. 在[Heroku](https://www.heroku.com/) 上注册用户，之后下载安装Heroku 的命令行工具包Toolbelt。参考这里[下载安装](https://devcenter.heroku.com/articles/getting-started-with-nodejs#set-up)
9. 在命令行中登录Heroku，Email 和Password 为注册Heroku 的相关信息
```
D:\nodejs\my-blog>heroku login
Enter your Heroku credentials:
Email: songzhipengmune@gmail.com
Password: *********
Logged in as songzhipengmune@gmail.com
```
10. 在Heroku 上创建应用
```
D:\nodejs\my-blog>heroku create
```
 {% asset_img Heroku_1.png %}
11. 部署代码到Heroku 上
```
D:\nodejs\my-blog>git push heroku heroku:master
Counting objects: 209, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (194/194), done.
Writing objects: 100% (209/209), 372.78 KiB | 0 bytes/s, done.
Total 209 (delta 104), reused 0 (delta 0)
remote: Compressing source files... done.
remote: Building source:
remote:
remote: -----> Node.js app detected
remote:
remote: -----> Creating runtime environment
remote:
remote:        NPM_CONFIG_LOGLEVEL=error
remote:        NPM_CONFIG_PRODUCTION=true
remote:        NODE_VERBOSE=false
remote:        NODE_ENV=production
remote:        NODE_MODULES_CACHE=true
remote:
remote: -----> Installing binaries
remote:        engines.node (package.json):  unspecified
remote:        engines.npm (package.json):   unspecified (use default)
remote:
remote:        Resolving node version 6.x via semver.io...
remote:        Downloading and installing node 6.10.3...
remote:        Using default npm version: 3.10.10
remote:
remote: -----> Restoring cache
remote:        Skipping cache restore (new-signature)
remote:
remote: -----> Building dependencies
remote:        Installing node modules (package.json)
remote:        my-blog@1.0.0 /tmp/build_68cbc0c86c867e77d30b4dabaed7f230
remote:        ├─┬ config-lite@2.0.0
remote:        │ ├─┬ chalk@1.1.3
remote:        │ │ ├── ansi-styles@2.2.1

remote:
remote:
remote: -----> Caching build
remote:        Clearing previous node cache
remote:        Saving 2 cacheDirectories (default):
remote:        - node_modules
remote:        - bower_components (nothing to cache)
remote:
remote: -----> Build succeeded!
remote: -----> Discovering process types
remote:        Procfile declares types -> web
remote:
remote: -----> Compressing...
remote:        Done: 17.7M
remote: -----> Launching...
remote:        Released v3
remote:        https://xx.herokuapp.com/ deployed to Heroku
remote:
remote: Verifying deploy... done.
To https://git.heroku.com/xx.git
 * [new branch]      heroku -> master
```
 注意：必须是这样部署，否则将会跳过构建，例如如下部署
```
D:\nodejs\my-blog>git push heroku heroku
```
 提示
```
D:\nodejs\my-blog>git push heroku heroku
Counting objects: 209, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (194/194), done.
Writing objects: 100% (209/209), 372.78 KiB | 0 bytes/s, done.
Total 209 (delta 104), reused 0 (delta 0)
remote: Pushed to non-master branch, skipping build.
To https://git.heroku.com/murmuring-fjord-81424.git
 * [new branch]      heroku -> heroku
```
12. 部署成功之后，使用
```
D:\nodejs\my-blog>heroku open
```
 打开应用主页
13. 将代码提交到github 库
```
D:\nodejs\my-blog>git push origin heroku:heroku
```
