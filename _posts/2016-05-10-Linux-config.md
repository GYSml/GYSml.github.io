---
author: GuoYS
layout: post
title:  "Centos6.5下搭建web服务器/访问web项目"
date:   2016-05-12 20:08:28 +0800
categories: Linux
tags: centos
---
* content
{:toc}

  这篇文章关于在centos下web项目环境的配置流程以及相关注意事项



## 搭建web服务器环境

- 在VM中启动centos的vi编辑器，安装apache

  ```vim
  yum install httpd vsftpd mysql mysql-server php php-mysql
  ```

- 启动http

  ```vim
  法一:/etc/init.d/httpd start
  法二:service httpd start
  ```

  ​


- 设置开机启动

  ```
  chkconfig --levels 235 httpd on
  chkconfig httpd on
  ```

  Apache的默认文档根目录是在CentOS上的/var/www/html 目录   

     ​                       配置文件是/etc/httpd/conf/httpd.conf

  ​

- 设置HTTP服务开机启动

  ```chkconfig httpd on
  chkconfig httpd on
  ```

- 设置FTP服务开机启动

  ```
  chkconfig vsftpd on
  ```

  ​

- 设置MySQL服务开机启动

  ```
  chkconfig mysqld on
  ```

  ​

- 重启服务

  ```
  法一:service httpd restart
  法二:/etc/init.d/httpd restart
  ```

  测试php是否安装成功：

  ```  
  test.php vi /var/www/html/test.php   //test.php文件内容  <?php  phpinfo(); ?>
  ```

  ​

- 启动服务

  ```
  service httpd start   //启动http服务
  service vsftpd start  //启动ftp服务
  service mysqld start  //启动mysql服务
  ```

## 访问本机centos上的web工程  

- 虚拟机自带3种上网方式

  - .NAT
  - Host-Only
  - 桥接

- 打开vm->编辑->虚拟网络编辑器->选择NAT模式->NAT设置

- 在打开的 本机WIN7 的控制面板-->网络和Internet-->网络连接-->界面

  - 右键单击“VMnet1的宽带连接”--->属性---->共享--->允许其他网络通过此计算机连接
  - 设置VMnet8的IP,DNS为自动获取方式
  - 依次鼠标右键VMnet8，属性，点击Internet协议版本4，也就是平常说的IPV4了，然后在里面选中自动获取IP 和 自动获得DNS

- 在本机WIN7中 打开命令行DOS窗口，cmd下执行 ipconfig /all 获取VMware Network Adapter VMnet8网卡的信息

- 进入centos修改网卡信息eth0

  ```
  vi etc/sysconfig/network-scripts/ifcfg-eth0
  GATEWAY="192.168.18.1
  NETMASK="255.255.255.0
  ONBOOT="yes"
  ```

- 重启虚拟网卡

  ```
  service network restart
  ```


## 如果在本机还不能访问虚拟机中的web项目 ，则查看防火墙


- 查看防火墙信息

  ```
  /etc/init.d/iptables status
  ```

  ​

- 启用80,22,3306端口

  ```
  iptables -I INPUT -p tcp  --dport 80 -j ACCEPT
  ```

  ​

- 保存修改

  ```
  /etc/rc.d/init.d/iptables save
  ```

  ​

- 重启防火墙

  ```
  /etc/init.d/iptables restart
  ```

  ​

  ​

##在win浏览器下访问Centos下web项目

- 在浏览器输入地址

  ```.
    http://192.168.18.130/info.php
    该地址可在centos下用vi键入一下指令查看eth0的地址
    ifconfig即可
  ```

  ​

  ​
