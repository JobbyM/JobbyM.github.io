---
title: 使用Travis CI 自动部署Hexo 之后续维护
date: 2020-07-30 08:46:29
tags:
---

# 简介
前几天写了一篇{% post_link Windows环境安装MongoDB Windows环境安装MongoDB %} 文章，在个人网站上https://jobbym.github.io/ 发现没有自动部署，于是去查找了一下原因。

将这次处理过程记录下来，为后续的维护留下线索。下面是之前利用Travis CI 自动部署Hexo 到GitHub Pages 时，自己记录的相关文章。

1. {% post_link Travis-CI-基础入门 Travis-CI-基础入门 %}
2. {% post_link Hexo集成Algolia搜索插件 Hexo集成Algolia搜索插件 %}
3. {% post_link continuous-deployment-to-github-with-travis 利用Travis CI 自动部署Hexo 到GitHub Pages 上 %}

<!--more-->

# 准备环境
由于时全新的Windows 10 系统的电脑，部署环境需要重新搭建
1. Windows 10 系统
2. Git
3. Nodejs

# 处理过程
首先登录到Travis CI 上查看错误原因
```bash
31.62s$ hexo g
INFO  Start processing
INFO  Files loaded in 8.31 s
ERROR Asset render failed: css/main.css
/home/travis/build/JobbyM/JobbyM.github.io/node_modules/stylus/node_modules/mkdirp/lib/opts-arg.js:7
    opts = { mode: 0o777, fs, ...opts }
                              ^^^
SyntaxError: Unexpected token ...
    at createScript (vm.js:56:10)
    at Object.runInThisContext (vm.js:97:10)
```
{% asset_img 0.png %}

## 一、看到这个问题首先时怀疑项目依赖的问题，需要更新依赖
使用npm-check 来检查npm依赖包是否有更新，错误以及不在使用的，我们也可以使用npm-check进行包的更新
安装npm-check：
```
npm install -g npm-check
```
检查npm包的状态:
```
npm-check -u -g
```

## 二、部署之后，Travis CI 仍然有问题
```bash
Warning: Permanently added the RSA host key for IP address '140.82.113.4' to the list of known hosts.
Permission denied (publickey).
fatal: Could not read from remote repository.
Please make sure you have the correct access rights
and the repository exists.
```
{% asset_img 1.png %}
提示没有权限进行部署，查看了一下https://github.com/JobbyM/JobbyM.github.io/settings/keys 没有相关的Deploy Key，可能是自己不小心删除了，所以重新需要生成一个。
{% asset_img 2.png %}

使用ssh-keygen 制作SSH Key，供GitHub 当作Deploy key 使用
```bash
ssh-keygen -t rsa -C "songzhipengmune@gmail.com"
```

## 三、Deploy key 重新生成了，那么需要重新加密相应的Private Key。
之前{% post_link continuous-deployment-to-github-with-travis 利用Travis CI 自动部署Hexo 到GitHub Pages 上 %} 是使用在Windows 上安装Ubuntu 虚拟机进行处理的，现在可以使用Windows10 访问Ubuntu 子系统（WSL）来进行处理。

## 四、安装完Ubuntu 需要做的第一件事，就是换源。
1. 备份原始源文件sources.list
```bash
sudo cp /etc/apt/sources.list /etc/apt/sources.list.backup
```
2. 编辑源文件sources.list
```bash
sudo vi /etc/apt/sources.list
```
3. 将官方源`http://archive.ubuntu.com/ubuntu/` 替换为阿里源`http://mirrors.aliyun.com/ubuntu/`
```bash
:%s/archive.ubuntu/mirrrors.aliyun/g
```
4. 之后执行保存命令
```bash
:wq
```
5. 修改完软件源后，更新软件列表和软件
```bash
sudo apt update
sudo apt upgrade
```

## 五、之后安装travis
1. 需要安装`ruby` `ruby-dev` 来通过`gem` 安装`travis`
```bash
sudo apt-get install ruby
sudo apt-get install ruby-dev
```
2. `ruby` 也需要换源
查看源列表
```bash
gem sources -l
```
3. 更新源
```bash
gem sources --add https://gems.ruby-china.com/ --remove https://rubygems.org/
```
4. 更新源的缓存
```bash
gem sources -u
```
5. 安装travis
```bash
sudo gem install travis
```
6. 提示错误
```bash

```


