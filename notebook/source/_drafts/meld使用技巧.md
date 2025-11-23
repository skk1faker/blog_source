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

3. 如果多个文件比较，则加上--dir-diff， 就会在一个目录中把所有需要比较的文件加上了
```git
git difftool --dir-diff &
```

4. meld会检测当前打开目录是否是git,如果是的话，则会直接节进行git比较操作。

```
meld .

```



[参考自](https://stackoverflow.com/questions/2006032/view-differences-of-branches-with-meld)
