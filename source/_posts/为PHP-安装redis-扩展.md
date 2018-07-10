---
title: 为PHP 安装redis 扩展
date: 2018-07-10 18:03:48
tags:
  - 技术
  - php
  - redis
categories: 技术
---

> 子曰：安装php 的redis 扩展

项目中搭建本地环境，需要安装php 的redis 扩展，将遇到的问题记录下来

## 本地环境
1.windows 7 x64
2.AppServ
3.php 7.1

<!--more-->

## 安装过程
0x00：
首先查看php 的版本信息 http://localhost/phpinfo.php ，**注意其使用红框标注出来的信息版本、架构，尤其需要注意架构，这里是x86，不是window 7 系统x64，在下载版本时要下载x86 的。**
{% asset_img php_0.jpg %}
0x01:
`php_redis.dll` 的版本选择：http://pecl.php.net/package/redis/3.1.1/windows 在这里选择[7.1 Thread Safe（TS）x86](https://windows.php.net/downloads/pecl/releases/redis/3.1.1/php_redis-3.1.1-7.1-ts-vc14-x86.zip)
`php_igbinary.dll` 的版本选择: http://pecl.php.net/package/igbinary/2.0.6/windows 在这里选择[7.1 Thread Safe(TS) x86](https://windows.php.net/downloads/pecl/releases/igbinary/2.0.6/php_igbinary-2.0.6-7.1-ts-vc14-x86.zip)
0x02:
将上述两个zip 下载解压，将其中的`php_redis.dll` 和`php_igbinary.dll` 拷贝到php7.1 目录下的ext 目录
0x03:
修改位于php7 目录下的php.ini 新增如下内容
```php
;php_redis
extension=php_igbinary.dll
extension=php_redis.dll
```
注意，其中的php_igbinary.dll 必须在php_redis.dll 的前面，否则此扩展不生效。
0x04:
重启apache 服务
0x05:
查看http://localhost/phpinfo.php ，注意新增了如下内容
{% asset_img php_1.jpg %}

## 参考文档
1.[Windows下为PHP安装redis扩展 白鹿博客](https://www.cnblogs.com/aksir/p/7183492.html)
