---
title: lower_bound和upper_bound查找大于等于、小于等于、大于、小于的方法
date: 2023-12-25 17:01:26
tags: c++
---

https://www.zhihu.com/question/63931004

常规情况下`lower_bound`查找的是大于等于，`upper_bound`查找的是大于，但是通过`great<int>()`可以将`lower_bound()`变为查找小于等于的函数，`upper_bound()`变为查找小于的函数
