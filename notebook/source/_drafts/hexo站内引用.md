---
title: hexo站内引用
tags:
- hexo
---

# 参考

[知乎参考链接](https://zhuanlan.zhihu.com/p/163020630)

# 站内引用

    以下 post_path 和 post_link 两个属性方法的区别是：
    post_path 返回的是字符串；
    post_link 返回的是超链接； 

1. `post_link`引用
```hexo
{% post_link 站内文章标题 文章内显示标题的名字 是否对字符转义}
站内目标文章的标题用来替代目标文章的标题在本页面内显示的文字，同时作为鼠标悬浮的时候显示title属性的文字，默认链接文字是文章的标题,如果包含空格的话那么使用英文单引号裹起来，否则的话不需要单引号  
true或者false，用来控制是否对引用文章的标题和自定义标题里的特殊字符进行转义，默认值为true转义  %}

注意：
1.生成的链接默认不带有 target=”_blank” 属性； 
2.在使用此标签时可以 忽略 文章文件所在的 路径 或者 文章的永久链接信息、如语言、日期； 
 只要存在一个名为 站内文章标题.md 的文章文件即可。

例如下面的链接:
{% post_link 容斥原理 站内文章 false %}
```
{% post_link 容斥原理 站内文章 false %}

2. post_path只会以字符串形式返回文章路径，用法
```hexo
语法： 
花括号%  固定的属性asset_path  资源文件夹中资源的名称，例如图片名称及后缀不需要路径  %花括号

示例： 
{% asset_path test.jpg %} 

返回 test.jpg 图片资源的站内链接地址字符串如下所示： 
/2020/03/14/Markdown写作模板/test.jpg
```
