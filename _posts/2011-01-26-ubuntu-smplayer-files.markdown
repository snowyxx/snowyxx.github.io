---
layout: post
title:  "ubuntu修改smplayer的格式关联"
date:   2011-01-26 00:05:42 +0800
categories: blog
tags:   [linux]
---
之前在kubuntu中安装了smplayer后文件格式进行了自动关联。但是ubuntu中就没有关联了。smplayer的选项中页面关联格式的。如果选中一个视频文件在属性中修改关联格式非常的麻烦。       
网上找到一个很好的方法很好，记录如下： 
        
- /etc/gnome/defaults.list 保存了全局的打开方式
- /.local/share/applications/mimeapps.list 保存了个人的打开方式

当两着不一致是，优先采用局部的个人设置。

例如，ubuntu8.04下多媒体文件默认是用Totem打开的，如果你想要改成用smplayer（本人比较喜欢用这个）来打开的话，可以按照以下步聚来进行：

- 进入/etc/gnome目录 命令 cd /etc/gnome
- 复制要改变打开方式的那些内容行到mimeapps.list文件 命令 grep “totem” defaults.list >> ~/.local/share/applications/mimeapps.list
- 进入~/.local/share/applications目录 命令 cd ~/.local/share/applications
- 编缉mimeapps.list文件 命令： sudo vim mimeapps.list
- 在vim中执行 命令：%s/totem/smplayer/g
- 然后执行保存命令：wq
- 完成

在我的环境中，第二步中要把双引号改成单引号，即：grep 'totem' defaults.list >> ~/.local/share/applications/mimeapps.list

参考：[http://blog.csdn.net/dadoneo/archive/2010/12/14/6076359.aspx](http://blog.csdn.net/dadoneo/archive/2010/12/14/6076359.aspx){:target="_blank"}