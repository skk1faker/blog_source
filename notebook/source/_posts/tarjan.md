---
title: tarjan
date: 2024-03-31 07:28:19

tags:
- tarjan算法
---

# 参考资料

[1. Kosaraju算法 ](https://www.cnblogs.com/RioTian/p/14026585.html)
[2. tarjan算法](https://minghu6.github.io/algs/GraphTarjanDFS.html)

# tarjan算法中，横向边和反祖边是什么

1. 反祖边：将dfs搜索过程建立成树，那么图中点a的一些边指向a的祖先，则称这些边为返祖边。（根节点到达a点路径中的所有点都成为是a的祖先）

2. 横向边：使用dfs访问节点，在访问到a点时，a存在一些边指向已经访问过且不是a祖先的节点，这样的边横向边称为  

在无向图中，tarjan算法不会生成横向边，因为AB两个区域出现横向边时，在访问A区域时会通过AB间的横向边访问到B区域，此时的横向边也变成了返祖边。只有在有向图中会出现横向边。

![边的分类](边的分类.png)

# 无向图中的tarjan算法

tarjan算法通过搜索树判断无向图中的割点和桥。一个点删除后产生连通分量如大于一，则这个点叫做割点。一个边删除后产生联通分量如大于1，则这个边叫做桥。

为正确的找到 割点和桥，需要tarjan中的dfn和low数组.
## low和dfn数组的概念

tarjan借助于dfs实现，dfs将标号

# 有向图中的tarjan算法


