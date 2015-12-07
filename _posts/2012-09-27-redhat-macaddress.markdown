---
layout: post
title:  "redhat 临时更改mac地址"
date:   2012-09-27 16:34:23 +0800
categories: blog
tags:   [linux]
---
重启后mac就会恢复。

    # ifconfig eth0 down
    # ifconfig eth0 hw ether 00:0c:29:6b:08:dd
    # ifconfig eth0 up
