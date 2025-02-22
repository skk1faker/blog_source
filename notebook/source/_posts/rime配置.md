---
title: rime配置
tags:
  - rime
date: 2024-05-27 11:13:44
---


[配置教程](https://www.mintimate.cn/2024/04/13/GuideToRime/)

# 所需修改文件，以及一些文件夹的作用是什么

linux下`~/.config/ibus/rime`存放着rime的配置文件，其中：
`default.custom.yaml` 	核心配置、全局配置，可以配置文件
`rime.lua`    设置lua脚本
`symbol.yaml` 设置符号
`dict/custom_simple.dict.yaml`  自定义字典
`rime_mint.dict.yaml`       mint输入法使用的字典
`default.yaml`              介绍整儿rime中有哪些输入法
`rime_mint.schema.yaml`     介绍整个输入法配置方案（例如有哪些字典）

# 做了哪些设置

在default.custom.yaml下设置了如下内容

```yaml
patch:
  "menu/page_size": 9 # 候选词个数
  "ascii_composer/good_old_caps_lock": true
  "ascii_composer/switch_key":
    Caps_Lock: commit_code
    Control_L: noop
    Control_R: noop
    Shift_L: noop
    Shift_R: noop
  # 开启用户词典功能
  "translator/enable_user_dict": true
  key_binder:
    bindings:
      - { when: always, accept: "Control+space", toggle: "ascii_mode" }
```

主要将shift切换中英文关闭了。


[参考Rime 输入法有比较现成的设置吗？ - Mintimate的回答 - 知乎](https://www.zhihu.com/question/20871256/answer/2952986138)

