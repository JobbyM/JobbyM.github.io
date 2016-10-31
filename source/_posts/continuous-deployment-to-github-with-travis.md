---
layout: post
title: 使用Travis CI 自动部署Hexo
comments: true
date: 2016-10-25 17:12:17
tags:
  - Travis CI
  - Hexo
  - CI
  - 技术
categories: 技术
---

## 简介
利用Travis CI 自动部署Hexo 到GitHub Pages 上
1. Travis CI
  {% cq %} Build apps with confidence. Focus on writing code. Let Travis CI toake care of running your tests and deploying your apps. {% endcq %}

<!--more-->

## 操作步骤

### 一、Deploy Key（部署Key）
1. 使用ssh-keygen 制作SSH Key，供GitHub 当作Deploy key 使用
```cmd
jobbym@ubuntu:~$ ssh-keygen -t rsa -C "songzhipengmune@gmail.com"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/jobbym/.ssh/id_rsa):
/home/jobbym/.ssh/id_rsa already exists.
Overwrite (y/n)? y
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/jobbym/.ssh/id_rsa.
Your public key has been saved in /home/jobbym/.ssh/id_rsa.pub.
The key fingerprint is:
ad:3c:0f:f0:b4:99:05:5c:48:e0:06:fb:07:a1:8a:81 songzhipengmune@gmail.com
The key's randomart image is:
+--[ RSA 2048]----+
|    . oo...      |
|.    = o..       |
|E   o + o        |
| o . o . o       |
|. .   o S o      |
|       * *       |
|        X        |
|         +       |
|          .      |
+-----------------+

```
  在制作SSH key 时，请把passphase 留空。当SSH key 制作完成后，复制Public key 到GitHub 上的Deploy key 位置
2. 注意：Window 7 下使用命令行复制内容到剪贴板
```cmd
$ clip < log.txt
```
  将log.txt 文件中的内容复制到剪贴板

### 二、加密 Private Key （私钥）
1. Windows 环境下需要安装Ubuntu 虚拟机，以下操作都是在Ubuntu 虚拟机环境下进行操作
2. 首先是安装ruby ,安装成功过后查看ruby 和gem 版本
```cmd
jobbym@ubuntu:~$ sudo apt-get install ruby
jobbym@ubuntu:~$ ruby -v
ruby 1.9.3p484 (2013-11-22 revision 43786) [x86_64-linux]
jobbym@ubuntu:~$ gem -v
1.8.23
```
3. 上述ruby 版本过低，需要重新安装
```cmd
jobbym@ubuntu:~$  sudo apt-get remove ruby ruby-dev
```
4. 使用rvm 进行ruby 版本管理
```cmd
jobbym@ubuntu:~$ curl -L get.rvm.io | bash -s stable
```
  安装rvm 报错，参考 http://blog.csdn.net/caspiansea/article/details/47802331 进行处理
