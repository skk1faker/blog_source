---
title: clash_dashboard使用
date: 2024-04-08 13:58:43
tags:
- clash
---

# 如何设置

到clash文件夹下查看config.yml文件，其中有一个选项为external-controller字段
```yaml
port: 7890
socks-port: 7891
redir-port: 7892
mixed-port: 7893
allow-lan: false
mode: Rule
log-level: info
external-controller: ':9990'
clash-for-android:
    append-system-dns: false

```
这个字段表示了`clash_dashboard`运行的端口。

这时打开网址`http://clash.razord.top`,只要在`外部控制设置`中点击编辑，将端口设置好就行
![](port_setting_posi.png)

![](clash_dashboard_port.png)

点击确定后就可以愉快的使用`clash dashboard`了
