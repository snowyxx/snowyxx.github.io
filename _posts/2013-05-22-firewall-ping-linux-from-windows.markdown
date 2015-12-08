---
layout: post
title:  "由于从windows防火墙不能从Linux ping的解决方法"
date:   2013-05-22 10:07:56 +0800
categories: blog
tags:   [other]
---
使用Windows 7默认的防火墙，虽然已经允许“文件和打印机共享”了，但是还是不能从Linux（另个一个网段）ping通。当关闭防火墙的时候，就可以ping。

命令可以解决：

    netsh firewall set icmpsetting 8
    netsh firewall set icmpsetting 8 disable

参考：[http://www.iamyoso.com/?p=425](http://www.iamyoso.com/?p=425){:target="_blank"}