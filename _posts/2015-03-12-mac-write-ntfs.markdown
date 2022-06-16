---
layout: post
title:  "mac写入NTFS移动硬盘"
date:   2015-03-12 17:19:59 +0800
categories: blog
tags:   [mac]
---
以前使用 Paragon NTFS

但是最近发现在yosemite下升级到11版本也不行。

最后使用一种非常简单的手动方式

1. 查看该NTFS分区的卷标。可以在Windows下看，也可以在Finder中看。
2. `sudo vim /etc/fstab`
3. 添加：`LABEL=你的卷标名 none ntfs rw,auto,nobrowse`
4. 重新插入移动硬盘
5. 转到/Volumes/你的卷标名 （Finder中使用shift+cmd+g）就可以写入了。

2022-6-16 更新 

以上方法在macOS 12.4 Monterey 上不灵。找到了个免费的方法：[mounty](https://mounty.app/)

安装： `brew install --cask mounty`