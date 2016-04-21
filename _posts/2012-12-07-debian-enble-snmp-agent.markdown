---
layout: post
title:  "debain安装和配置snmp代理"
date:   2012-12-07 09:23:36 +0800
categories: blog
tags:   [linux]
---
### 安装
`apt-get install snmp snmpd`

### 配置
`vim /etc/snmp/snmpd.conf`

远程访问

    #  Listen for connections from the local system only
    #agentAddress  udp:127.0.0.1:161
    #  Listen for connections on all interfaces (both IPv4 *and* IPv6)
    agentAddress udp:161,udp6:[::1]:161
    
访问权限

    view   allview    included   .1.3.6
    rocommunity public  default    -V allview

### 重启服务
`/etc/init.d/snmpd restart`
 
### iptables

    iptables -A INPUT -i eth0 -p udp -s 60.*.*.* –dport 161 -j ACCEPT