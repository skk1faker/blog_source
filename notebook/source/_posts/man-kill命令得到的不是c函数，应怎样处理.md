---
title: man kill命令得到的不是c函数，应怎样处理
date: 2024-03-25 17:35:19
tags: 
- linux
---

使用`man 2 kill`即可解决，注意前面的 2，它指示 man 命令查找系统调用（通常是库函数）的手册页。在大多数系统上，1 表示用户命令，2 表示系统调用，3 表示库函数等。
