---
layout: post
title:  "Windows 10的默认共享访问"
date:   2021-01-26 15:50:05 +0800
categories: blog
tags:   [windows, 共享, 1073741412]
---

陪伴我的12多年的Windows 7系统出问题不能启动了，所以就安装了新的系统，选择了Windows 10 20H2_v2_Chinese。

使用新系统遇到个问题，不能从远程挂载Windows的默认共享盘了：

```
mount_smbfs //myname@192.168.0.258/d$ ~/258d
Password for 192.168.0.258:
mount_smbfs: mount error: /Users/myname/258d: Unknown error: -1073741412
```

首先想到的是Windows 10系统的上的防火墙设置，反复测试访问都没有效果。

下一步尝试使用修改挂载命令，也都没有效果。这期间在本地的账户设置上浪费了好多时间。

后来发现，从其他的Windows也不能访问这个Windows 10的默认共享。所以终于找到了解决问题的方向：“Windows 10默认共享访问的问题”。

解决方法：

- Windows 10只能用_administrator_用户访问。其他用户即使在administrators组也不行。那么就启用administrator，用户其访问即可。

- 如果想让其他用户可以访问默认共享。在该Windows 10上做如下设置：

    1.打开注册表（win+r输入regedit回车）
    2.打开HKEY\_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System
    3.右击右面空白处并新建DWORD (32 位)
    4.把新建的命名为LocalAccountTokenFilterPolicy
    5.双击把它的值改为1