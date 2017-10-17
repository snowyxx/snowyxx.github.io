---
layout: post
title:  "debain: halt变成了重启"
date:   2012-09-18 17:09:27 +0800
categories: 笔记
tags:   [linux]
---
不知道为什么执行halt或变成了重启

搜索得到的结果是：

>在/boot/grub/grub.cfg  (grub2) 中：
menuentry "Debian GNU/Linux, with Linux 2.6.32-3-amd64" --class>
然后再找到：linux   /vmlinuz-2.6.32-3-amd64 #这里加上acpi=off  
这样不再重启，但是halt后不能关闭电源，所以改成acpi=force，问题解决。

查看系统（BIOS）是否支持acpi：     
`dmesg |grep -i acpi`

参考：             
[http://lists.debian.org/debian-chinese-gb/2010/04/msg00040.html](http://lists.debian.org/debian-chinese-gb/2010/04/msg00040.html){:target="blank"}                 
[http://blog.sina.com.cn/s/blog_4de32da70100d1pg.html](http://blog.sina.com.cn/s/blog_4de32da70100d1pg.html){:target="blank"}                 
[http://howto.gumph.org/content/poweroff-an-old-pc-in-debian/](http://howto.gumph.org/content/poweroff-an-old-pc-in-debian/)
