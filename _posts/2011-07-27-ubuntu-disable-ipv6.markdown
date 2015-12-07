---
layout: post
title:  "ubuntu禁用ipv6"
date:   2011-07-27 16:06:40 +0800
categories: blog
tags:   [linux]
---
1. `gedit /etc/default/grub`
2. 
> GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"             
变为            
> GRUB_CMDLINE_LINUX_DEFAULT="ipv6.disable=1 quiet splash"

3. `sudo update-grub`

###检查：
`ip a | grep inet6`
 
参考：[http://wiki.ubuntu.org.cn/如何禁用IPv6](http://wiki.ubuntu.org.cn/如何禁用IPv6){:target="blank"}