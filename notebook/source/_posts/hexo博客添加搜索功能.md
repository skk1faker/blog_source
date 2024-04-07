---
title: hexo博客添加搜索功能
date: 2024-04-07 13:52:17
tags:   
- hexo
---

# 参考
[如何给网站添加站内搜索功能](https://champyin.com/2019/01/26/%E8%BF%9B%E9%98%B6-%E5%9B%9B-%EF%BC%9A%E7%BB%99%E5%8D%9A%E5%AE%A2%E6%B7%BB%E5%8A%A0%E7%AB%99%E5%86%85%E6%90%9C%E7%B4%A2%E5%8A%9F%E8%83%BD/)

# 设置方法

1. 安装插件

```bash
npm i --save hexo-generator-search hexo-generator-searchdb
```

2. 将根目录中配置文件`_config.yml`添加如下内容
```yaml
# local_search
search:
  path: search.xml
  field: post
  format: html
  limit: 10000
```

3. next主题中在`theme/next/_config.yml`文件中修改如下内容：
```yaml
local_search:
  enable: true
  # if auto, trigger search by changing input
  # if manual, trigger search by pressing enter key or search button
  trigger: auto
  # show top n results per article, show all results by setting to -1
  top_n_per_article: 1
```

4. 最后启动服务器
hexo clean && hexo s -d
