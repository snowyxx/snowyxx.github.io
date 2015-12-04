---
layout: post
title:  "debain在启动时不进行磁盘检查"
date:   2011-07-14 11:20:28 21:09:33 +0800
categories: blog
tags:   [linux]
---
debain在运行一段时间或挂载一定次数后在开机的时候就会自检。用如下方法停止：

1. 编辑/etc/fstab文件，把对应分区的最后一个数改成“0”。

        # cat /etc/fstab
        # /etc/fstab: static file system information.
        #
        # <file system> <mount point>   <type>  <options>       <dump>  <pass>
        proc            /proc           proc    defaults        0       0
        /dev/sda1       /               ext3    errors=remount-ro 0       0
        /dev/sda5       none            swap    sw              0       0
        /dev/scd0       /media/cdrom0   udf,iso9660 user,noauto     0       0
        
2. 使用以下命令：          
`#tune2fs -c 0 -i 0d /dev/xxxx`                     
这个命令会把周期检查也停用了。

可以通过以下命令查看跟多磁盘相关信息：             
`#tune2fs -l /dev/xxxx`

**以上两种方法都不建议使用。  ：）**
