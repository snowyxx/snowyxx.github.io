---
layout: post
title:  "安装shc"
date:   2013-05-03 16:58:29 +0800
categories: blog
tags:   [linux]
---
shc是对shell脚本进行加密的工具，对于那些不想让别人看到内容脚本很有用。

**安装**              

    wget http://www.datsi.fi.upm.es/~frosal/sources/shc-3.8.9.tgz
    tar -zxvf shc-3.8.9.tgz
    cd shc-3.8.9
    make

>会出错：
>make: *** No rule to make target \`shc.c', needed by `shc\'. Stop.

    cp shc-3.8.9.c shc.c
    make
    make install
    cp shc-3.8.9.c shc.c
    sudo chmod +x /bin/shc
    shc

参考：[http://blog.riswan.com/2012/11/how-to-install-shc-on-your-linux-system.html](http://blog.riswan.com/2012/11/how-to-install-shc-on-your-linux-system.html){:target="_blank"}