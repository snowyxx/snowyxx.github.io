---
layout: post
title:  "修改debian终端登录的欢迎信息/etc/motd"
date:   2013-05-16 09:52:29 +0800
categories: 笔记
tags:   [linux]
---
在命令行登录到debian的时候显示的欢迎信息是**/etc/motd**，但是motd文件在系统重启的时候被重写。
因为motd是**/var/run/motd**的链接。
要修改的方法是删除**/etc/motd**，创建正常的文件，写入需要的内容。

参考：[http://wiki.debian.org/motd](http://wiki.debian.org/motd){:target="_blank"}