---
layout: post
title:  "wget下载文件及子目录"
date:   2015-05-06 16:21:05 +0800
categories: 笔记
tags:   [linux]
---
`wget -c -r -np -k -L -p http://xxx.x.x.xxx.com/xxx/`

使用代理：

    wget -c -r -np -k -L -p -e use_proxy=yes -e http_proxy=127.0.0.1:8080  http://xxx.x.x.xxx.com/xxx/

如果代理是pac，要分析一下pac文件来找到代理地址和端口