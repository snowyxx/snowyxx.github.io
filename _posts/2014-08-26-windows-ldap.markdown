---
layout: post
title:  "Windows中部署LDAP"
date:   2014-08-26 13:51:28 +0800
categories: blog
tags:   [windows,ldap]
---
1. 下载安装[Windows版OpenLDAP](http://www.userbooster.de/download/openldap-for-windows.aspx){:target="_blank"}
   
2. 关闭OpenLDAP

3. 修改OpenLDAP\slapd.conf 

        suffix      "dc=maxcrc,dc=com"
        rootdn      "cn=Manager,dc=maxcrc,dc=com"

    修改成：

        suffix      "dc=micmiu,dc=com"
        rootdn      "cn=Manager,dc=micmiu,dc=com"

4. 验证是否可以启动run\run.cmd 或者（slapd.exe -d 1 -f ./slapd.conf）

5. 新建一个文件：OpenLDAP\mydemo.ldif ，内容如下（格式要求严格，不能有多余的空格）：

            dn: dc=micmiu,dc=com
            objectclass: domain
            objectclass: top
            o: Michael Blog
            dc: micmiu

            dn: ou=Developer,dc=micmiu,dc=com
            objectclass: organizationalUnit
            ou: Developer
            description: Container for developer entries

            dn: ou=Tester,dc=micmiu,dc=com
            objectclass: organizationalUnit
            ou: Tester
            description: Container for test entries

            dn: uid=Michael,ou=Developer,dc=micmiu,dc=com
            uid: Michael
            objectClass: inetOrgPerson
            mail: sjsky_007@gmail.com
            userPassword: 111111
            labeledURI: http://www.micmiu.com
            sn: Sun
            cn: Michael Sun

            dn: uid=Miumiu,ou=Tester,dc=micmiu,dc=com
            uid: Miumiu
            objectClass: inetOrgPerson
            userPassword: 111111
            labeledURI: http://www.micmiu.com
            sn: Wu
            cn: Miumiu Wu

6. 运行slapadd -v -l ./mydemo.ldif
（如果不成功可以从ldap中导入该文件）

7. ladp客户端（LdapAdminExe-1.5.2.zip）密码secret
    ![ldap](/images/ldap.png)

8. 连接后，导入mydemo.ldif  tools>import


参考:             
[http://www.micmiu.com/enterprise-app/sso/openldap-windows-config/](http://www.micmiu.com/enterprise-app/sso/openldap-windows-config/){:target="_blank"}