---
title: GitHub Pages + Hexo 搭建博客
comment: true
date: 2016-10-21 16:58:16
tags: [技术,静态独立博客,Hexo,GitHub Pages, Git]
---
## 一、简介
这是一篇使用GitHub Pages 和Hexo 搭建免费独立博客的总结

## 二、Hexo
1. 安装Hexo
```cmd
$ npm install -g hexo-cli
```
2. 使用Hexo 建站

  2.1 使用hexo init 初始化个人博客
```cmd
$ hexo init hexo-blog
```
  2.2 进入站点目录，并安装依赖包
```cmd
$ cd hexo-blog
$ npm install
```
  2.3 启动本地服务
```cmd
$ hexo server
```
  2.4 访问浏览器 http://localhost:4000 就可以在本地进行预览了。

## 三、GitHub Pages 仓库
1. 创建对应仓库
在自己的GitHub 帐号下创建一个新的仓库，命名为JobbyM.github.io
2. 创建hexo 分支，hexo 用来存放Hexo 生成的网站原始的文件，master 分支用来存放生成的静态网页

## 四、部署到GitHub 上
1. 部署到GitHub 上需要进行配置_config.yml 文件，将_config.yml 中的
```yml
# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy:
  type:
```
  按照如下进行修改
```yml
# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy:
  type: git
  repo: https://github.com/JobbyM/JobbyM.github.io.git
  branch: master
```
2. 本地运行如下命令进行部署
```cmd
$ hexo deploy
```
3. 在浏览器中访问 https://jobbym.github.io/index.html

## 五、搭建流程
1. 初始化个人博客
```cmd
$ hexo init hexo-blog
```
2. 进入hexo-blog 目录，进行git 初始化
```cmd
$  cd hexo-blog
$  git init
```
3. 添加远程主机
```cmd
$ git remote add origin https://github.com/JobbyM/JobbyM.github.io.git
```
4. 更改默认分支为hexo
```cmd
$ git checkout -b hexo
```
5. 提交文件到本地
```
$  git add .
$  git commit -m "hexo-blog init and git init"
$  git pull origin hexo
```
  产生冲突
```cmd
warning: no common commits
remote: Counting objects: 4, done.
remote: Compressing objects: 100% (3/3), done.
remote: Total 4 (delta 0), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (4/4), done.
From https://github.com/JobbyM/JobbyM.github.io
 * branch            hexo       -> FETCH_HEAD
 * [new branch]      hexo       -> origin/hexo
Auto-merging .gitignore
CONFLICT (add/add): Merge conflict in .gitignore
Automatic merge failed; fix conflicts and then commit the result.
```
6. 处理冲突，再次将代码进行提交
```cmd
$ git status
On branch hexo
You have unmerged paths.
  (fix conflicts and run "git commit")
Changes to be committed:
        new file:   README.md
Unmerged paths:
  (use "git add <file>..." to mark resolution)
        both added:      .gitignore
```
  代码提交
```cmd
$  git add .
$  git commit -m "fix conflict in .gitignore"
$  git push -u origin hexo
```
  提交结果
```cmd
Counting objects: 110, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (100/100), done.
Writing objects: 100% (110/110), 521.17 KiB | 0 bytes/s, done.
Total 110 (delta 2), reused 0 (delta 0)
remote: Resolving deltas: 100% (2/2), done.
To https://github.com/JobbyM/JobbyM.github.io.git
   77af381..57d0a0e  hexo -> hexo
Branch hexo set up to track remote branch hexo from origin.
```
7. 修改_config.yml 并进行提交
```cmd
$  git add .
$  git commti -m "modify _config.yml"
$  git push origin hexo
```
8. 安装hexo-deployer-git 模块，并进行代码提交
```
$  npm install hexo-deployer-git --save
$  git add .
$  git commit -m "npm install hexo-deployer-git --save"
$  git push origin hexo
```
9. 部署
```
$ hexo deploy
```
  部署成功
