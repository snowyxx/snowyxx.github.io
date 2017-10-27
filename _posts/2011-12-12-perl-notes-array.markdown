---
layout: post
title:  "perl笔记  array"
date:   2011-12-12 14:32:32 +0800
categories: blog
tags:   [perl]
---

    #!perl -w
    $enter="\n";
    $empty="";
    @b=qw/a b c d e/;
    print "数组\@b的长度是$#b \n";      $#arrayname用来获得数组的最后一个索引值
    @a=1..9;
    #取出数组的最后元素
    $nine=pop(@a);
    $e=pop @b;
    print $nine.$e;
    print "\n";
    #向数组最后添加元素
    push @a,11..15;
    print "@a\n";
    #取出数组的开头元素
    $one=shift @a;
    print $one.$enter;
    #向数组开头添加元素
    unshift @a,$one;
    print "@a";
    print $enter;
    print @a.$empty."-------------\n";
    print scalar @a;
    print "\n";
    print "@b";
    print $enter;
    print ${e}."\n";
    foreach (@b){
    print $_."-";
    }
    print "\n";
    #翻转
    @a= reverse @a;
    print "@a";

    splice拼接数组
    @a = qw(Steve Stu Stan);
    splice @a, 1, 1, 'Stewart', 'Zane';
    # @a = ('Steve', 'Stewart', 'Zane', 'Stan') 
    格式：splice 数组,起始位置,偏移量,数组

    map 实现foreach
    my @array = ( 1, 2, 3, 4, 5 );
    my %hash  = map { $_ => $_ * 9 } @array;
    # %hash   = ( 1 => 9, 2 => 18, 3 => 27, 4 => 36, 5 => 45 )

    my @array       = ( 'ReD', 'bLue', 'GrEEN' );
    my @fixed_array = map(ucfirst, map(lc, @array)); # note the nested 'map' functions
    # @fixed_array  = ( 'Red', 'Blue', 'Green' )


    grep  foreach在数组中查找
    my @array     = ( 0, 1, 2, 3, 4, 5 );
    my @new_array = grep { $_ * 9 } @array;
    # @new_array  = ( 1, 2, 3, 4, 5 );

    改变原数据
    my @array     = ( 0, 1, 2, 3, 4, 5 );
    my @new_array = grep { $_ *= 9 } @array;
    # @array      = ( 0, 9, 18, 27, 36, 45 );
    # @new_array  = ( 9, 18, 27, 36, 45 );

    my @does = grep { /\bDoe$/ } @people;

    #希望god和我以后可以看懂我这是要干啥
    