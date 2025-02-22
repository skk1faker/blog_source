---
title: linux定时任务
tags:
- linux
---

# 参考
[linux工具](https://linuxtools-rst.readthedocs.io/zh-cn/latest/tool/crontab.html)  
[runoob定时任务](https://www.runoob.com/w3cnote/linux-crontab-tasks.html)  

这里使用`notify-send`来实现crontab的演示

# 开启cron服务

首先要开启cron服务才能使用crontab命令，使用`systemctl status cron`检查cron服务的状态，启动状态应为

``` bash
$ systemctl status cron                                            
● cron.service - Regular background program processing daemon
     Loaded: loaded (/lib/systemd/system/cron.service; enabled; vendor preset: enable>
     Active: active (running) since Mon 2024-05-13 18:11:43 HKT; 7h left
       Docs: man:cron(8)
   Main PID: 1286 (cron)
      Tasks: 1 (limit: 18386)
     Memory: 4.2M
     CGroup: /system.slice/cron.service
             └─1286 /usr/sbin/cron -f

Warning: some journal files were not opened due to insufficient permissions.
```

如果没有启动可以使用`systemctl enable cron`使得开启可以启动

# 添加任务

添加任务有两种方式，使用`crontab -e `或`crontab 周期任务文件`。

1. `crontab -e`直接编辑文件，文件暂时在/tmp中，也就是说，这个文件在crontab执行任务时并不重要，只是用来传递需要执行的任务和执行周期。

例如我在终端输入`crontab -e`后，选择vim编辑器书写周期执行的命令, 

```crontab
# Edit this file to introduce tasks to be run by cron.
# 
# Each task to run has to be defined through a single line
# indicating with different fields when the task will be run
# and what command to run for the task
# 
# To define the time you can provide concrete values for
# minute (m), hour (h), day of month (dom), month (mon),
# and day of week (dow) or use '*' in these fields (for 'any').
# 
# Notice that tasks will be started based on the cron's system
# daemon's notion of time and timezones.
# 
# Output of the crontab jobs (including errors) is sent through
# email to the user the crontab file belongs to (unless redirected).
# 
# For example, you can run a backup of all your user accounts
# at 5 a.m every week with:
# 0 5 * * 1 tar -zcf /var/backups/home.tgz /home/
# 
# For more information see the manual pages of crontab(5) and cron(8)
# 
# m h  dom mon dow   command

*/1 * * * * echo "shjfafhsakf"|mail -s "linux-notify" a@163.com

```
最后一行为输入的执行时间 + 命令，可以结束后保存可以周期性的执行命令。当前命令是每隔一分钟发送给a@163.com一封邮件，观察邮件列表可以发现每隔一分钟接收到了一封邮件

![](linux_mail_test.png)

设置完corntab的任务后，再次使用crontab -e会继续上次的编辑内容添加命令。

2. `crontab 周期任务文件`，这个周期任务文件同样不重要，内容同上文件形式即可。

设定周期命令的方式是周期 + 命令，这个周期形式为"分是日月周"，具体如下：
```
分 时 日 月 星期 要运行的命令

    第1列分钟0～59
    第2列小时0～23（0表示子夜）
    第3列日1～31
    第4列月1～12
    第5列星期0～7（0和7表示星期天）
    第6列要运行的命令
```

而时间的设定也有很多技巧
```
    * 取值范围内的所有数字
    / 每过多少个数字
    - 从X到Z
    ，散列数字
```
举几个例子
1. `* * * * * myCommand`表示每分钟都执行myCommand
2. `3,15 * * * * myCommand`表示没小时的第3和第15分钟执行，这里使用了散列技巧
3. `3,15 8-11 * * * myCommand`表示每天8-11时的第3和第15分钟执行，这里使用了'-';
4. `3,15 8-11 */2  *  * myCommand`表示每隔两天的上午8-11点的第3和15分钟执行command  
5. `45 4 1,10,22 * * /etc/init.d/smb restart`表示每个月的1，10，22号的4.45分执行smb的重启
6. `0 23-7/1 * * * /etc/init.d/smb restart`晚上23-早上7点每一小时重启一次smb

# 查看任务

`crontab -l`可以输出任务列表， 也可以将任务内容重定向到其他文件中进行副本保存。

```bash
skt1faker@nwpu ~/rm
master* $ crontab -l > test                                                [11:10:51]
(homework)
skt1faker@nwpu ~/rm
master* $ vim test                                                         [11:10:55]
(homework)
skt1faker@nwpu ~/rm
master* $ cat test                                                         [11:11:00]
# Edit this file to introduce tasks to be run by cron.
#
# Each task to run has to be defined through a single line
# indicating with different fields when the task will be run
# and what command to run for the task
#
# To define the time you can provide concrete values for
# minute (m), hour (h), day of month (dom), month (mon),
# and day of week (dow) or use '*' in these fields (for 'any').
#
# Notice that tasks will be started based on the cron's system
# daemon's notion of time and timezones.
#
# Output of the crontab jobs (including errors) is sent through
# email to the user the crontab file belongs to (unless redirected).
#
# For example, you can run a backup of all your user accounts
# at 5 a.m every week with:
# 0 5 * * 1 tar -zcf /var/backups/home.tgz /home/
#
# For more information see the manual pages of crontab(5) and cron(8)
#
# m h  dom mon dow   command

*/1 * * * * echo "shjfafhsakf"|mail -s "linux-notify" skk1faker@163.com
(homework)
skt1faker@nwpu ~/rm
```

# 删除任务

`crontab -r`可以删除列表中所有任务。可以再删除之前使用`crontab -l > temp`将任务列表保存，

# 指定用户

```
crontab [-u username]　　　　//省略用户表表示操作当前用户的crontab
    -e      (编辑工作表)
    -l      (列出工作表里的命令)
    -r      (删除工作作)
```
使用-u即可指定设定的用户，不指定时默认用户为当前用户。

# 一些问题

1. 如果定时任务不能正确执行python，可能因为Python执行文件不是绝对路径
