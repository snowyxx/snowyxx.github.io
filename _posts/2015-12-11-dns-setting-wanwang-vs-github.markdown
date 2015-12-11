---
layout: post
title:  "到Github pages域名解析设置"
date:   2015-12-11 11:07:05 +0800
categories: blog
tags:   [other]
---
把博客迁移到Github pages后设置了域名解析后可以访问www.xiaoxuenotes.com了。但是会收到Github的邮件，让添加CNAME。
而且不能用xiaoxuenotes.com访问，有点low。网上大家的方法各异。最后找到最合适我要求的方法。

**之前配置：**           
代码根目录CNAME文件内容：
    
    www.xiaoxuenotes.com
    
万网域名解析设置

![wanwang](/images/wanwang_old.png)


**新的配置：**               
代码根目录CNAME文件内容：
    
    xiaoxuenotes.com
    
万网域名解析设置    

![wanwang](/images/wanwang_new.png)

这样就可以使用xiaoxuenotes.com和www.xiaoxuenotes.com访问了。

---

###DNS参考：
**纪录类型**

- A记录：将域名指向一个IPv4地址（例如：10.10.10.10），需要增加A记录
- CNAME记录：如果将域名指向一个域名，实现与被指向域名相同的访问效果，需要增加CNAME记录
- MX记录：建立电子邮箱服务，将指向邮件服务器地址，需要设置MX记录
- NS记录：域名解析服务器记录，如果要将子域名指定某个域名服务器来解析，需要设置NS记录
- TXT记录： 可任意填写（可为空），通常用做SPF记录（反垃圾邮件）使用
- AAAA记录： 将主机名（或域名）指向一个IPv6地址（例如：ff03:0:0:0:0:0:0:c1），需要添加AAAA记录
- SRV记录： 记录了哪台计算机提供了哪个服务。格式为：服务的名字.协议的类型（例如：\_example-server._tcp）
- 显性URL： 将域名指向一个http（s)协议地址，访问域名时，自动跳转至目标地址（例如：将www.net.cn显性转发到www.hichina.com后，访问www.net.cn时，地址栏显示的地址为：www.hichina.com）。 
- 隐性URL： 与显性URL类似，但隐性转发会隐藏真实的目标地址（例如：将www.net.cn隐性转发到www.hichina.com后，访问www.net.cn时，地址栏显示的地址仍然为：www.net.cn）。

**主机记录**

- www ： 将域名解析为www.example.com，填写www；
- @ ： 将域名解析为example.com（不带www），填写@或者不填写；
- mail ： 将域名解析为mail.example.com，通常用于解析邮箱服务器；
- \* ： 泛解析，所有子域名均被解析到统一地址（除单独设置的子域名解析）；
- 二级域名 ： 如：mail.example.com或abc.example.com，填写mail或abc；
- 手机网站 ： 如：m.example.com，填写m。
