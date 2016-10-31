---
title: Travis CI 基础入门
date: 2016-10-26 16:13:28
tags:
  - 技术
  - Travis CI
  - Hexo
  - GitHub Pages
  - Git
comments: true
updated:
categories: 技术
permalink: Travis-CI-基础入门
---

# 简介
为了了解Travis CI，而到Travis CI 官网上按照一个demo 进行练习了解
1. Travis CI
  {% cq %} Build apps with confidence. Focus on writing code. Let Travis CI toake care of running your tests and deploying your apps. {% endcq %}
  
<!--more-->
## 使用Travis CI 测试
1. 使用GitHub 帐号登录 https://travis-ci.org/
2. 进入 https://docs.travis-ci.com/user/for-beginners 页面
3. fork https://github.com/plaindocs/travis-broken-example 这个测试项目
4. 进入 https://travis-ci.org/profile 使travis-broken-example 这个项目能够进行Travis CI Build
5. 本地进行git clone
```cmd
$ git clone https://github.com/plaindocs/travis-broken-example.git
```
6. 进行项目
```cmd
$ cd travis-broken-example
```
7. 查看 .travis.yml 文件，此文件告诉Travis CI 如何工作
8. 在NewUser.txt 文件中编辑你的名字，并进行git add commit push 操作去触发Travis CI
```cmd
$ git add .
$ git commit -m "Testing Travis CI"
$ git push
```
9. 查看 https://travis-ci.org/ 可以看到Travis CI 构建失败了
{% asset_img travis_ci_fail.jpg %}
10. 修改Test.php 中的代码
```php
$this->assertEquals(1+1,2);
```
11. 进行git add commit push 操作去触发Travis CI
```cmd
$ git add .
$ git commit -m "'Testing Travis CI: fixing the build"
$ git push
```
12. 查看 https://travis-ci.org/ 可以看到Travis CI 构建成功了
{% asset_img travis_ci_success.jpg %}

## 参考文档

1. [Travis CI官网](https://travis-ci.org/)
2. [Travis CI官网入门](https://docs.travis-ci.com/user/for-beginners) assset
