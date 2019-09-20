---
layout: post
title:  "Linux syslog简单测试"
date:   2015-05-07 15:04:29 +0800
categories: blog
tags:   [linux]
---
1. 配置rsyslog输出syslog到一个接收日志的“syslog server”         
  （检查是否安装rsyslog的方法：`rsyslogd -v`）
   编辑/etc/rsyslog.conf              
   添加`*.*  @192.168.0.97:514`  是把syslog发送到192.168.0.97的514端口。是UDP的。             
   如果要使用TCP，就写`*.*  @@192.168.0.97:514`                   

2. 重启rsyslog服务
   
        service rsyslog restart 

3. 转发非syslog        
    例如把一个程序的日志通过syslog发出去       
    在rsyslog配置文件中加载imfile模块     
         `module(load="imfile" PollingInterval="5") `           
    然后配置syslog属性  
      
                input(type="imfile"
                File="/var/log/foobar.log"
                Tag="foobar"
                Severity="error"
                Facility="local7")
                
    [zhSyslogGenerator.py](https://github.com/snowyxx/MyTest/blob/master/zhSyslogGenerator.py)是一个用来生成中文syslog日志的脚本。
    对应的`/etc/rsyslog.conf`添加类似于以下的配置：
    ```shell
    module(load="imfile" PollingInterval="5")
    input(type="imfile"
    File="/home/yan/yantestlog.txt"
    Tag = "yantest"
    Severity="error"
    Facility="local7")
    ```

4. 接收syslog
    接收syslog的叫syslog server。可以使用一些现成的工具或日志管理分析软件                
    对于java来说一个简单的测试方法，使用现成的API：[syslog4j](https://github.com/syslog4j/syslog4j){:target="_blank"}     
    使用方法：
               
        java -cp syslog4j-0.9.46.jar org.productivity.java.syslog4j.server.SyslogServerMain -p 514 udp
     
     将在标准输出中显示syslog信息。         
     参数说明：      
            SyslogServer [-h] [-p ] [-o ] [-a] [-q]         
            -h    host or IP to bind        
            -p    port to bind          
            -t socket timeout (in milliseconds)             
            -o    file to write entries(overwrites by default)      
            -a           append to file (instead ofoverwrite)   
            -q           do not write anything to standardout   
            protocol     Syslog4j protocol implementation (tcp,udp, ...)            

参考：         
[https://linux.cn/article-4835-weixin.html](https://linux.cn/article-4835-weixin.html){:target="_blank"}            
[http://blog.csdn.net/peterwanghao/article/details/6958580](http://blog.csdn.net/peterwanghao/article/details/6958580){:target="_blank"}        
[http://blog.csdn.net/origin100/article/details/5176033](http://blog.csdn.net/origin100/article/details/5176033){:target="_blank"}