---
layout: post
title:  "ubuntu使用bin光盘镜像"
date:   2011-02-10 10:26:51 +0800
categories: 笔记
tags:   [linux]
---

在ubuntu使用bin光盘镜像方法是把bin/cue文件使用**bchunk**转换成为iso文件。         
还可以使用CDemu等虚拟方法，因为我的bin镜像比较少，就不试用这种方法了。。。。

    sudo apt-get install bchunk
    bchunk xxx.bin xxx.cue xxx.iso
    sudo mount -o loop xxx.iso /xxx/xxx
    sudo umount xxx/xxx

注：**mount 里的选项 `-o loop`是指需要使用 “loopback device”（环路设备）。所谓“loopback device” 指的就是拿文件来模拟块设备。**