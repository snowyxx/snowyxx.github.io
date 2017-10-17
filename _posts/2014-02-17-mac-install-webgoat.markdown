---
layout: post
title:  "mac安装WebGoat"
date:   2014-02-17 16:44:37 +0800
categories: 笔记
tags:   [mac]
---
1. 准备jre
2. 下载Tomcat (7）的tar.gz或者zip，解压缩到工作目录。
    运行`bin\startup.sh`来启动tomcat确保可以正常启动
3. 下载Webgoat war包 。复制war文件到tomcat的webapps目录，并重命名为**WebGoat.war**
4. 配置tomcat的用户
    修改tomcat/conf/tomcat-users.xml文件：
    {% highlight html %}
    <role rolename="webgoat_basic"/>
    <role rolename="webgoat_admin"/>
    <role rolename="webgoat_user"/>
    <role rolename="tomcat"/>
    <role rolename="manager-gui"/>
    <user username="tomcat" password="tomcat" roles="manager-gui"/>
    <user password="webgoat" roles="webgoat_admin" username="webgoat"/>
    <user password="basic" roles="webgoat_user,webgoat_basic" username="basic"/>
    <user password="tomcat" roles="tomcat" username="tomcat"/>
    <user password="guest" roles="webgoat_user" username="guest"/>
    {% endhighlight %}
            
最后，启动tomcat，访问http://localhost:8080/WebGoat/attack，用户名密码都是guest