## 六、需要搭建C/C++编译开发环境
1. 安装gcc
```bash
sudo apt-get install gcc
```
2. 安装g++
```bash
sudo apt-get install g++
```
3. 需要安装make
```bash
sudo apt-get install make
```
4. 之后再安装travis 就成功了

## 七、之后就是使用Travis
1. 使用命令工具登录Travis
```bash
jobbym@ubuntu:~/learn/blog/.travis$ travis login --auto
```
2. Trivas 将前面生成的Private key 的文件id_rsa， 加密生成id_rsa.enc， 并自动在.travis.yml 的before_install 中，自动插入解密指令，-r 选项用来指定repos 名称
```bash
jobbym@ubuntu:~/learn/blog/.travis$ travis encrypt-file id_rsa --add -r JobbyM/blog
```
3. ** 注意** 这里应该将`JobbyM/blog` 修改为`JobbyM/JobbyM.github.io`，否则在自动部署时候，会提示如下错误
```bash
0.01s$ openssl aes-256-cbc -K $encrypted_f217180e22ee_key -iv $encrypted_f217180e22ee_iv -in .travis/id_rsa.enc -out ~/.ssh/id_rsa -d
iv undefined
The command "openssl aes-256-cbc -K $encrypted_f217180e22ee_key -iv $encrypted_f217180e22ee_iv -in .travis/id_rsa.enc -out ~/.ssh/id_rsa -d" failed and exited with 1 during .
```
{% asset_img 3.png %}

## 八、之后再次进行部署
1. 提示了如下错误
```bash
$ hexo algolia
FATAL Something's wrong. Maybe you can find the solution here: https://hexo.io/docs/troubleshooting.html
AlgoliaSearchError: Please provide an API key. Usage: algoliasearch(applicationID, apiKey, opts)
    at AlgoliaSearchNodeJS.AlgoliaSearchCore 
```
{% asset_img 4.png %}
2. 大概是说缺少API key，查看了文档http://theme-next.iissnan.com/third-party-services.html#algolia-search，提示需要将Search-Only API key 设置为环境变量
```bash
$ export HEXO_ALGOLIA_INDEXING_KEY=Search-Only API key
$ hexo algolia
```
3. 修改_config.yml 为如下
```yml
# Algolia
algolia:
  applicationID: 'SV57WJ53OS'
  indexName: 'jobbymsblog'
  chunkSize: 5000
```
{% asset_img 5.png %}
4. 修改`.travis.yml` ，在before_install 之前设置环境变量
```yml
before_install:
- export HEXO_ALGOLIA_INDEXING_KEY=$hexo_aloglia
```
{% asset_img 6.png %}
5. 在https://www.travis-ci.org/github/JobbyM/JobbyM.github.io/settings 这里进行新增一个变量`hexo_aloglia`
{% asset_img 7.png %}
6. 这个变量的值为Admin API Key 的值，而不是Search-Only API Key 的值。在https://www.algolia.com/apps/SV57WJ53OS/api-keys/all 在这里进行查看相关值
{% asset_img 8.png %}
7. 使用Search-Only API Key 会提示如下错误信息， 没有权限去更新对象
```bash
$ hexo algolia
INFO  [hexo-algolia] Testing HEXO_ALGOLIA_INDEXING_KEY permissions.
INFO  Start processing
INFO  [hexo-algolia] 191 records to index (post, page).
INFO  [hexo-algolia] Indexing chunk 1 of 4 (50 records)
ERROR [hexo-algolia] Not enough rights to update an object near line:1 column:1539
INFO  [hexo-algolia] Indexing chunk 2 of 4 (50 records)
```
{% asset_img 9.png %}

## 九、更新之后会跳转到`http://zouzeir.xyz/` 域名，需要将`source/CNAME` 文件内容清空，同时重新刷新浏览器



# 参考文档
1. [Ubuntu 18.04 下搭建 C/C++编译开发环境及GCC多版本切换](https://www.linuxidc.com/Linux/2019-04/158258.htm)
2. [Windows10访问Ubuntu子系统（WSL）的桌面环境](https://blog.csdn.net/xmh19936688/article/details/90212960)