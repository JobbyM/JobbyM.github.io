---
title: git 常用命令 标签管理
date: 2018-07-31 16:02:22
tags:
  - Git
  - 技术
categories: 技术
comments: true
---

> 子曰：git 常用命令 标签管理

**0x00：前言**
发布一个版本时，我们通常先在版本库中打一个标签（tag），这样，就唯一确定了打标签时刻的版本。将来无论什么时候，取某个标签的版本，就是把那个打标签的时刻的历史版本取出来。所以，标签也是版本库的一个快照。

为什么要引入`tag`？考虑如下情景：
“请把上周一那个版本打包发布，commit 号是6a5819”
“一串乱七八糟的数字不好找！”
如果换一个办法：
“请把上周一那个版本打包发布，版本号是v1.0.0”
“好的，按照tag v1.0.0 查找commit 就行了！”
所以，tag 就是一个让人容易记住的有意义的数字。

<!--more-->

**0x01：创建标签**
使用`git tag <name>` 进行创建轻量级标签，轻量级标签实际上就是一个保存着对应提交对象的校验和信息的文件。
```bash
git tag v1.0.0
```
默认标签是创建在最新提交的`commit` 上的
使用`git tag <name> <commit_id>` 将标签创建在某一个`commit_id` 上
```
git tag v1.0.0 6a5819
```
还可以使用`git tag -a <name> -m 'commit'` 创建一个含附注类型的标签，`-a`（`annotated` 的首字母）指定标签名字即可。而`-m` 选项则制定了对应的标签说明
```bash
git tag -a v1.0.0 -m "my version v1.0.0"
```

**0x02：查看标签**
使用`git tag` 命令查看所有的标签
```bash
git tag
```
查看某一个标签详细内容，可以使用`git show <tagname>`
```bash
git show v1.0.0
```

**0x03：推送远端**
使用命令`git push origin <tagname>` 将本地某个标签推送到远端
```bash
git push origin v1.0.0
```
或者，一次性推送全部尚未推送到远端的本地标签
```bash
git push origin --tags
```

**0x04：删除标签**
如果标签打错了，可以删除，使用命令`git tag -d <tagname>`
```bash
git tag -d v1.0.0
```
如果要删除远端标签，使用命令`git push origin :refs/tags/<tagname>`
```bash
git push origin :refs/tags/v1.0.0
```

**0x05：参考文档**
1.[Git 基础 - 打标签](https://git-scm.com/book/zh/v1/Git-%E5%9F%BA%E7%A1%80-%E6%89%93%E6%A0%87%E7%AD%BE)
2.[标签管理 廖雪峰](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/0013762144381812a168659b3dd4610b4229d81de5056cc000)
