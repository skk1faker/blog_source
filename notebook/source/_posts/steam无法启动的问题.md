---
title: steam无法启动的问题
tags:
  - 其他
date: 2024-05-27 11:14:06
---


# 终端报错

终端输入steam后报错如下：
```bash
cannot update snap namespace: cannot expand mount entry (none $HOME/.local/share none x-snapd.kind=ensure-dir,x-snapd.must-exist-dir=$HOME 0 0): cannot use invalid home directory "/home/skt1faker": permission denied
snap-update-ns failed with code 1
```

这种问题可能是由于软件更新导致了home目录权限除了问题，需要将`chown -R skt1faker:skt1faker /home/skt1faker` jin xing xiu gai 