```cmd
INFO  Deploying: git
INFO  Setting up Git deployment...
Initialized empty Git repository in D:/nodejs/hexo-blog/.deploy_git/.git/
[master (root-commit) ae9275c] First commit
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 placeholder
INFO  Clearing .deploy_git folder...
INFO  Copying files from public folder...
warning: LF will be replaced by CRLF in 2016/10/21/hello-world/index.html.
The file will have its original line endings in your working directory.
warning: LF will be replaced by CRLF in archives/2016/10/index.html.
The file will have its original line endings in your working directory.
warning: LF will be replaced by CRLF in archives/2016/index.html.
The file will have its original line endings in your working directory.
warning: LF will be replaced by CRLF in archives/index.html.
The file will have its original line endings in your working directory.
warning: LF will be replaced by CRLF in css/style.css.
The file will have its original line endings in your working directory.
warning: LF will be replaced by CRLF in fancybox/helpers/jquery.fancybox-buttons.css.
The file will have its original line endings in your working directory.
warning: LF will be replaced by CRLF in fancybox/helpers/jquery.fancybox-buttons.js.
The file will have its original line endings in your working directory.
warning: LF will be replaced by CRLF in fancybox/helpers/jquery.fancybox-media.js.
The file will have its original line endings in your working directory.
warning: LF will be replaced by CRLF in fancybox/helpers/jquery.fancybox-thumbs.css.
The file will have its original line endings in your working directory.
warning: LF will be replaced by CRLF in fancybox/helpers/jquery.fancybox-thumbs.js.
The file will have its original line endings in your working directory.
warning: LF will be replaced by CRLF in fancybox/jquery.fancybox.css.
The file will have its original line endings in your working directory.
warning: LF will be replaced by CRLF in fancybox/jquery.fancybox.js.
The file will have its original line endings in your working directory.
warning: LF will be replaced by CRLF in fancybox/jquery.fancybox.pack.js.
The file will have its original line endings in your working directory.
warning: LF will be replaced by CRLF in index.html.
The file will have its original line endings in your working directory.
warning: LF will be replaced by CRLF in js/script.js.
The file will have its original line endings in your working directory.
warning: LF will be replaced by CRLF in 2016/10/21/hello-world/index.html.
The file will have its original line endings in your working directory.
warning: LF will be replaced by CRLF in archives/2016/10/index.html.
The file will have its original line endings in your working directory.
warning: LF will be replaced by CRLF in archives/2016/index.html.
The file will have its original line endings in your working directory.
warning: LF will be replaced by CRLF in archives/index.html.
The file will have its original line endings in your working directory.
warning: LF will be replaced by CRLF in css/style.css.
The file will have its original line endings in your working directory.
warning: LF will be replaced by CRLF in fancybox/helpers/jquery.fancybox-buttons.css.
The file will have its original line endings in your working directory.
warning: LF will be replaced by CRLF in fancybox/helpers/jquery.fancybox-buttons.js.
The file will have its original line endings in your working directory.
warning: LF will be replaced by CRLF in fancybox/helpers/jquery.fancybox-media.js.
The file will have its original line endings in your working directory.
warning: LF will be replaced by CRLF in fancybox/helpers/jquery.fancybox-thumbs.css.
The file will have its original line endings in your working directory.
warning: LF will be replaced by CRLF in fancybox/helpers/jquery.fancybox-thumbs.js.
The file will have its original line endings in your working directory.
warning: LF will be replaced by CRLF in fancybox/jquery.fancybox.css.
The file will have its original line endings in your working directory.
warning: LF will be replaced by CRLF in fancybox/jquery.fancybox.js.
The file will have its original line endings in your working directory.
warning: LF will be replaced by CRLF in fancybox/jquery.fancybox.pack.js.
The file will have its original line endings in your working directory.
warning: LF will be replaced by CRLF in index.html.
The file will have its original line endings in your working directory.
[master 9a77861] Site updated: 2016-10-21 15:45:40
 29 files changed, 5761 insertions(+)
 create mode 100644 2016/10/21/hello-world/index.html
 create mode 100644 archives/2016/10/index.html
 create mode 100644 archives/2016/index.html
 create mode 100644 archives/index.html
 create mode 100644 css/fonts/FontAwesome.otf
 create mode 100644 css/fonts/fontawesome-webfont.eot
 create mode 100644 css/fonts/fontawesome-webfont.svg
 create mode 100644 css/fonts/fontawesome-webfont.ttf
 create mode 100644 css/fonts/fontawesome-webfont.woff
 create mode 100644 css/images/banner.jpg
 create mode 100644 css/style.css
 create mode 100644 fancybox/blank.gif
 create mode 100644 fancybox/fancybox_loading.gif
 create mode 100644 fancybox/fancybox_loading@2x.gif
 create mode 100644 fancybox/fancybox_overlay.png
 create mode 100644 fancybox/fancybox_sprite.png
 create mode 100644 fancybox/fancybox_sprite@2x.png
 create mode 100644 fancybox/helpers/fancybox_buttons.png
 create mode 100644 fancybox/helpers/jquery.fancybox-buttons.css
 create mode 100644 fancybox/helpers/jquery.fancybox-buttons.js
 create mode 100644 fancybox/helpers/jquery.fancybox-media.js
 create mode 100644 fancybox/helpers/jquery.fancybox-thumbs.css
 create mode 100644 fancybox/helpers/jquery.fancybox-thumbs.js
 create mode 100644 fancybox/jquery.fancybox.css
 create mode 100644 fancybox/jquery.fancybox.js
 create mode 100644 fancybox/jquery.fancybox.pack.js
 create mode 100644 index.html
 create mode 100644 js/script.js
 delete mode 100644 placeholder
warning: LF will be replaced by CRLF in js/script.js.
The file will have its original line endings in your working directory.
Branch master set up to track remote branch master from https://github.com/JobbyM/JobbyM.github.io.git.
To https://github.com/JobbyM/JobbyM.github.io.git
 + 77af381...9a77861 HEAD -> master (forced update)
INFO  Deploy done: git
```
10. 访问 https://jobbym.github.com/index.html

## 六、参考文档
1. [Hexo搭建Github静态博客](http://www.cnblogs.com/zhcncn/p/4097881.html)
2. [GitHub Pages + Hexo搭建博客](http://crazymilk.github.io/2015/12/28/GitHub-Pages-Hexo%E6%90%AD%E5%BB%BA%E5%8D%9A%E5%AE%A2)
3. [git远程操作详解](http://www.ruanyifeng.com/blog/2014/06/git_remote.html)
