---
layout: post
title:  "Linux添加服务，让程序开机运行"
date:   2012-09-28 10:07:41 +0800
categories: blog
tags:   [linux]
---
要在Linux添加服务，随系统启动自动运行：

以Redhat 5为例

1.把以下内容添加的一个文件（xxx）中，然后复制到/etc/init.d/目录下

    #!/bin/bash
    #
    # Startup script for the pmagent
    #
    # chkconfig: 345 99 02
    # description: Run the xxx 
    INITLOG_ARGS=""
    prog="xxx"
    progname="xxx"
    RETVAL=0
    # Edit the following to indicate the 'bin' directory for your installation
    MDIR=/opt/xxx
    if [ ! -d "$MDIR" ]
    then
    echo "Invalid directory $MDIR"
    exit 1
    fi
    start()
    {
    mv -f /var/log/xxx.log /var/log/xxx1.log
    echo "Starting $progname"
    cd $MDIR
    nohup sh startxxx.sh >/var/log/xxx.log 2>&1 &
    RETVAL=$?
    echo
    [ $RETVAL = 0 ] && touch /var/lock/Xxx 
    }
    stop()
    {
    echo "Stopping $progname"
    cd $MDIR
    sh shutdownxxx.sh >>/var/log/xxx.log 2>&1
    }
    case "$1" in
    start)
    start
    ;;
    stop)
    stop
    ;;
    *)
    echo "Usage: $prog {start|stop}"
    exit 1
    ;;
    esac
    exit $RETVAL 

2. `# chmod 755 /etc/init.d/xxx`

3. 使用下面的命令添加脚本到启动进程         
`# chkconfig --add xxx`             
(debina命令:`update-rc.d xxx defaults`)