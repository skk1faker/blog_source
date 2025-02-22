---
title: vim切换normal模式下的中英文切换
tags:
  - vim
date: 2024-05-27 11:38:40
---


#　vim插件可以实现切换到normal模式和其他模式时的中英文自动切换

[插件地址](https://github.com/h-youhei/vim-ibus)

nvim中使用`use("h-youhei/vim-ibus")`将插件安装,之后在配置中使用
```lua
let g:ibus#layout = 'xkb:us::eng'
let g:ibus#engine = 'rime'          
```
g:ibus#engine的值使用`ibus engine`可以得到

```bash
(homework)
skt1faker@nwpu ~/website/notebook/source/_drafts
master* $ ibus engine                                                      [11:36:32]
xkb:us::eng

(homework)
skt1faker@nwpu ~/website/notebook/source/_drafts
master* $ ibus engine                                                      [11:36:37]
rime
```

两个命令前后是我切换输入法的结果。
