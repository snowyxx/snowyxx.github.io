---
layout: post
title:  "Linux 使用串口短信调制解调器（短信猫）"
date:   2011-03-09 11:13:18 +0800
categories: blog
tags:   [linux]
---
# 连接
1. 如果连接的为串口，直接连接。确定使用的串口号，可能是tty[1-n]
2. 如果使用USB转串口的，确定使用的串口号。可能是**ttyUSB[1-n]**。查看/dev目录下是否有ttyUSB0，如果没有使用mknod /dev/ttyUSB[1-n] c 188 [1-n]创建。（在Linux中比Windows方便，不用另外安装usb转串口的驱动。）

# 测试串口
使用minicom，类似Windows中的超级终端。

# minicom安装：
1. Redhat，在安装盘中可以找到对应的包，然后`rpm -ihv`安装
2. Debian (ubuntu): `sudo apt-get install minicom`

# minicom使用：
初次运行`minicom -s,`设置端口和波特率等，保存为默认配置。退出minicom，按**Ctrl+a**，然后按**q**。      
对短信常用的at命令：     

    AT
    AT+CGSN
    AT+CSQ
    AT+CMGF=1
    AT+CMGS=手机号之后回车输入内容后按CTRL+Z
    ATD+手机号+分号是打电话，ATH是挂机