---
title: meld使用技巧
tags: 
- git
---


## meld配置
1. 过滤

https://gist.github.com/baymaxium/03d93b888469df6a37d1cf78f8913c76

## meld版本比较
meld不带有版本比较，但是git可以方便的把这个比较启动起来.

1. 先将git的比较工具设为meld
```git
git config --global diff.tool meld
```

2. 比较一个版本时可以使用
```git
git difftool "version1" "version2" -y
```
但上述比较命令只能一个一个文件的比较，会造成很多麻烦，这里可以使用如下方法
```git
git difftool -d "version1" "version2"
```

[参考自](https://stackoverflow.com/questions/2006032/view-differences-of-branches-with-meld)
