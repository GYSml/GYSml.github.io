## 			shell脚本-定时执行任务

1、centos重启命令：

​	（1）reboot

​	（2）shutdown -r now 立刻重启(root用户使用)

​	（3）shutdown -r 10 过10分钟自动重启(root用户使用)

​	（4）shutdown -r 20:35 在时间为20:35时候重启(root用户使用)

2.Linux centos关机命令：

​	（1）halt 立刻关机

​	（2）poweroff 立刻关机

​	（3）shutdown -h now 立刻关机(root用户使用)

​	（4）shutdown -h 10 10分钟后自动关机

​		如果是通过shutdown命令设置关机的话，可以用shutdown -c命令取消重启。

3.实现定时任务crontab

​	（1）查看当前是否有定时任务

​		  `crontab -l`

​	（2） 删除定时任务

​		   `crontab -r`

​	（3 ）每个一分钟向/CCoder/aaa.txt写入aaaaa数据

  		   ` */1 * * * * echo "aaaaa" >> /CCoder/aaa.txt`

4.定时执行脚本

​	（1） 脚本`test.sh`

		   #! /bin/bash
		   echo "This is Timer" >> /CCoder/aaa.txt
		   exit 0
​	（2）创建定时任务，注意：在执行shell脚本时必须使用绝对路径

​		`crontab -e`

​		编辑内容：`*/1 * * * * sh /CCoder/test.sh`

5.crontab命令介绍

​	  基本格式：

​		`*　 *　 *　 * 　* command　`　

​		分别表示分钟，小时，日期，月份，星期几，command表示需要执行的命令

​		`32 20 * * * /usr/local/etc/rc.d/lighttpd restart`

​		表示每天晚上的20:30重新启动Apache

​		

​			



