---
layout: post
title:  "Windows 2008r2 集群部署"
date:   2014-07-30 10:13:04 +0800
categories: blog
tags:   [windows]
---
##主机：
- 域控：192.168.0.60 
- 节点1：192.168.0.61（public），192.168.33.1（存储）
- 节点2：192.168.0.62（public），192.168.33.2（存储）
- 存储主机：192.168.33.3（可以和域控放到一个主机上）

##步骤：
1. 域控上安装dns服务器角色
2. 域控上运行dcpromo配置域
3. 节点1，节点2：更改主机名并加入域
4. 存储主机安装 iSCSI Software Target 3.3（微软官网下载，免费）。
5. 存储主机配置iSCSI Software Target，添加2个iSCSI目标并分配添加虚拟磁盘：Quorum（500MB）Share（10240MB）；iSCSI发起程序的标识可以使用IP地址，即2个节点主机的IP地址。
6. 节点1，节点2：管理工具> iSCSI发起程序，目录页签中输入存储主机IP，选择连接已发现的目标。这时要选中“启用多路径”。
7. 在节点1，节点2任意之一的磁盘管理中格式化2个磁盘。
8. 节点1，节点2：安装“故障转移群集”功能。
9. 节点1，节点2任意之一：管理工具>故障转移群集管理器，验证配置，如果没有红色的错误可以进行下一步。
（如果是克隆的虚拟机，这里验证不能通过，需要修改系统SID，时间上不如重新安装2个系统快）
10. 节点1，节点2任意之一：管理工具>故障转移群集管理器，创建集群。
11. 集群创建完成后如果仲裁磁盘不是你需要的，在集群上右键>更多操作>配置集群仲裁，选择你的仲裁磁盘。
12. 集群创建完成后就可以部署应用了。

参考：             
[http://dufei.blog.51cto.com/382644/932019](http://dufei.blog.51cto.com/382644/932019){:target="_blank"}            
[http://yes-blogger.blogspot.com/2011/09/iscsi-software-target-33-sop.html](http://yes-blogger.blogspot.com/2011/09/iscsi-software-target-33-sop.html){:target="_blank"}            
[http://download.microsoft.com/download/2/F/1/2F110B9C-6CB0-45F1-971B-4BA37395D820/iscsiTargetqfe6.exe](http://download.microsoft.com/download/2/F/1/2F110B9C-6CB0-45F1-971B-4BA37395D820/iscsiTargetqfe6.exe){:target="_blank"}