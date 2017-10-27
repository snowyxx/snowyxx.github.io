---
layout: post
title:  "perl gt 和 > 的一个区别"
date:   2011-02-11 19:03:08 +0800
categories: blog
tags:   [perl]
---
个人认为：perl中gt应该是比较ascii的大小， “>”是比较数字的    
通过下面的程序验证：

    #/usr/bin/perl
    use strict;

    my @fred = above_average(1..10);
    print "\@fred is @fred\n";
    print "(Should be 6 7 8 9 10)\n";
    sub above_average{
        my @list;
        foreach (@_){
            if ($_ > get_avarage(@_)){
                #print "yan " .$_ . "\n";
                push @list, $_           
            }
        }
        @list;
    }
    sub get_avarage{
        my $avg;
        my $sum;
        my $cun=@_;
        foreach (@_){
            $sum += $_
        } 
        $avg=$sum/$cun
    }

如果在`$_ > get_avarage(@_)`换成`$_ gt get_avarage(@_)`，那么程序认为6不是 “gt” 10，程序的结果就会丢掉10。