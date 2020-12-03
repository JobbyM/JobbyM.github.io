---
title: Hexo集成Algolia搜索插件时遇到的问题
date: 2020-12-03 14:55:03
tags:
  - 技术
  - 静态独立博客
  - Hexo
  - Algolia
categories: 技术
---

>从来就没有什么救世主

今天刚刚写了一篇随笔，发布之后，发现在github.com 上并没有部署成功，
于是就到`travis-ci.org` 上查找了，发现CI 自动部署失败。
在CI 的log 中查找的原因，费了九牛二虎之力才修复成功。
而这篇文章就是记录自己的解决过程。

<!-- more -->

## 问题描述
在`travis-ci.org` 部署日志中发现了如下错误
```
hexo algolia
INFO  [hexo-algolia] Testing HEXO_ALGOLIA_INDEXING_KEY permissions.
ERROR [hexo-algolia] getaddrinfo ENOTFOUND sv57wj53os-1.algolianet.com
ERROR >> You might have used an Admin Key or an invalid Key.
ERROR >> Read https://npmjs.com/hexo-algolia#security-concerns for more informations.
The command "hexo algolia" exited with 1.
```

## 问题分析
1.主要是分析
```
getaddrinfo ENOTFOUND sv57wj53os-1.algolianet.com
```
应该是`sv57wj53os-1.algolianet.com`域名解析失败，将此地址放到浏览器访问中提示如下
{% asset_img 1.png %}

2.在`hexo-algolia` 的源码上 `https://github.com/oncletom/hexo-algolia/blob/master/lib/command.js#L77` 找到如下代码
```js
hexo.log.info('[hexo-algolia] Testing HEXO_ALGOLIA_INDEXING_KEY permissions.');

return client.getApiKey(apiKey)
.catch(function(err){
    hexo.log.error('[hexo-algolia] %s', err.message);
    hexo.log.error('>> You might have used an Admin Key or an invalid Key.');
    hexo.log.error('>> Read %s for more informations.', 'https://npmjs.com/hexo-algolia#security-concerns');
    process.exit(1);
});
})
```

3.是在获取`client.ApiKey()` 时，遇到的问题，而`client` 是由
```js
var algoliasearch = require('algoliasearch');
var client;
client = algoliasearch(applicationID, apiKey);
```
赋值的。

4.需要到`algoliasearch` 的源码上查找，而`algoliasearch` 是在`http://theme-next.iissnan.com/third-party-services.html#algolia-search` 第三方服务Aloglia 中实现的。在`https://github.com/iissnan/hexo-theme-next/blob/master/source/lib/algolia-instant-search/instantsearch.min.js` 搜素`algolianet.com` 结果如下：
{% asset_img 3.png %}

5.也就是说`sv57wj53os-1.algolianet.com` 是由`applicationID` 和`aligolianet.com` 组成。

6.在`_config.yml` 中设置的`applicationID` 是`JQFAQAQAP5`
```yaml
# Algolia
algolia:
  applicationID: 'JQFAQAQAP5'
  indexName: 'dev_jobbymsblog'
  chunkSize: 5000
```

7.重新提交代码，再次执行就OK 了

## 解决方案
1.重新提交代码，再次执行，发现执行成功了
```bash
$ hexo algolia
INFO  [hexo-algolia] Testing HEXO_ALGOLIA_INDEXING_KEY permissions.
INFO  Start processing
INFO  [hexo-algolia] 196 records to index (post, page).
INFO  [hexo-algolia] Indexing chunk 1 of 4 (50 records)
INFO  [hexo-algolia] Indexing chunk 2 of 4 (50 records)
INFO  [hexo-algolia] Indexing chunk 3 of 4 (50 records)
INFO  [hexo-algolia] Indexing chunk 4 of 4 (46 records)
INFO  [hexo-algolia] Indexing done.
The command "hexo algolia" exited with 0.
```

