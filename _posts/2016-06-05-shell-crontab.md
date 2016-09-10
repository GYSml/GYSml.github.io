---
author: GuoYS
layout: post
title:  "shell脚本-定时执行任务"
date:   2016-05-12 20:08:28 +0800
categories: Linux
tags: centos
---
* content
  {:toc}

  在linux系统下，我们经常需要定时执行一些固定的任务，比如说服务器每天定时将数据进行备份，但是又不希望人工来操作，因为这样不仅效率低而且又浪费人力资源，我们可以使用shell脚本来配合crontab命令实现定时任务



##centos重启命令：

- reboot

- shutdown -r now 立刻重启(root用户使用)

- shutdown -r 10 过10分钟自动重启(root用户使用)

- shutdown -r 20:35 在时间为20:35时候重启(root用户使用)

##Linux centos关机命令：

- halt 立刻关机

- poweroff 立刻关机

- shutdown -h now 立刻关机(root用户使用)

- shutdown -h 10 10分钟后自动关机

- 如果是通过shutdown命令设置关机的话，可以用shutdown -c命令取消重启。

##实现定时任务crontab

- 查看当前是否有定时任务

```
crontab -l
```

- 删除定时任务


```
crontab -r
```

- 每个一分钟向/CCoder/aaa.txt写入aaaaa数据

```
*/1 * * * * echo "aaaaa" >> /CCoder/aaa.txt
```



-	定时执行脚本

  `test.sh`

  	   #! /bin/bash
  	   echo "This is Timer" >> /CCoder/aaa.txt
  	   exit 0


-	创建定时任务，注意：在执行shell脚本时必须使用绝对路径	

  `crontab -e`

  ​	编辑内容：`*/1 * * * * sh /CCoder/test.sh`

##crontab命令介绍

-	基本格式：

  ​	`*　 *　 *　 * 　* command　`　

  ​	分别表示分钟，小时，日期，月份，星期几，command表示需要执行的命令

  ​	`32 20 * * * /usr/local/etc/rc.d/lighttpd restart`

  ​	表示每天晚上的20:30重新启动Apache

  ​	

  ​		



