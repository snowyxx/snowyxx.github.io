---
layout: post
title:  "ubuntu启用samba，让windows访问Linux共享文件夹"
date:   2012-11-15 16:46:51 +0800
categories: 笔记
tags:   [linux]
---
1. 安装samba服务器
        `sudo apt-get install samba`
2. 配置文件
    `sudo vim /etc/samba/smb.conf`                  
    以共享用户主目录为例：                 
    [global]部分
    
        security = user
        [homes]部分
        comment = Home Directories
        browseable = no
        read only = no （值为no的时候，共享有写权限，yes的时候不能写）

3. 设置访问samba用户的密码
    `sudo smbpasswd -a 用户名`
4. 启动samba服务
    `sudo /etc/init.d/smbd restart或者sudo service smbd restart`
5. 从Windows中使用\\\\ip的方式访问共享的文件夹

另外：Linux中访问Windows共享的方法:    
        
1. 在文件管理器中的地址栏中输入smb://ip/c$
2. 在终端中输入 smbclient //ip/c$ -U 用户名

共享其他文件夹的参数      
    
    [myshare]
    comment = This is a samba share
    path = /linuxcast-share           共享路径
    valid users = linuxcast nash_su   可用用户
    public = no                       是否为开放共享 
    browsable = yes                   是否可浏览
    writable = yes                    是否可写
    create mask = 0765                默认创建文件权限

samba配置参数检查                 
`testparm`
