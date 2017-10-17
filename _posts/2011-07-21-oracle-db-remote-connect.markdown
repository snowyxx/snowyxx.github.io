---
layout: post
title:  "oracle远程sysdba连接"
date:   2011-07-21 11:32:38 +0800
categories: 笔记
tags:   [database,oralce]
---
**network\admin\sqlnet.ora**配置

    REMOTE_LOGIN_PASSWORDFILE和SQLNET.AUTHENTICATION_SERVICES
    REMOTE_LOGIN_PASSWORDFILE参数值为exclusive时，远程能作为sysdba身份登录
    SQLNET.AUTHENTICATION_SERVICES=none是oracle认证

参考：
[http://wenku.baidu.com/view/0b845c51ad02de80d4d840ca.html](http://wenku.baidu.com/view/0b845c51ad02de80d4d840ca.html){:target="blank"}
[http://yangzhongfei.blog.163.com/blog/static/4610987520098693213374/](http://yangzhongfei.blog.163.com/blog/static/4610987520098693213374/){:target="blank"}