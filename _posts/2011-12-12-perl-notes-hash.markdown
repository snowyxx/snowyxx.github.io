---
layout: post
title:  "perl笔记  hash"
date:   2011-12-12 15:10:35 +0800
categories: 笔记
tags:   [perl]
---

    #!perl -w
    use strict;
    my %hash_test_weekday=("Mon","星期一","Wed","星期二");
    print $hash_test_weekday{"Mon"},"\n";
    $hash_test_weekday{"Sun"}="星期日";
    &println(%hash_test_weekday);
    print %hash_test_weekday,"\n";
    print "%hash_test_weekday\n";
    #哈希翻转。（赋值的时候先转成列表，所以翻转后原来哈系的值和键翻转了
    my %hash_test_weekday_r=reverse %hash_test_weekday;
    print %hash_test_weekday_r,"\n";
    #使用胖箭头为哈希赋值 ######在后面添加一个额外的逗号是个好习惯
    %hash_test_weekday=("Sat"=>"星期六"=>    # "=>"胖箭头就等于逗号。但是为了引人胖箭头就是为了容易阅读
    Tus=>"星期几",);     #胖箭头的左侧可以使用裸词。裸词：字母数字下划线的序列，可用加减号开头，不能用数字开头
    print %hash_test_weekday,"\n"; 
    #keys和values函数
    &println("--------keys和values函数------------");
    my @keys_weekday=keys %hash_test_weekday;
    my @values_weekday=values %hash_test_weekday;
    print "@keys_weekday","\n";
    print "@values_weekday","\n";

    #each函数
    &println("--------each函数------------");
    while(my ($key,$value)=each %hash_test_weekday){
    print "$key=>$value\n"
    }

    #exists函数
    &println("--------exists函数------------");
    if(exists $hash_test_weekday{"Sat"}){
    print "there's a key named Sat, its value is $hash_test_weekday{Sat}\n";
    }
    #delete函数
    &println("--------delete函数------------");
    delete $hash_test_weekday{Sat};
    print %hash_test_weekday,"\n";

    &list_hash_entry(\%hash_test_weekday);  #"\"必须加
    &list_env_items;
    sub println{
    print "@_"."\n"
    }
    sub list_hash_entry{#接收哈希参数
    my($hash)=@_;
    foreach my $key (sort keys %$hash){
    if($key){print "$key","--------","$hash->{$key}\n";}
    }
    }
    sub list_env_items{
    my $longest=0;
    foreach (keys %ENV){
    my $key_length=length($_);
    $longest=$key_length if $key_length>$longest;
    }
    foreach (sort keys %ENV){
    printf "%-${longest}s = %s \n",$_,$ENV{$_};
    }
    }
