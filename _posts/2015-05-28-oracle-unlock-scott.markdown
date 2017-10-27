---
layout: post
title:  "oracle 12c 解锁scott用户"
date:   2015-05-28 16:09:37 +0800
categories: blog
tags:   [other]
---
安装oracle 12c的时候没有解锁scott用户。安装后解锁步骤：

1. `sqlplus / as sysdba`

        @path\to\RDBMS\ADMIN\scott.sql

2. 修改文件：path\to\NETWORK\ADMIN\tnsnames.ora

        PDBORCL =
          (DESCRIPTION =
            (ADDRESS = (PROTOCOL = TCP)(HOST = localhost)(PORT = 1521))
            (CONNECT_DATA =
              (SERVER = DEDICATED)
              (SERVICE_NAME = pdborcl)
            )
          )

3. 重启

        SHUTDOWN IMMEDIATE;
        STARTUP;

4. `sqlplus / as sysdba `

        CREATE OR REPLACE TRIGGER open_pdbs 
          AFTER STARTUP ON DATABASE 
        BEGIN 
           EXECUTE IMMEDIATE 'ALTER PLUGGABLE DATABASE ALL OPEN'; 
        END open_pdbs;
        /

5. `sqlplus SYS/password@PDBORCL AS SYSDBA`

        SQL> ALTER USER scott ACCOUNT UNLOCK IDENTIFIED BY tiger;

        sqlplus scott/tiger@pdborcl

        SQL> show user;
        USER is "SCOTT"


参考：      
[http://lalitkumarb.com/2014/10/01/mandatory-steps-for-12c-installation/](http://lalitkumarb.com/2014/10/01/mandatory-steps-for-12c-installation/){:target="_blank"}            
[http://skyuck.iteye.com/blog/845225](http://skyuck.iteye.com/blog/845225){:target="_blank"}