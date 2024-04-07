---
title: hexo导航栏设置
date: 2024-04-07 15:43:10
tags:
- hexo
---

1. 先在`theme/next/_config.yml`中将`menu`字段配置。举个例子`tags: 路径 || 图标`，路径是指source下的路径，各个图标点进去会加载index.yml文件。

```bash
menu:
  home: / || fa fa-home
  about: /about/ || fa fa-user
  tags: /tags/ || fa fa-tags
  categories: /categories/ || fa fa-th
  archives: /archives/ || fa fa-archive
  #schedule: /schedule/ || fa fa-calendar
  #sitemap: /sitemap.xml || fa fa-sitemap
  #commonweal: /404/ || fa fa-heartbeat
```

2. 新建各种对应的页面主要命令如下
```bash
hexo new page categories
```

相应的会在source下出现一个navigate文件夹，里面有一个`index.md`文件，需要进行设置

categories设置如下:  
`使用hexo new page categories`生成，修改相应index.md
```bash
---
title: categories
date: 2024-04-07 12:23:16
type: "categories"
comments: false
---
```
tags设置如下:  
`使用hexo new page tags`生成，修改相应index.md
```bash
---
title: tags
date: 2024-04-07 12:23:25
type: "tags"
comments: false
---
```
archives设置如下：
```bash
无需修改，将theme/next/_config.yml中menu中archives位置修改即可。
```

3. 新设定栏目

一些栏在next主题中没有预先设定，但设定过程主要是将`theme/next/_config.yml`中`menu`添加新的字段
```bash
menu:
  home: / || fa fa-home
  # about: /about/ || fa fa-user
  tags: /tags/ || fa fa-tags
  categories: /categories/ || fa fa-th
  archives: /archives/ || fa fa-archive
  navigation: /navigation || fa fa-user
  #schedule: /schedule/ || fa fa-calendar
  #sitemap: /sitemap.xml || fa fa-sitemap
  #commonweal: /404/ || fa fa-heartbeat
```
上述代码中添加了`navigation`字段，

但是在显示中有如下情况：

![languages_setting](languages_setting.png)

可以看到导航为英文，这里将`theme/next/languages/zh-CN.yml`中相关部分进行翻译，设置如下

```yaml
menu:
  home: 首页
  archives: 归档
  categories: 分类
  tags: 标签
  about: 关于
  search: 搜索
  schedule: 日程表
  sitemap: 站点地图
  commonweal: 公益 404
  navigation: 导航
```
将navigation给出相关翻译即可

![language_seting_2](language_seting_2.png)

4. 主页设定

[主页设定](https://segmentfault.com/a/1190000039871393)
在没有设定主页之前，hexo主要使用根目录`_config.yml`指定的主页，即显示写好的文章， 这样并不好看，可以通过如下设置得到主页
```bash
根路径下_config.yml设置如下：
index_generator:
  path: /default-index/       # 这是一个无效的路径，主要目的是将hexo中默认主页去掉换位next皮肤指定的主页，也就是source下的index.md文件显示的内容
  per_page: 10
  order_by: -date

./theme/next/_config.yml

只需将menu相应的home开关打开就行,之后在source下建立index.md文件
```


