---
title: clash配置方法
date: 2024-03-11 10:40:34
tags: 
- 代理
---

订阅地址后面加&client=clash，同时订阅的命令使用`wget -O /home/skt1faker/VPN/clash/config.yaml "[订阅地址]&client=clash"`，例如：
`wget -O /home/skt1faker/VPN/clash/config.yaml "https://getnode.dksb.store/subscribe/152449/TBfADuRZNOTJ&client=clash"`

注意最后修改`/usr/bin/renew_subcribe`中的订阅链接
