---
layout: post
title:  "vi在打开只读文件后用sudo改为可写"
date:   2013-02-04 12:21:35 +0800
categories: 笔记
tags:   [linux]
---
You can just 'vi filename' as a normal user. Then in the vim editor, do `:w !sudo tee %` 

参考：[https://bbs.archlinux.org/viewtopic.php?pid=989778#p989778](https://bbs.archlinux.org/viewtopic.php?pid=989778#p989778){:target="_blank"}

说明：

- `:w`是vi的ex模式中的一个命令。
    
    > 把缓冲区写（保存）到文件中但不退出，可以（和应该）在编辑会话期间使用：w来保护编辑操作以避免系统瘫痪或重大的编辑错误

- `tee`：

    > 读取标准输入的数据，并将其内容输出成文件。

- `%`：

    > 在vi中代表当前文件名

所以：`:w !sudo tee %`的意思就是把当前编辑的文件的内容当做标准输入通过sudo tee保存到当前文件中。
