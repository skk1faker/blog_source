---
title: manjaro配置
date: 2025-07-26 08:12:11
tags:
---

# manjaro/arch系统配置
## 休眠与挂起
休眠与挂起区别是，休眠将进程放入交换内存，挂起进程放在物理内存。也就是挂起需要通电保证物理内存有电，但是休眠不需要。

manjaro中挂起和休眠都配置好了，只需要调用如下命令
```bash
# 休眠
systemctl hibernate     

# 挂起
systenctl suspend
```

在i3的配置中加入如下命令

```bash
bindsym $mod+l exec /home/skt1faker/configRepo/lock/sleep
```

其中sleep脚本存放的是
```bash
#!/bin/bash
systemctl hibernate
```

# i3配置

[i3wm配置教程](https://zjuyk.site/i3wm-userguide-zh/%E4%BB%8B%E7%BB%8D.html)

