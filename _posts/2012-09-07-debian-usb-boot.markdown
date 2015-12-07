---
layout: post
title:  "debian usb启动盘的制作方法"
date:   2012-09-07 13:14:49 +0800
categories: blog
tags:   [linux]
---
1 按照[debian用户手册](http://www.debian.org/releases/stable/i386/ch04s03.html.en){:target="blank"}中的方法 （制作的USB盘在我的环境中没有成功）

    4.3.1. Preparing a USB stick using a hybrid CD or DVD image
    # cat debian.iso > /dev/sdX
    # sync
    
2. 使用[Universal-USB-Installer](http://www.pendrivelinux.com/debian-live-flash-drive-install-from-windows/#more-2073){:target="blank"} （在我的环境中可用）

   这个好像只能做live的。。。