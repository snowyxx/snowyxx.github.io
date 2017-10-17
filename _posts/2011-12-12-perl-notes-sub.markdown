---
layout: post
title:  "perl笔记  sub"
date:   2011-12-12 14:40:31 +0800
categories: 笔记
tags:   [perl]
---

    #!perl -w
    use strict;

    &println( &return_print_test);
    #print &return_print_error;
    print '--------&println( &max(4,7,9,3))--------'."\n";
    &println( &max(4,7,9,3));
    my $test=1; #使用了use strict后，必须用my声明变量
    &println($test);
    &println("----sub count---------");
    &count;
    &count;
    sub println{
    print "@_"."\n";
    }
    sub return_print_test{ #print的返回值是1
    print "something here \n";
    }
    sub return_print_error{ #出错的子程序没有返回值
    3/0;
    }
    sub count{#使用state声明持久私有变量
    use 5.010;
    state $n=0;
    $n+=1;
    &println($n);
    }
    sub max{#获得最大值
    my $max_so_far= shift @_;
    foreach (@_){
    if ($_>$max_so_far){
    $max_so_far=$_;
    }
    }
    $max_so_far;
    }
    #load, take me away plz.

