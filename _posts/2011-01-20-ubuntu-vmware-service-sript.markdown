---
layout: post
title:  "在ubuntu开机启动vmware到虚拟机"
date:   2011-01-20 10:48:41 +0800
categories: 笔记
tags:   [linux,script,vmware]
---
在ubuntu中安装了vmware用来运行几个虚拟机。但是每次重新启动ubuntu都要手动启动vmware和虚拟机，很不方便。两种方法，一种是直接把命令写到/etc/rc.local中；另外一种是添加一个系统服务。下面记录添加服务的过程。

1. 编写服务用的脚本。放到/etc/init.d/下，chomd为755 脚本内容如下：

        #!/bin/bash
        prog="vmxxxxxxxx"
        progname="vmxxxxxxxx"
        RETVAL=0
        # Edit the following to indicate the 'bin' directory for your installation
        MDIR=/usr/bin

        if [ ! -d "$MDIR" ]
        then
         echo "Invalid directory $MDIR"
            exit 1
        fi

        start()
        {
                #mv -f /var/log/vm.log /var/log/vm1.log
            echo "Starting $progname"
                cd $MDIR
            #su -l ac -c "vmrun -T ws start xxx.vmx nogui >>/var/log/vm.log 2>&1"
            su -l ac -c "vmrun -T ws start yyy.vmx nogui"    
            #echo $? >>/var/log/vm.log    
            su -l ac -c "vmrun -T ws yyy.vmx nogui"
            #echo $? >>/var/log/vm.log
        }

        stop()
        {
            #mv -f /var/log/vm_stop.log /var/log/vm_stop1.log    
            #echo "Stopping $progname"
                cd $MDIR
            vmrun suspend "xxx.vmx" #nogui >/var/log/vm_stop.log
            vmrun suspend "yyy.vmx" #nogui >>/var/log/vm_stop.log
        }
        status()
        {
            cd $MDIR
            vmrun list
        }
        case "$1" in
         start)
               start
                 ;;
         stop)
              stop
                ;;
         status)
              status
                ;;
            *)
             echo "Usage: $prog {start|stop|status}"
             exit 1
             ;;
        esac

        exit $RETVAL

2. 运行sudo update-rc.d scriptname defualts，来添加为服务。这样当启动服务器到时候自动启动两个虚拟机。当服务器关闭到时候，暂停这两个虚拟机。
3. 说明
    - vmrun是vmware到命令行工具。常用到到的参数有：
        - vmrun start xxx.vmx    启动一个虚拟机
        - vmrun suspend xxxvmx    暂停一个虚拟机
        - vmrun list    显示正在运行到虚拟机
        - 直接运行vmrun就可以显示详细帮助。
    - nogui参数表示不显示图像界面
    - vmrun运行必须指定用户，原因是要读取license信息。例如我使用到ac用户安装到vmware，licnese信息保存在ac的环境变量中，所以在我脚本中使用ac运行启动命令。不然启动命令不能执行，提示信息“Error: The operation was canceled”
    - su -/-l/-login [user]，是切换到其他用户。和su不同到是：切换用户后使用新用户到环境变量，而su使用原来用户到环境变量。
    - c参数是用来执行后面的命令（双引号之间）后切换回原用户继续工作。
    - ubuntu中可以时候用sysv-rc-conf来查看，管理服务。
    - echo $?可以显示上面命令执行返回的代码。
    - 如果不使用服务，直接添加启动虚拟机命令到/etc/rc.local的话，要在/etc/init.d/rc.local中添加do_stop部分，来实现系统关闭到时候暂停虚拟机。
