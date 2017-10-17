---
layout: post
title:  "ubuntu 更改主目录中的文件名为英文"
date:   2011-01-24 10:48:41 +0800
categories: 笔记
tags:   [linux]
---
打开终端，在终端中输入命令:      
`export LANG=en_US`    
`xdg-user-dirs-gtk-update`

跳出对话框询问是否将目录转化为英文路径,同意并关闭。        
在终端中输入命令:       
`export LANG=zh_CN`

参考：[http://my.oschina.net/myriads/blog/2867](http://my.oschina.net/myriads/blog/2867){:target="_blank"}