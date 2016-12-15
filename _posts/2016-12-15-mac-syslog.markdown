---
layout: post
title:  "Mac中配置syslog"
date:   2016-12-15 14:38:05 +0800
categories: blog
tags:   [mac]
---
<!-- 
* 目录
{:toc} -->

记录在Mac配置发送syslog到syslog服务器的方法：

__配置syslog服务器地址：__

`sudo vi /etc/syslog.conf`

添加一行:

`*.*                                       @192.168.1.12:514`

> 表示发送所有syslog到192.168.1.12的udp 514端口。

__检查Mac是否运行syslogd__

`ps -e | grep syslogd`

__重新启动syslogd__

```shell
sudo launchctl unload /System/Library/LaunchDaemons/com.apple.syslogd.plist
sudo launchctl load /System/Library/LaunchDaemons/com.apple.syslogd.plist
```

__抓包__

`sudo tcpdump -i en1 host 192.168.1.12 and udp port 514`

__生成一条syslog__

`logger -s -p user.info Testing splunk syslog forwarding`

在本地查看syslog

`tail /var/log/system.log`

---

参考：                                   

- [HowTo Configure Mac OS X Syslog To Forward Data](https://wiki.splunk.com/Community:HowTo_Configure_Mac_OS_X_Syslog_To_Forward_Data)                   
