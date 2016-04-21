---
layout: post
title:  "tomcat 启动ssl（ubuntu）"
date:   2012-03-30 14:35:32 +0800
categories: blog
tags:   [linux]
---

### 生成key 

(1) `keytool -genkey -alias tomcat -keyalg RSA -keypass changeit -storepass changeit -keystore server.keystore -validity 3600`

此时会在%TOMCAT_HOME%/bin下生成server.keystore 文件。

注：参数 -validity 指证书的有效期(天)，缺省有效期很短，只有90天。

(2) `keytool -export -trustcacerts -alias tomcat -file server.cer -keystore server.keystore -storepass changeit`

这一步用于导出证书，此时会在%TOMCAT_HOME%/bin下生成server.cer 文件。

(3)`sudo  keytool -import -trustcacerts -alias tomcat -file server.cer -keystore %JAVA_HOME%/jre/lib/security/cacerts -storepass changeit`

这一步是导入到证书信任库，大家可以观察$JAVA_HOME/jre/lib/security/cacerts 这个文件，执行完此命令后，文件变大。

附：keytool其它命令（列出信任证书库中所有已有证书，删除库中某个证书）：

`keytool -list -v -keystore D:/sdks/jdk1.5.0_11/jre/lib/security/cacerts`

`keytool -delete -trustcacerts -alias tomcat -keystore D:/sdks/jdk1.5.0_11/jre/lib/security/cacerts -storepass changeit`


### 修改%TOMCAT_HOME%\conf\server.xml,找到这段代码：

    maxThreads="150" scheme="https" secure="true"
    clientAuth="false" sslProtocol="TLS" />

这段代码本来是注释掉的，把注释去掉，并且加上两个属性之后，如下：

    maxThreads="150" scheme="https" secure="true"
    clientAuth="false" sslProtocol="TLS"
    keystoreFile="tomcat_home/bin/server.keystore"
    keystorePass="changeit" />

3、启动tomcat,访问 https://localhost:8443/，弹出一个安全警告的页面就OK了。

参考：             
[http://www.oschina.net/question/12_23148  (这个说的简单）](http://www.oschina.net/question/12_23148){:target="_blank"}            
[http://hi.baidu.com/水缘烟/blog/item/236437d58df9921da08bb77e.html](http://hi.baidu.com/水缘烟/blog/item/236437d58df9921da08bb77e.html){:target="_blank"}            
[http://tomcat.apache.org/tomcat-5.5-doc/ssl-howto.html#Configuration](http://tomcat.apache.org/tomcat-5.5-doc/ssl-howto.html#Configuration){:target="_blank"}