---
layout: post
title:  "debain安装和配置snmp代理"
date:   2012-12-07 09:23:36 +0800
categories: blog
tags:   [linux]
---

* 目录
{:toc}


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
    
    
### Red hat/Cent配置

```shell
$cat /etc/snmp/snmpd.conf
com2sec AllUser         default         public
group   AllGroup        v2c             AllUser
view    AllView         included        .1
access  AllGroup        ""      any     noauth  exact   AllView         none    none
```

安全配置(如果需要)

```shell
$cat /etc/sysconfig/snmpd
# snmpd command line options
#OPTIONS="-LS0-6d -Lf /dev/null -p /var/run/snmpd.pid -x 0.0.0.0"
```

配置完成后重启SNMP服务：

`service snmpd restart`

开机启动服务：

`chkconfig snmpd on`

验证SNMP代理是否配置正确：

`snmpwalk -v 2c -c public -O e 127.0.0.1`

从远程验证是否可以访问：

- mib浏览器
- `snmpwalk -v 2c -c public -O e <设备ip>`