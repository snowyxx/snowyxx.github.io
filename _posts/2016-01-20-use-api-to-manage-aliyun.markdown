---
layout: post
title:  "通过API来管理阿里云"
date:   2016-01-20 14:16:05 +0800
categories: blog
tags:   [python]
---
###引

除了传统的本地物理资源，整个IT架构中的云计算和虚拟化的占比越来越大。云计算主要分为三类：IaaS（Infrastructure-as-a-Service，基础设施即服务）、Paas（PaaS: Platform-as-a-Service，平台即服务）和Saas（Software-as-a-Service，软件即服务）。三者的区别如下图：

![云计算模型](/images/Cloud-Service-Models.png)
（图片来自网络）
###异构管理

在运维的时候，我们总是希望有一个统一的平台来集中管理整个IT基础架构，即时有些设施是在云上的。我们期望的目标：

- 统一管理。在一个统一的控制台中管理网络、服务器、中间件、数据库、虚拟化和云资源。
- 无干扰。就是不用在被管资源上部署代理，通过公开API来进行管理。
- 动作。包括：出现故障后的纠正动作；根据实际情况自动增加或减少资源投入。

###阿里云

阿里云提供了大量的API，运维人员可以通过API把对阿里云的监视和管理集成到目前的运维平台中。

[阿里云API帮助](http://develop.aliyun.com/api){:target="_blank"}   

###示例

使用Python通过API获取所有域名信息。

[DNS API帮助](https://help.aliyun.com/document_detail/dns/api-reference/summary.html){:target="_blank"}

（[source](https://github.com/snowyxx/aliyun-python-demo/blob/master/monitor.py)）
输出：

    <--table Domain details starts-->
    domainName|domainMail|domainRegDate|domainOU|domainExpDate|domainUser
    ********.com|***@126.com|2015-12-09 02:28:24|HICHINA ZHICHENG TECHNOLOGY LTD.|2018-12-09 02:28:24|Lu Xiangchuan
    <--table Domain details ends-->
    <--table Domain records starts-->
    RecordValue|RecordLine|RecordName|RecordTTL|RecordStatus|RecordRR|RecordLocked|RecordType
    ***.github.io|default|********.com|600|ENABLE|www|False|CNAME
    192.30.252.153|default|********.com|600|ENABLE|@|False|A
    192.30.252.154|default|********.com|600|ENABLE|@|False|A
    v=spf1 include:spf.mxhichina.com ~all|default|********.com|600|ENABLE|@|False|TXT
    mxn.mxhichina.com.|default|********.com|600|ENABLE|@|False|MX
    mxw.mxhichina.com.|default|********.com|600|ENABLE|@|False|MX
    pop3.mxhichina.com|default|********.com|600|ENABLE|pop3|False|CNAME
    smtp.mxhichina.com|default|********.com|600|ENABLE|smtp|False|CNAME
    mail.mxhichina.com|default|********.com|600|ENABLE|mail|False|CNAME
    <--table Domain records ends-->

![APM_Table](/images/aliyun_dns.png)
