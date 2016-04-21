---
layout: post
title:  "perl笔记  大小写转换"
date:   2011-12-16 13:57:40 +0800
categories: blog
tags:   [perl]
---
大小写转换有两种方法，一种用**正则中的转意字符**，另外一种是**字符串函数**

功能  |转意  |函数
---|---|---
全部字母转换成大写   |\U *用\E标记结束   |uc
全部字母转换成小写   |\L *用\E标记结束   |lc
首字母转换成大写    |\u  |ucfirst
首字母转换成小写    |\l  |lcfirst


### 实例：

    #!perl -w
    $string="Hello, World!!!";
    $string=~s/(Hello)/\U$1\E/g;
    println ($string); #HELLO, World!!!
    println ("\l$string"); #hELLO, World!!!
    println (lcfirst $string); #hELLO, World!!!
    println ("\U$string"); #HELLO, WORLD!!!
    println (uc $string); #HELLO, WORLD!!!

    sub println{
    my $m=shift or return;
    print("$m\n");
    }