5. 使用rvm 查看可用的ruby 版本安装
```cmd
jobbym@ubuntu:~$ rvm list known
```
6. 使用rvm 进行安装，发现速度很慢
```cmd
jobbym@ubuntu:~$ rvm install 2.2-head
```
7. 参考 https://ruby-china.org/wiki/rvm-guide 修改rvm 的ruby 安装源
```cmd
jobbym@ubuntu:~$ echo "ruby_url=https://cache.ruby-china.org/pub/ruby" > ~/.rvm/user/db
```
8. 再次进行安装速度变快
```cmd
jobbym@ubuntu:~$ rvm install 2.3
```
9. 设置2.3 为默认ruby ，并进行查看
```cmd
jobbym@ubuntu:~$ rvm alias create default ruby-2.3.0
Creating alias default for ruby-2.3.0...
jobbym@ubuntu:~$ rvm list
rvm rubies
=* ruby-2.3.0 [ x86_64 ]
# => - current
# =* - current && default
#  * - default
```
10. 查看ruby gem 相关版本信息
```cmd
jobbym@ubuntu:~$ ruby -v
ruby 2.3.0p0 (2015-12-25 revision 53290) [x86_64-linux]
jobbym@ubuntu:~$ gem -v
2.5.1
```
11. 安装travis
```cmd
jobbym@ubuntu:~$ gem install travis
Fetching: ffi-1.9.14.gem (100%)
Building native extensions.  This could take a while...
Successfully installed ffi-1.9.14
Fetching: ethon-0.9.1.gem (100%)
Successfully installed ethon-0.9.1
Fetching: typhoeus-0.8.0.gem (100%)
Successfully installed typhoeus-0.8.0
Fetching: websocket-1.2.3.gem (100%)
Successfully installed websocket-1.2.3
Fetching: pusher-client-0.6.2.gem (100%)
Successfully installed pusher-client-0.6.2
Fetching: addressable-2.4.0.gem (100%)
Successfully installed addressable-2.4.0
Fetching: launchy-2.4.3.gem (100%)
Successfully installed launchy-2.4.3
Fetching: highline-1.7.8.gem (100%)
Successfully installed highline-1.7.8
Fetching: net-http-pipeline-1.0.1.gem (100%)
Successfully installed net-http-pipeline-1.0.1
Fetching: connection_pool-2.2.0.gem (100%)
Successfully installed connection_pool-2.2.0
Fetching: net-http-persistent-3.0.0.gem (100%)
Successfully installed net-http-persistent-3.0.0
Fetching: multi_json-1.12.1.gem (100%)
Successfully installed multi_json-1.12.1
Fetching: multipart-post-2.0.0.gem (100%)
Successfully installed multipart-post-2.0.0
Fetching: faraday-0.9.2.gem (100%)
Successfully installed faraday-0.9.2
Fetching: backports-3.6.8.gem (100%)
Successfully installed backports-3.6.8
Fetching: gh-0.14.0.gem (100%)
Successfully installed gh-0.14.0
Fetching: faraday_middleware-0.10.0.gem (100%)
Successfully installed faraday_middleware-0.10.0
Fetching: travis-1.8.2.gem (100%)
Successfully installed travis-1.8.2
Parsing documentation for ffi-1.9.14
Installing ri documentation for ffi-1.9.14
Parsing documentation for ethon-0.9.1
Installing ri documentation for ethon-0.9.1
Parsing documentation for typhoeus-0.8.0
Installing ri documentation for typhoeus-0.8.0
Parsing documentation for websocket-1.2.3
Installing ri documentation for websocket-1.2.3
Parsing documentation for pusher-client-0.6.2
Installing ri documentation for pusher-client-0.6.2
Parsing documentation for addressable-2.4.0
Installing ri documentation for addressable-2.4.0
Parsing documentation for launchy-2.4.3
Installing ri documentation for launchy-2.4.3
Parsing documentation for highline-1.7.8
Installing ri documentation for highline-1.7.8
Parsing documentation for net-http-pipeline-1.0.1
Installing ri documentation for net-http-pipeline-1.0.1
Parsing documentation for connection_pool-2.2.0
Installing ri documentation for connection_pool-2.2.0
Parsing documentation for net-http-persistent-3.0.0
Installing ri documentation for net-http-persistent-3.0.0
Parsing documentation for multi_json-1.12.1
Installing ri documentation for multi_json-1.12.1
invalid options: -SHN
(invalid options are ignored)
Parsing documentation for multipart-post-2.0.0
Installing ri documentation for multipart-post-2.0.0
Parsing documentation for faraday-0.9.2
Installing ri documentation for faraday-0.9.2
Parsing documentation for backports-3.6.8
Installing ri documentation for backports-3.6.8
Parsing documentation for gh-0.14.0
Installing ri documentation for gh-0.14.0
Parsing documentation for faraday_middleware-0.10.0
Installing ri documentation for faraday_middleware-0.10.0
Parsing documentation for travis-1.8.2
Installing ri documentation for travis-1.8.2
Done installing documentation for ffi, ethon, typhoeus, websocket, pusher-client, addressable, launchy, highline, net-http-pipeline, connection_pool, net-http-persistent, multi_json, multipart-post, faraday, backports, gh, faraday_middleware, travis after 37 seconds
18 gems installed
```
12. 打开博客项目文件夹，在项目根目录新家.travis.yml 配置文件
```cmd
jobbym@ubuntu:~/learn/blog$ touch .travis.yml
```
13. 新家文件夹.travis：确保已经开启travis 支持
```cmd
jobbym@ubuntu:~/learn/blog$ mkdir .travis
jobbym@ubuntu:~/learn/blog$ cd .travis
```
14. 复制id_rsa 到.travis 目录下
```cmd
jobbym@ubuntu:~/learn/blog/.travis$ cp ~/.ssh/id_rsa ./
```
15. 在.travis 目录下创建ssh_config 文件
```cmd
jobbym@ubuntu:~/learn/blog/.travis$ sudo vim ssh_config
[sudo] password for jobbym:

Host github.com
User git
StrictHostKeyChecking no
IdentityFile ~/.ssh/id_rsa
IdentitiesOnly yes

```
16. 使用命令工具登入Travis
```cmd
jobbym@ubuntu:~/learn/blog/.travis$ travis login --auto
We need your GitHub login to identify you.
This information will not be sent to Travis CI, only to api.github.com.
The password will not be displayed.

Try running with --github-token or --auto if you don't want to enter your password anyway.

Username: JobbyM
Password for JobbyM: ***********
Successfully logged in as JobbyM!

```
  这样一来，我们就能够通过Travis 提供的命令行工具加密刚刚制作的Private key，并把它上传供日后使用。上面的Username 和Password 就是登录Travis 的Username 和Password
