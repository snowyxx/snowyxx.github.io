---
layout: post
title:  "debian的sources.list更新"
date:   2012-04-09 11:29:46 +0800
categories: blog
tags:   [linux]
---
安装Debain 6的时候没有设置网络，所以sources.list没有内容。使用下面的方法：

###第一种：使用[http://debgen.simplylinux.ch/index.php](http://debgen.simplylinux.ch/index.php){:target="blank"} 选择版本和地区生成
163镜像 http://mirrors.163.com/.help/debian.html

###第二种：使用apt-spy

步骤： 

1. echo "deb http://http.us.debian.org/debian/ stable main" >> /etc/apt/sources.list
2. apt-get update
3. apt-get install apt-spy
4. apt-spy update #更新这个文件http://http.us.debian.org/debian/README.mirrors.txt
5. apt-spy -d unstable -a asia -t 5

说明：

    * -d distribution 选择 debian 的发行版本，例如 stable 。除升级外，必须使用此参数。
    * -a area 选择进行测试的区域，例如 Aisa 。
    * -c config 指定配置文件。
    * -e number 指定测试的服务器数量。
    * -f file 测试时抓取制定的文件（文件相对 debian base 的路径）。
    * -i file 指定输入的文件，配合 -w 参数使用。
    * -m mirror-list 指定要升级的镜像列表文件。
    * -o output-file 重定向输出到指定文件。
    * -p proxy 指定使用的代理服务器，需要遵从 [server]:[port] 的格式。
    * -s country_list 指定要进行测试的国家。不能和 -a 参数同时使用。
    * -t time 指定进行测试时每台服务器的超时时间，只能是正整数。
    * -u update-URL 从指定的 URL 获得镜像列表更新。
    * -w file 输出 -i 参数提供的服务器列表中速度最快的前几台镜像服务器（缺省是前 5 台）。
    * -n number 制定 -w 参数输出的服务器数量。
    * -v 输出版本信息。
    * -h 输出当前帮助信息
    * -update 升级镜像站点列表。


参考：         
[http://comdeng.com/blog/article/10642.html](http://comdeng.com/blog/article/10642.html){:target="blank"}               
[http://www.linuxdiyf.com/bbs/thread-52437-1-1.html](http://www.linuxdiyf.com/bbs/thread-52437-1-1.html){:target="blank"}               
相关文章：        
[Using Old Debian Versions In Your sources.list](http://www.howtoforge.com/using-old-debian-versions-in-your-sources.list){:target="blank"}