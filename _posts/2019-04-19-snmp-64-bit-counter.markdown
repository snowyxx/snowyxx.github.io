---
layout: post
title:  "SNMP 32/64位 Counter类型数据"
date:   2019-04-19 15:50:05 +0800
categories: blog
tags:   [snmp]
---

在通过SNMP监视接口流量的时候，如果流量大于100Mbps，就会出现数据不准确的情况。

原因是，在标准mib中，流入流量（ifInOctets）和流出（ifInOctets）流量的oid数据类型是32位的。

> 使用32位的Counter，例如：10Mbps大小流量，大约57分钟后会重置；100Mbps，可能在5.7分钟后重置； 1Gbps，大约34秒。

所以，当监视的流量很大时候，应该使用64位的Counter。(IF-MIB, ifXTable)


ifHCInOctets (.1.3.6.1.2.1.31.1.1.1.6) | ifHCOutOctets (1.3.6.1.2.1.31.1.1.1.10)
ifHCInUcastPkts (.1.3.6.1.2.1.31.1.1.1.7) | ifHCOutUcastPkts (.1.3.6.1.2.1.31.1.1.1.11)
ifHCInMulticastPkts (.1.3.6.1.2.1.31.1.1.1.8) | ifHCOutMulticastPkts (.1.3.6.1.2.1.31.1.1.1.12)
ifHCInBroadcastPkts (.1.3.6.1.2.1.31.1.1.1.9) | ifHCOutBroadcastPkts (.1.3.6.1.2.1.31.1.1.1.13)

可以通过从设备获取 `ifHCInOctets (.1.3.6.1.2.1.31.1.1.1.6) ` 来验证被管设备是否支持64位计数器。

> 一般的网络管理软件中，都有选项选择是那个32位还是64位Counter。

注意：

-  SNMP v1不支持64位Counter。v2c和v3支持。
-  并不是所有厂商的设备，或固件版本支持64位Counter。
-  SNMP和CLI计数器值不一样。
-  厂商并不一定支持所有ifXTable OID。例如思科有的设备肯能只有ifHCInOctets (.1.3.6.1.2.1.31.1.1.1.6)和ifHCOutOctets (1.3.6.1.2.1.31.1.1.1.10)是64位的。
-  

---

参考：                                   

- <https://www.cisco.com/c/en/us/support/docs/ip/simple-network-management-protocol-snmp/26007-faq-snmpcounter.html>