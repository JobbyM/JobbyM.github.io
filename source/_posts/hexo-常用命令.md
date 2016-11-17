---
title: hexo 常用命令
date: 2016-11-17 15:53:32
comments: true
tags:
  - 技术
  - 静态独立博客
  - Hexo
  - GitHub Pages
  - Git
categories: 技术
---

## 简介
这是一篇记录使用Hexo 写作的常用命令笔记
{% asset_img hexo_posts.jpg %}

<!--more-->

## 常用命令

### 写作

你可以执行如下命令来创建一篇新文章。

```bash
$ hexo new <title>
```

使用实例

```bash
$ hexo new "hexo 常用命令"
```

### 生成静态文件

在新建一篇文章之后，你可以执行如下命令来生成静态文件

```bash
$ hexo generate
```

### 启动服务器

生成静态文件之后，你可以执行如下命令来启动服务器

```bash
$ hexo server
```

默认情况下，访问地址为：<http://localhost:4000>

### Front-matter

```
comments: true
tags:
  - 技术
  - 静态独立博客
  - Hexo
  - GitHub Pages
  - Git
categories: 技术
```

参数 | 描述
---|---|---
comments | 开启文章的评论功能
tags | 标签
categories | 分类
permalink | 覆盖文章网址

### 阅读全文标志

你可以在文章中添加如下命令

```jsp
<!--more-->
```

### 添加引用居中

你可以在文章中添加如下命令

```jsp
  {% cq %} A fast,simple & powerful blog framework,powered by Node.js {% endcq %}
```

### 使用表格

你可以在文章中添加如下命令

```jsp
参数 | 描述
---|---|---
comments | 开启文章的评论功能
tags | 标签
categories | 分类
permalink | 覆盖文章网址
```

### 引用图片

你可以在文章中添加如下命令

```jsp
{% asset_img hexo_posts.jpg %}
```

注意你需要修改站点配置文件`_config.yml` 的下列命令，之后再使用`$ hexo new hexo 常用命令` 来生成文章，此时会在`_post`文件夹中同时生成`hexo 常用命令` 文件夹，在此文件夹中加入相应的图片资源就可以了

```jsp
post_asset_folder: true
```

### 超级链接

你可以如下使用超级链接

```jsp
[Hexo 中文文档](https://hexo.io/zh-cn/docs/index.html/)
```

## 参考文章

1. [Hexo 中文文档](https://hexo.io/zh-cn/docs/index.html/)
