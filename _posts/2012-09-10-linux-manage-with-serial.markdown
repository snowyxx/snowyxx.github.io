---
layout: post
title:  "使用串口管理Debian(linux)"
date:   2012-09-10 15:44:38 +0800
categories: 笔记
tags:   [linux]
---
## 配置Grub，将输出重定向到串口        
### 对于**Grub1**,直接修改**menu.list**               

1. 把Grub输出重定向到串口
 
    编辑grub的配置文件/boot/grub/menu.lst, 添加如下行:

        serial --unit=0 --speed=9600 --word=8 --parity=no --stop=1
        terminal --timeout=10 serial console

    说明：grub引导过程中, 会将输出同时发送到终端屏幕和串口. grub引导过程中将在终端和连接到串口的超级终端上提示Press any key to continue, 每秒钟提示一次, 共10次, 可修改menu.lst文件terminal行中的--timeout=10改变提示次数, 在这一段时间内, 可以在终端的键盘, 或者连接到串口的超级终端中按任意键进入grub选择菜单.         
    如果10秒内没有在终端和连接串口的超级终端上按任意键, 则grub的选择菜单将出现在连接串口的超级终端上,       
    如果希望默认情况下, grub选择菜单出现在终端上, 则可修改menu.lst将serial console修改为console serial.          

2. 将kernel输出信息输出到串口

    修改kernel行, 在该行后增加增加
    `console=ttyS0,9600n8 console=tty0`         
    则kernel会将输出信息同时输出到串口和终端
     
    修改后的示例：

        serial --unit=0 --speed=9600 --word=8 --parity=no --stop=1
        terminal --timeout=5 serial console
        title        Debian 5
        root     (hd0,0)
        kernel       /boot/vmlinuz-2.6.26-1-686 root=/dev/sda1 ro console=ttyS0,9600n8 console=tty0
        initrd       /boot/initrd.img-2.6.26-1-686
        title        Debian 5(single-user mode)
        root     (hd0,0)
        kernel       /boot/vmlinuz-2.6.26-1-686 root=/dev/sda1 ro single
        initrd       /boot/initrd.img-2.6.26-1-686

    说明：在上例中, 服务启动的信息会显示在终端上(tty0), 如果进入单用户模式, 也只会在终端(tty0)上提示输入root密码, 如果需要将服务启动的信息也输出到串口上, 可修改两个console参数的顺序, 既修改为
    `console=tty0 console=ttyS0,9600n8`

### 对于Grub2

配置文件改为**grub.cfg**。不建议直接修改/boot/grub/grub.cfg文件，通过修改**/etc/default/grub**

文件：

    GRUB_DEFAULT=0
    GRUB_TIMEOUT=5
    GRUB_DISTRIBUTOR=`lsb_release -i -s 2> /dev/null || echo Debian`
    GRUB_CMDLINE_LINUX_DEFAULT="console=tty0 console=ttyS0,9600n8"
    
    # Uncomment to disable graphical terminal (grub-pc only)
    GRUB_TERMINAL=serial
    GRUB_SERIAL_COMMAND="serial --speed=9600 --unit=0 --word=8 --parity=no --stop=1"

然后运行命令:`update-grub`

## 允许从串口登陆
 
修改/etc/inittab文件, 增加如下内容            
`T0:23:respawn:/sbin/getty -L ttyS0 9600 vt100`         
要确保/etc/securett文件中有ttyS，这样的目的是允许root在ttyS0上登陆
则会启动完成后会允许从串口登陆到linux.

最后就可以从Windows系统的超级终端，或者使用Linux中的mincom中使用了。

参考：             
[http://wiki.debian.org/Grub#Configuring_grub_v2](http://wiki.debian.org/Grub#Configuring_grub_v2){:target="blank"}                 
[http://blog.csdn.net/defeattroy/article/details/5257323](http://blog.csdn.net/defeattroy/article/details/5257323){:target="blank"}                 
[http://linux.xvx.cz/2009/08/debian-with-grub2-and-serial-connection/](http://linux.xvx.cz/2009/08/debian-with-grub2-and-serial-connection/)