---
layout: post
title:  "redhat 启用snmpv3 "
date:   2012-03-28 10:14:07 +0800
categories: 笔记
tags:   [linux]
---

1. install **snmp snmpd**
2. install **net-snmp-utils net-snmp-devel**
3. `service snmpd stop`
4. `net-snmp-config --create-snmpv3-user -ro -a PASSWORD -X AES -A SHA USERNAME`            
This creates an SNMPv3 user called USERNAME that has read only access (-ro) authenticated (-a) access with a PASSWORD using AES for encryption (-X) and SHA for your hashes (-A).
Edit _/etc/snmp/snmpd.conf_ and change **rouser USERNAME** to **rouser USERNAME priv**, this forces encryption for all traffic.
 
5. `sudo service snmpd start`
6. test         
`snmpwalk -v 3 -l authPriv -a sha -A PASSWORD -x AES -X PASSWORD -u USERNAME localhost`

### 如果没有安装net-snmp，手动配置：
`sudo service snmpd stop`

Edit **/var/net-snmp/snmpd.conf** and add the following line to the bottom of the file:

`createUser USERNAME SHA "PASSWORD" AES`

When snmpd is started again the password will be hashed and added to the file, the original un-hashed password will be removed. This line specifies the use of SHA for the hash and AES for the encryption.
Edit /etc/snmp/snmpd.conf and add the following line to the bottom, or in a place you deem appropriate:

`rouser USERNAME priv`

This line will add a read only user that can only be accessed via AuthPriv, so with session integrity and encryption enabled.
Start snmpd:

`sudo service snmpd start`

Test:

`snmpwalk -v 3 -l authPriv -a sha -A PASSWORD -x AES -X PASSWORD -u USERNAME localhost`
 
 
参考：[https://stomp.colorado.edu/blog/blog/2010/07/09/on-configuring-snmpv3-in-net-snmp/](https://stomp.colorado.edu/blog/blog/2010/07/09/on-configuring-snmpv3-in-net-snmp/){:target="_blank"}