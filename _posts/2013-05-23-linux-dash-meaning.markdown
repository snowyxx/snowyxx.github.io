---
layout: post
title:  "linux 命令中使用减号（-）来代替标准输入输出"
date:   2013-05-23 11:22:11 +0800
categories: blog
tags:   [linux]
---
看到这样的命令：

Server          
`$tar -cvf – dir_name | nc -l 1567`

Client              
`$nc -n 172.31.100.7 1567 | tar -xvf -`

这个是通过nc命令在网络上传输文件的。但是不知道其中减号的作用。。。。
调查发现：**当用减号（-）代替文件名的时候表示标准输入或标准输出**

例如：`tar -cvf - /home/log |tar -xvf -`               
等同于：`cp /home/log .`            
第一个 - 表示打包到标准输出中，第二个 - 表示从标准输入中读取，中间使用通道来传输。