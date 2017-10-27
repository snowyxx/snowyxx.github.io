---
layout: post
title:  "ubuntu- PostgreSQL安装和卸载"
date:   2011-07-20 12:58:27 +0800
categories: blog
tags:   [linux,postgresql]
---
# 安装：

`sudo apt-get install postgresql`

# 设置密码

`sudo -u postgres psql `

    postgres=# ALTER USER postgres WITH PASSWORD ‘postgres’;
    postgres=# \q

`sudo passwd -d postgres`

`sudo -u postgres passwd`

更改配置文件

`sudo vim /etc/postgresql/8.4/main/postgresql.conf`

    ​listen_addresses = ‘*’ 
    ​password_encryption = on
    ​sudo vim /etc/postgresql/8.4/main/pg_hba.conf 
    ​host all all 0.0.0.0/0 md5

`sudo /etc/init.d/postgresql restart`

# 测试连接

`psql -U postgres -h 127.0.0.1`

# 安装GUI管理工具

`sudo apt-get install pgadmin3`

# 卸载

`dpkg --list | grep postgresql` 

`dpkg --purge <insert package here>`

以后重新安装使用aptitude安装PostgreSQL，可以重新获得所有配置文件。

参考：[http://www.cnblogs.com/ctou45/archive/2011/05/26/2057852.html](http://www.cnblogs.com/ctou45/archive/2011/05/26/2057852.html){:target="_blank"}

          [http://zh-cn.w3support.net/index.php?db=su&id=76495](http://zh-cn.w3support.net/index.php?db=su&id=76495){:target="blank"}

