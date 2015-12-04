---
layout: post
title:  "Installshield指定jvm"
date:   2011-04-26 16:24:51 +0800
categories: blog
tags:   [linux,jvm]
---
在Linux中一些installshield制作的安装程序会出现中文乱码的问题。（如在我的ubuntu中安装了ManageEngine的程序）。
 
解决方法是指定jvm，如：
`./xxx.bin -is:javahome /opt/jdkxx...`