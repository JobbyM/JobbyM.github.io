---
title: github 使用 PAT 方式提交代码
date: 2021-08-19 10:48:04
tags:
  - 技术
  - github
  - PAT
categories: 技术
---

> 子曰：github 使用 PAT 方式提交代码

## 问题
给 github.com 上的项目提交代码，提示如下问题
{% asset_img 1.png %}

<!--more-->

大概是说，从 2021 年 8 月 13 日起，通过密码认证的方式被取消，建议使用 PAT（Person Access Token ）。

## 解决
1. 首先登录 github.com 获取 PAT
点击 GitHub 用户设置页面 最下方的 Developer setting ，然后选择 Personal access tokens 来生成一个 token，由于我们只需要能够对普通仓库 push 就行了，所以把 repo 部分勾上即可
{% asset_img 2.jpg %}
2. Windows 上找的编辑凭据的位置
从控制面板 -> 用户账户 -> 凭据管理器 点击编辑，将复制的 PAT 粘贴到密码位置就可以了。
{% asset_img 3.png %}
