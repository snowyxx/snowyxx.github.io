---
layout: post
title:  "Windows7 ftp服务器的防火墙配置"
date:   2011-12-26 10:43:01 +0800
categories: blog
tags:   [windows]
---
启用了**Windows7**自带的FTP服务器，但是一个问题是如果打开Windows自带的防火墙，从远程就连接不上了。即使防火墙中“允许的程序”中添加了FTP服务器；出入站规则中添加启用相关配置也不行。      
网上找了半天，试了一些方法（开启全局ftp设置、添加inetinfo.exe...)都不好使。     
**最后的解决方法：**
在入站规则中添加新纪录，启用20和21端口。然后就可以了。