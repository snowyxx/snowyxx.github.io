---
layout: post
title:  "ubuntu- mysql安装和简单配置"
date:   2011-03-16 15:58:23 +0800
categories: blog
tags:   [linux,mysql]
---
纯笔记：

#安装客户端和服务器
`sudo apt-get install mysql-server-5.1 mysql-client-5.1`

#安装后自动启动，不喜欢，去掉
`sudo update-rc.d -f mysql remove`

#哈哈，可是为什么执行了上面的命令后，mysql还是会启动呢？在ubuntu 10.04后会这样,使用下面的方法
`vim /etc/init.d/mysql.conf`

         start on (net-device-up
         and local-filesystems)
         #and runlevel [2345])
         stop on runlevel [0123456]

#服务启动
`sudo /etc/init.d/mysql start`

#可以远程用root用户连接
- 首先`vim /etc/mysql/my.cnf`
- 修改**“bind-address = 127.0.0.1”**为**“bind-address = 192.168.0.91”**
- 然后用客户端连接`mysql -u root -p`
- 执行下面的命令               
`GRANT ALL PRIVILEGES ON *.* TO root@"%" IDENTIFIED BY "123456";`
- 重新启动mysql(sudo /etc/init.d/mysql restart)即可

#修改字符集
编辑*/etc/mysql/my.cnf*
在**[mysqld]**加上     
`default-character-set=utf8 `           
客户端可以用`set users 'utf8'`       
查看使用的字符集：`show variables like "%char%";`        