13. 前面生成的Private key 的文件名为id_rsa，Trivas 会加密生成id_rsa.enc,并自动在.travis.yml 的before_install 中，自动插入解密指令,-r 选项用来指定repos 名称
```cmd
jobbym@ubuntu:~/learn/blog/.travis$ travis encrypt-file id_rsa --add -r JobbyM/blog
encrypting id_rsa for JobbyM/blog
storing result as id_rsa.enc
storing secure env variables for decryption

Make sure to add id_rsa.enc to the git repository.
Make sure not to add id_rsa to the git repository.
Commit all changes to your .travis.yml.
```
  需要删除id_rsa 密钥文件（私钥不能随便透露）
14. 编写.travis.yml 文件
```yml
language: node_js
node_js:
  - "6"

branches:
  only:
    - hexo

# Travis-CI Caching
cache:
  directories:
    - node_modules

# S: Buid Lifecycle
before_install:
  # Decrypt the private key
  - openssl aes-256-cbc -K $encrypted_b3c3b32c2fbe_key -iv $encrypted_b3c3b32c2fbe_iv -in .travis/id_rsa.enc -out ~/.ssh/id_rsa -d
  # Set the permission of the key:
  - chmod 600 ~/.ssh/id_rsa
  # Start SSH agent
  - eval $(ssh-agent)
  # Add the private key to the system
  - ssh-add ~/.ssh/id_rsa
  # Copy SSH config
  - cp .travis/ssh_config ~/.ssh/config
  # Set Git config
  - git config --global user.name JobbyM
  - git config --global user.email songzhipengmune@gmail.com

install:
  - npm install hexo-cli -g
  - npm install
  - npm install hexo-deployer-git --save

script:
  - hexo clean
  - hexo g
  - hexo g
  - hexo d

# E: Build LifeCycle
```

## 参考文档

1.[publishing github pages with travis-ci](http://notes.iissnan.com/2016/publishing-github-pages-with-travis-ci/)
2.[.travis.yml 文件](https://github.com/iissnan/theme-next-docs/blob/master/.travis.yml)
3.[使用Travis CI 自动部署hexo](http://blog.acwong.org/2016/03/20/auto-deploy-hexo-with-travis-CI/)
4.[.travis.yml 文件](https://github.com/acwong00/blog/blob/master/.travis.yml)
5.[hexo 作者使用travis 部署blog](https://zespia.tw/blog/2015/01/21/continuous-deployment-to-github-with-travis/)
6.[windows 下需要在虚拟机中进行安装](http://blog.csdn.net/lablenet/article/details/52509663)
