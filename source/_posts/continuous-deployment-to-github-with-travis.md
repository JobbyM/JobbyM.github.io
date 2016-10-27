---
layout: post
title: 使用Travis CI 自动部署Hexo
comments: true
date: 2016-10-25 17:12:17
tags:
  - Travis-CI
  - Hexo
  - CI
  - 技术
categories: 技术
---

## 操作步骤

### 一、Deploy Key（部署Key）
1. 使用ssh-keygen 制作SSH Key，供GitHub 当作Deploy key 使用
```cmd
$ ssh-keygen -t rsa -C "songzhipengmune@gmail.com"
Generating public/private rsa key pair.
Enter file in which to save the key (/c/Users/Administrator/.ssh/id_rsa):
/c/Users/Administrator/.ssh/id_rsa already exists.
Overwrite (y/n)? y
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /c/Users/Administrator/.ssh/id_rsa.
Your public key has been saved in /c/Users/Administrator/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:QLewULPH+KgT82OAwJCTs5CR4Rz8Du+sS95wopODqEo songzhipengmune@gmail.com
The key's randomart image is:
+---[RSA 2048]----+
|=O  ..= .        |
|&..  o O .       |
|oB.   = +        |
|.o o   =         |
|  = + . S        |
|   o *           |
|oEo.o +          |
|@ =o o .         |
|**o.             |
+----[SHA256]-----+
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

## 参考文档

1.[publishing github pages with travis-ci](http://notes.iissnan.com/2016/publishing-github-pages-with-travis-ci/)
2.[.travis.yml 文件](https://github.com/iissnan/theme-next-docs/blob/master/.travis.yml)
3.[使用Travis CI 自动部署hexo](http://blog.acwong.org/2016/03/20/auto-deploy-hexo-with-travis-CI/)
4.[.travis.yml 文件](https://github.com/acwong00/blog/blob/master/.travis.yml)
5.[hexo 作者使用travis 部署blog](https://zespia.tw/blog/2015/01/21/continuous-deployment-to-github-with-travis/)
6.[windows 下需要在虚拟机中进行安装](http://blog.csdn.net/lablenet/article/details/52509663)
