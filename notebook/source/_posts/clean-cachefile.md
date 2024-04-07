---
title: 清理linux垃圾内容
date: 2023-10-13 10:19:41
categories:
- linux
tags: linux_manager
---

# 一些文件夹
1. ~/.cache/vscode-cpptools/ipch
ipch文件夹是Intelli Sense（好像是预编译头文件之类的）这个东西产生的缓存文件，占用空间很大！！！每编译一次文件就会产生ipch里对应的一个文件夹。我看了我的ipch文件夹产生了121个对应的文件夹，总共产生了6G多。。。惊人啊！

在VSCode找到设置→扩展→C/C++→Intelli Sense Cache Path，如上图。1处写明了vscode默认Intelli Sense缓存文件路径是在C盘的，2处可以更改为你要保存缓存文件的位置。据我所知，这些缓存文件删了也不影响的之前的文件的，所以可以随时删~只是每次编译又会重新产生，所以还是把缓存路径改为别的盘吧
[参考](https://blog.csdn.net/qq_41688558/article/details/99085069)

2. ~/.config/Code/Service Worker/CacheStorage
ubuntu系统下，我们的空间会越来越小，有时候不知道大文件藏在哪。其中有一项就是vscode的缓存，时间久了可能会有几十G的空间，把缓存删掉，可以腾出很大空间。
不同电脑的vscode安装目录可能有区别：
cd ~/.config/Code/User/workspaceStorage/
rm ./*
cd ~/.config/Code/Service\ Worker/CacheStorage$
rm ./*

[参考](https://blog.csdn.net/u011754972/article/details/120764945)

3. anaconda/pkts
conda clean 命令是对所有anaconda下的包进行搜索，不用再进入其他环境重复操作
conda clean -p 这个命令会检查哪些包没有在包缓存中被硬依赖到其他地方，并删除它们
conda clean -t
刚刚清理的缓存的packages，现在继续清理缓存的压缩包文件，代码如下：conda clean -t

https://blog.csdn.net/zhouchen1998/article/details/124397874
https://blog.csdn.net/Robin_Pi/article/details/115004870

4. ~/.local/share/Trash

linux的垃圾箱
