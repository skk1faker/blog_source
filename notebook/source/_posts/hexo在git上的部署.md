---
title: hexo在git上的部署
date: 2024-04-08 05:39:26
tags:
- hexo
---



# hexo部署过程


整个设置过程主要分为github与hexo两方面的设置。

## github

github上需要建立一个特殊名字的仓库`your_github_user_name.github.io·`，建立为public，不加入README.md

![](github_page.png)

## hexo

1. 需要设置在根目录下`_config.yml`文件，加入或修改如下内容。

```yaml
deploy:
  type: git
  repo: git@github.com:username/username.github.io.git      #项目的ssh地址，使用http的可能会推不上去，我没试过
  # example, https://github.com/hexojs/hexojs.github.io
  branch: main          # 随便一个分支，因为这个分支是新建的
```

2. 上述配置结束后，还需使用命令`npm i --save hexo-deployer-git`安装插件`hexo-deployer-git`。

3. 执行hexo clean && hexo deploy


当上述过程结束后，查看网站`username.githubn.io`即可发现博客已经部署！！！

## 参考

[hexo官方教程](https://hexo.io/zh-cn/docs/github-pages.html)  
[github page 的设置](https://pages.github.com/)
