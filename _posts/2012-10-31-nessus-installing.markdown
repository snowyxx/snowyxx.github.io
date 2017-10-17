---
layout: post
title:  "nessus安装记录"
date:   2012-10-31 16:23:22 +0800
categories: 笔记
tags:   [other]
---
1. 到官网下载
2. 安装   

麻烦的是plugin的下载，我直接下载失败，所以选择offline方式

到安装目录运行`nessus-fetch --challenge`生成**挑战码**                  
到官网注册激活码
按照_https://ip:8443_提示打开offline的页面，输入以上两个代码获得polugin的下载地址                
![nessus](/images/nessus.png)
按照上面的提示把nessus-fetch.rc放到相应的目录中         
下载的plugin文件为all-2.0.tar.gz，使用方法是运行`nessus-update-plugins.exe <path>\all-2.0.tar.gz`