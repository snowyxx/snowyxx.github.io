---
layout: post
title:  "vi在打开只读文件后用sudo改为可写"
date:   2013-02-04 12:21:35 +0800
categories: blog
tags:   [linux]
---
You can just 'vi filename' as a normal user. Then in the vim editor, do `:w !sudo tee %` 

参考：[https://bbs.archlinux.org/viewtopic.php?pid=989778#p989778](https://bbs.archlinux.org/viewtopic.php?pid=989778#p989778){:target="_blank"}
