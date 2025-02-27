---
title: grub_背景图片设置
tags:
---

# grub添加背景图片
```bash
# If you change this file, run 'update-grub' afterwards to update
# /boot/grub/grub.cfg.
# For full documentation of the options in this file, see:
#   info -f grub -n 'Simple configuration'

GRUB_DEFAULT=0
GRUB_TIMEOUT_STYLE=menu
GRUB_TIMEOUT=10
GRUB_DISTRIBUTOR=`lsb_release -i -s 2> /dev/null || echo Debian`
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"
GRUB_CMDLINE_LINUX=""
GRUB_BACKGROUND="/home/skt1faker/Picture/acm-icpc.png"

# Uncomment to enable BadRAM filtering, modify to suit your needs
# This works with Linux (no patch required) and with any kernel that obtains
# the memory map information from GRUB (GNU Mach, kernel of FreeBSD ...)
#GRUB_BADRAM="0x01234567,0xfefefefe,0x89abcdef,0xefefefef"

# Uncomment to disable graphical terminal (grub-pc only)
#GRUB_TERMINAL=console

# The resolution used on graphical terminal
# note that you can use only modes which your graphic card supports via VBE
# you can see them in real GRUB with the command `vbeinfo'
#GRUB_GFXMODE=640x480

# Uncomment if you don't want GRUB to pass "root=UUID=xxx" parameter to Linux
#GRUB_DISABLE_LINUX_UUID=true

# Uncomment to disable generation of recovery mode menu entries
#GRUB_DISABLE_RECOVERY="true"

# Uncomment to get a beep at grub start
#GRUB_INIT_TUNE="480 440 1"
GRUB_DISABLE_OS_PROBER=false
```
背景图片的关键字是`GRUB_BACKGROUND`只要修改这个关键字就可以，之后保存。在终端中输入`sudo update-grub`更新即可


```bash
skt1faker@nwpu ~
$ sudo update-grub                                                          [19:59
Sourcing file `/etc/default/grub'
Sourcing file `/etc/default/grub.d/init-select.cfg'
Generating grub configuration file ...
Found background: /home/skt1faker/Picture/acm-icpc.png
Found background image: /home/skt1faker/Picture/acm-icpc.png
Found linux image: /boot/vmlinuz-5.15.0-107-generic
Found initrd image: /boot/initrd.img-5.15.0-107-generic
Found linux image: /boot/vmlinuz-5.15.0-106-generic
Found initrd image: /boot/initrd.img-5.15.0-106-generic
Found linux image: /boot/vmlinuz-5.13.0-51-generic
Found initrd image: /boot/initrd.img-5.13.0-51-generic
Found linux image: /boot/vmlinuz-5.11.0-34-generic
Found initrd image: /boot/initrd.img-5.11.0-34-generic
Found linux image: /boot/vmlinuz-5.11.0-27-generic
Found initrd image: /boot/initrd.img-5.11.0-27-generic
Found linux image: /boot/vmlinuz-5.11.0-25-generic
Found initrd image: /boot/initrd.img-5.11.0-25-generic
Found Windows Boot Manager on /dev/nvme0n1p1@/efi/Microsoft/Boot/bootmgfw.efi
Found Ubuntu 20.04.4 LTS (20.04) on /dev/nvme0n1p6
Adding boot menu entry for UEFI Firmware Settings
```
