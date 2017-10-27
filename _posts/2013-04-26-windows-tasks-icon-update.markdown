---
layout: post
title:  "Windows任务栏中的chrome图标变成qq的了"
date:   2013-04-26 09:49:25 +0800
categories: blog
tags:   [other]
---
不知为啥Windows任务栏中的chrome图标变成qq的了
网上找到个脚本删除图标缓存来恢复图标。。。（不好使！可能是用来恢复显示不出来的图标的）

    @echo off
    taskkill /f /im explorer.exe
    attrib -h-s-r "%userprofile%\AppData\Local\IconCache.db"
    del /f "%userprofile%\AppData\Local\IconCache.db"
    attrib /s /d -h -s -r "%userprofile%\AppData\Local\Microsoft\Windows\Explorer\*"
    del /f "%userprofile%\AppData\Local\Microsoft\Windows\Explorer\thumbcache_32.db"
    del /f "%userprofile%\AppData\Local\Microsoft\Windows\Explorer\thumbcache_96.db"
    del /f "%userprofile%\AppData\Local\Microsoft\Windows\Explorer\thumbcache_102.db"
    del /f "%userprofile%\AppData\Local\Microsoft\Windows\Explorer\thumbcache_256.db"
    del /f "%userprofile%\AppData\Local\Microsoft\Windows\Explorer\thumbcache_1024.db"
    del /f "%userprofile%\AppData\Local\Microsoft\Windows\Explorer\thumbcache_idx.db"
    del /f "%userprofile%\AppData\Local\Microsoft\Windows\Explorer\thumbcache_sr.db"
    echo y|reg delete "HKEY_CLASSES_ROOT\Local Settings\Software\Microsoft\Windows\CurrentVersion\TrayNotify" /v IconStreams
    echo y|reg delete "HKEY_CLASSES_ROOT\LocalSettings\Software\Microsoft\Windows\CurrentVersion\TrayNotify" /v PastIconsStream
    start explorer

**最后恢复的方法是**:           
首先把chrome重任务栏中解锁。然后在chrome的桌面图标右键中选择属性中的兼容性选择Win XP SP3，启动一次，图标正常了。把图标锁定到任务栏。然后关闭Chrome取消兼容性，再启动好了。

**补充**
新看到的: [http://yonsm.net/resolve-windows-7-start-menu-and-taskbar-icon-error/](http://yonsm.net/resolve-windows-7-start-menu-and-taskbar-icon-error/){:target="_blank"}