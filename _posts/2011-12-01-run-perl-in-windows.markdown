---
layout: post
title:  "在Windows中直接运行perl程序【cat示例】"
date:   2011-12-01 17:38:35 +0800
categories: 笔记
tags:   [windows,perl]
---
1. 安装activeperl
2. cat.pl     
    `while (<>) { print; }`
3. 关联pl文件的默认运行程序为perl.exe
4. 添加.pl到pathext环境变量中
5. 这样就可以执行运行: cat test.txt来显示test.txt文件内容了。

### 遇到的问题：
运行perl cat.pl test.txt可以，但是运行cat test.txt的时候命令行没有反应             
**解决：**
注册表：`HKEY_CLASSES_ROOT\Applications\perl.exe\shell\open\command`            
默认项修改为：`"D:\Perl\bin\perl.exe" "%1" %*`

>不知道为什么在Windows 7 中出现这样的问题。即使运行ftype perl命令返回Perl="D:\Perl\bin\perl.exe" "%1" %*，但是在注册表中却是："D:\Perl\bin\perl.exe" "%1"


### 其他命令：
1. 另外一种直接运行的方法就是把pl文件转成bat文件。 使用activeperl的**pl2bat**
2. 
    `D:\workspace\perltest>assoc .pl`     
    `.pl=Perl`

3. `D:\workspace\perltest>ftype perl`     
    `perl="D:\Perl\bin\perl.exe" "%1" %*`

参考：[http://stackoverflow.com/questions/1695188/how-do-i-make-perl-scripts-recognize-parameters-in-the-win32-cmd-console](http://stackoverflow.com/questions/1695188/how-do-i-make-perl-scripts-recognize-parameters-in-the-win32-cmd-console){:target="blank"}

### 这个grep.pl 虽然没有参数，但是可以使用正则表达式

    #!perl -w
    $pattern=shift @ARGV;
    while(<>){
        if(m"$pattern"){print;}
    }
