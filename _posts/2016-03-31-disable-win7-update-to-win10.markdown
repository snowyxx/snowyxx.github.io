---
layout: post
title:  "禁止Windows 7更新到Windows 10"
date:   2016-03-31 11:11:05 +0800
categories: blog
tags:   [windows]
---

### 相关补丁

更新到Windows 10对应补丁是KB3035583。在Windows Update中把该补丁隐藏，这样就不安装了。

![diswin72win10-hidepatch](/images/diswin72win10-hidepatch.png)

如果已经安装了，到控制面板 > 系统和安全 > Windows Update > 查看更新历史 > 以安装的更新，找到这个补丁并卸载。

![diswin72win10-uninstallpatch](/images/diswin72win10-uninstallpatch.png)

> 这个补丁安装之后会在c:\windows\system32中创建GWX（get Widnwos 10)文件夹。

### 禁止自动升级

运行gpedit.msc打开组策略。计算机配置 > 管理模版 > Windows组件 > Windows Update。找到“Turn off the upgrade to the latest version of Windows through Windows Update”, 启用该项。

![diswin72win10-gpedit](/images/diswin72win10-gpedit.png)

启用该项后，对应的注册表设置为： 

    Windows Registry Editor Version 5.00

    [HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate]
    "DisableOSUpgrade"=dword:00000001

