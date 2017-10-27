---
layout: post
title:  "从Windows使用Linux的主机名称访问"
date:   2011-10-17 11:37:31 +0800
categories: blog
tags:   [linux]
---
使用smb配置文件，这样不用在dns服务器上做配置，是一个简单的绕行方法
在**/etc/samba/smb.conf**添加**netbios name = xxxx**
重新启动smb服务后就可以了