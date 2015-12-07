---
layout: post
title:  "perl笔记  io"
date:   2011-12-12 15:03:12 +0800
categories: blog
tags:   [perl]
---

    #!perl -w
    use strict;
    print "@ARGV";
    my @items =qw(wilma dino pebbles);
    print "@items\n";
    printf "The items are:\n".("s\n"x@items),@items;
    if(@ARGV<2){
    #die "参数个数小于2"; #显示文件和行号
    #die "参数个数小于22222222\n";  #不显示文件和行号
    }
    #if(!open TESTFILE, ">> test.txt"){ #不能同时对文件进行三种操作“<”、“>”、“>>”
    if(!open TESTFILE, "test.txt"){
    die "打开文件失败:$!";
    }
    while(<TESTFILE>){
    print;
    }
    #print TESTFILE "\ntestline from file hander";
    close TESTFILE;


    if(!open TESTFILE, ">> test.txt"){
    die "打开文件失败:$!";
    }
    select TESTFILE;  #更改默认文件句柄
    $|=1; #不将内容保留在缓冲区，在这里就是立即写入到文件中
    print "春风化雨\n";
    select STDOUT;
    print "化雨春风\n";
    print "----------------";

    if(!open STDOUT, ">> test.txt"){
    die "打开文件失败:$!";
    }
    print "又化雨春风了\n";
    #程序结束之前不能把重定向了的STDOUT改回来。 解决方法，要不使用select，要不放到代码块{}中
    #参考http://www.unix.com/shell-programming-scripting/76046-perl-stdout-not-return-screen.html
    use 5.010;
    say "asdfasfasf";
    #执行系统命令的两种操作
    system('dir');
    my @line=`dir`;
    print "@line";
