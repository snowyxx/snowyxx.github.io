---
layout: post
title:  "mac安装PostgreSQL"
date:   2014-02-14 13:36:38 +0800
categories: 笔记
tags:   [mac]
---
1. 简单的方法是使用PostgreSQL.app
2. 如果使用brew安装:          
    在OS X10.9中`brew install postgresql`失败，
    要先`brew update`             
    如果brew update失败，查看`brew doctor`       
    `xcode-select --install`  安装CLT然后再`brew update`                 
    `brew install postgresql`成功
    
        ==> /usr/local/Cellar/postgresql/9.3.2/bin/initdb /usr/local/var/postgres -E utf 
        ==> Summary  /usr/local/Cellar/postgresql/9.3.2: 2924 files, 39M

3. 启动配置
    - 运行`brew info postgres`查看安装信息                
    - To have launchd start postgresql at login: 
        
            ln -sfv /usr/local/opt/postgresql/*.plist ~/Library/LaunchAgents 
    
    - Then to load postgresql now: 
        
            launchctl load ~/Library/LaunchAgents/homebrew.mxcl.postgresql.plist
  
    - 或者使用下面的命令启动和关闭
    
            alias pg.start='pg_ctl -D /usr/local/var/postgres -l /usr/local/var/postgres/server.log start' 
            (postgres -D /usr/local/var/postgres)
            alias pg.stop='pg_ctl -D /usr/local/var/postgres stop -s -m fast'

    - 查看日志或者运行psql -d postgres确定PostgreSQL已经启动

4. 创建用户和数据库

        createuser postgres -P
        createdb amdb -O postgres -E UTF8 -e