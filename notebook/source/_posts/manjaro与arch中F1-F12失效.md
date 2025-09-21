---
title: manjaro与arch中F1-F12失效
date: 2025-07-26 09:51:50
tags:
---


如果F1~F12和Fn都失效了,那么可能是文件`/sys/module/hid_apple/parameters/fnmode`内容不对, 其三种值代表如下

|值|含义|用途|
|---|---|---|
|0|disable|废除fn,f1~f12|
|1|fkeyslast|功能键F1-F12本身的功能靠后，也就是默认一直按下Fn键|
|2|fkeysfirst|默认使用F1-F12键本身的功能，要用实现特殊功能需要先按Fn再按F1-F12|

需要F1-F12,Fn那么一定要设置模式为2.

1. 永久设置

```bash
cd /etc/modprobe.d
touch hid_apple.conf
echo options hid_apple fnmode=2 > hid_apple.conf
# 重启
```

2. 临时设置(直接改那个文件)

```
 cd /sys/module/hid_apple/parameters
 cat fnmode
 # 如果结果是1 需要修改
 echo 2 > fnmode
 # 修改后F1-F12功能键生效
```

参考[](https://blog.csdn.net/m0_46238775/article/details/127097605)
