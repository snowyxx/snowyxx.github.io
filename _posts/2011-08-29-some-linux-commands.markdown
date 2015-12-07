---
layout: post
title:  "牛x Linux命令"
date:   2011-08-29 10:52:39 +0800
categories: blog
tags:   [linux]
---
###startup a simple web server which default port is 8000
`python -m SimpleHTTPServer 8000`

###startup a SMTP server by python
`python -m smtpd -n -c DebuggingServer localhost:1025`

    -n 参数让 Python 不要进行 setuid ( 改变用户）为 "nobody" ，也就是说直接用你的帐号来运行
    -c DebuggingServer 参数是让 Python 运行时在屏幕上输出调试及运行信息
    localhost:1025 参数则是让 Python 在本地的 1025 端口上开启 SMTP 服务


###save in vim without write permission
`:w !sudo tee %`

###replace some characters in previous command ^old^new^
`!!:s/old/new`

###quickly backup or copy a file
`cp filename{,bak}`

###traceroute and ping 
`mtr www.google.hk`

###find in command history but run it  
`!keyword:p`

###better view of monut list   
`mount | column -t`

###mount remote folder by ssh  
`sshfs name@server:/path/to/folder /path/to/mount/point`
`fusermount -u /path/to/mount/point`

###get keywork from wikipedia dns 
`dig +short txt .wp.dg.cx`

> 这也许是最有趣的一条技巧了，David Leadbeater 创建了一个 DNS 服务器，通过它当你查询一个 TXT 记录类型时，会返回一条来自于 Wikipedia 的简短的词条文字，这是他的介绍。

###download a website by wget
`wget --random-wait -r -p -e robots=off -U Mozilla www.example.com`

    -random-wait 等待 0.5 到 1.5 秒的时间来进行下一次请求
    -r 开启递归检索
    -e robots=off 忽略 robots.txt
    -U Mozilla 设置 User-Agent 头为 Mozilla
    -limit-rate=20K 限制下载速度为 20K
    -o logfile.txt 记录下载日志
    -l 0 删除深度（默认为5）
    --wait=1h 每下载一个文件后等待1小时
    
###get paramater of last command   
`ALT + . (or ESC + .)`
`echo <Press ALT + 2> <Press ALT + .>`

###command histroy 

* .bashrc HISTCONTROL=
* .bash_history
* do not save the command to history if it start with space

###get sub folder size 
`du -h --max-depth=1|sort -rn`

###mount memory to a folder
`mount -t tmpfs -o size=1024m tmpfs /mnt/ram`
>/dev/shm is it

###show runing port and process 
`sudo netstat -tulnp`

    -t: 显示TCP链接信息
    -u: 显示UDP链接信息
    -l: 显示监听状态的端口
    -n: 直接显示ip，不做名称转换
    -p: 显示相应的进程PID以及名称（要root权限）
    如果要查看关于sockets更详细占用信息等，可以使用lsof工具。
    
###show ascii table    
`man 7 ascii`

###shutdown a windows server by remote 
`net rpc shutdown -I IP_ADDRESS -U username%password`

###top 10 commands
`history | awk '{a[$2]++}END{for(i in a){print a[i] " " i}}' | sort -rn | head`

###cool man    
* manpages里面还有一些有趣而且实用的资料，可能鲜为人知：
* man 1 intro - 一篇对从未接触过Linux的用户的简明教程。
* man 2 syscalls - 内核系统请求的列表，按内核版本注释分类，系统编程必备。
* man 2 select_tut - 关于select()系统请求的教程。
* man 3 string - 在头文件内的所有函数。
* man 3 stdio - 关于头文件的使用，标准输入/输出库的说明。
* man 3 errno - 所有errorno的取值及说明。（C语言内类似其他语言的异常告知机制）
* man 4 console_codes - Linux的终端控制码及其使用解释。
* man 4 full - 介绍/dev/full这个总是处于"满"状态的磁盘。（对应/dev/null这个总是空的设备）
* man 5 proc - 介绍/proc下的文件系统。
* man 5 filesystems - 各种Linux文件系统。

第7区里面的资料通常最酷：

* man 7 bootparam - 详细解释内核启动参数。
* man 7 charsets - 解释各种语言的编码集。（gbk，gb2312等）
* man 7 glob - 解释glob文件名管理机制的工作过程。
* man 7 hier - 解释Linux文件系统结构各个部分的作用。
* man 7 operator - C语言的运算符的列表。
* man 7 regex - 介绍正则表达式。
* man 7 suffixes - 常见文件后缀名的列表跟解释。
* man 7 time - Linux的时钟机制解释。
* man 7 units - 数值单位及其数值的解释。
* man 7 utf8 - 描述UTF-8编码。
* man 7 url - 解释URL、URI、URN等的标准。

###view [start wars]by telenet 
`telnet towel.blinkenlights.nl`

###录制Linux屏幕   
`$ ffmpeg -f x11grab -s wxga -r 25 -i :0.0 -sameq /tmp/out.mpg`

###用ssh远程打开程序的图形界面

    ssh -X 用户名@服务器IP "程序名" ## 直接运行一个程序
    ssh -X 用户名@服务器IP # 或 先登陆再运行程序

###免密码SSH登录主机
`$ ssh-copy-id remote-machine`

这个命令把当前用户的公钥串写入到远程主机的~/.ssh/authorized_keys内，这样下次使用ssh登录的时候，远程主机就直接根据这串密钥完成身份校验，不再询问密码了。前提是你当前用户有生成了公钥，默认是没有的，先执行ssh-keygen试试吧！
这个命令如果用手工完成，是这样的：

    your-machine$ scp ~/.ssh/identity.pub remote-machine:
    your-machine$ ssh remote-machine
    remote-machine$ cat identity.pub >> ~/.ssh/authorized_keys
    
参考： [http://www.catonmat.net/blog/top-ten-one-liners-from-commandlinefu-explained/](http://www.catonmat.net/blog/top-ten-one-liners-from-commandlinefu-explained/){:target="blank"}