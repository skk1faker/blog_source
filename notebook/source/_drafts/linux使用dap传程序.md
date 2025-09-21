---
title: linux使用dap传程序
tags:
- stm32
---

[参考自](https://blog.syize.cn/2023/11/08/linux-stm32-development/)

1. 先找到相关相关usb设备的信息
2. 保存相关信息
3. 使用openocd进行烧录

	
```
openocd -f /usr/share/openocd/scripts/interface/cmsis-dap.cfg -c "transport select swd" -f Drivers/openocdcfg/stm32f1x.cfg -c "init" -c "reset halt" -c "sleep 100" -c "wait_halt 2" -c "flash write_image erase build/STM32LED.elf" -c "sleep 100" -c "reset run" -c shutdown
```

其中

- -f /usr/share/openocd/scripts/interface/cmsis-dap.cfg：由于我用的调试器使用CMSIS-DAP标准，所以配置文件选择cmsis-dap.cfg
- -c "transport select swd"：使用SWD模式进行数据传输
- -f Drivers/openocdcfg/stm32f1x.cfg：使用stm32f1x类型芯片的配置文件。此文件是我从/usr/share/openocd/scripts/target下复制过来的
- -c "init" ：初始化
- -c "reset halt"：执行硬件复位，将所有已定义的目标重置，并立即停止目标的运行
- -c "sleep 100"：OpenOCD在状态恢复之前等待至少100毫秒
- -c "wait_halt 2"：OpenOCD等待目标停止的时间为2毫秒
- -c "flash write_image erase build/STM32LED.elf"：擦出flash扇区并将build/STM32LED.elf文件烧录到其中
- -c "reset run"：执行硬件复位，将所有已定义的目标重置，并开始目标的运行
- -c shutdown：关闭OpenOCD

可以将以上的命令写进一个shell脚本中，方便后续重复运行。然后我发现烧录的时候，烧录bin文件是有问题的，烧录不进去，但是烧录elf文件就是正常的，这个问题比较奇怪，还没有找到原因。
