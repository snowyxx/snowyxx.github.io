---
layout: post
title:  "perl笔记  stdin"
date:   2011-12-12 14:35:00 +0800
categories: 笔记
tags:   [perl]
---

    #!perl -w
    #错误的写法 $line=chomp(<STDIN>);
    #正确的写法：
    chomp($line=<STDIN>);
    if(!$line){
    print "you just enter nothing \n";
    }else{
    print "what you entered is：$line \n";
    }
    #defined函数
    $line2=undef;
    if(defined($line2)){
    print "it is $line2";
    }else{
    print "it is undef";
    }
    #windows ctrl +z,  Linux ctrl+d
    chomp(@lines=<STDIN>);
    print "@lines";

    #god, what am i doing?
