---
layout: post
title:  "mac中使用java-home来查找安装的jdk，更改JAVA_HOME"
date:   2014-02-12 15:17:06 +0800
categories: 笔记
tags:   [mac]
---
mac中使用java-home来更改JAVA_HOME

    $ /usr/libexec/java_home  -V
    Matching Java Virtual Machines (3):
        1.7.0_09, x86_64: "Java SE 7" /Library/Java/JavaVirtualMachines/jdk1.7.0_09.jdk/Contents/Home
        1.6.0_45-b06-451, x86_64: "Java SE 6" /System/Library/Java/JavaVirtualMachines/1.6.0.jdk/Contents/Home
        1.6.0_45-b06-451, i386: "Java SE 6" /System/Library/Java/JavaVirtualMachines/1.6.0.jdk/Contents/Home
     
    /Library/Java/JavaVirtualMachines/jdk1.7.0_09.jdk/Contents/Home



参考：[http://java.dzone.com/articles/java-findingsetting](http://java.dzone.com/articles/java-findingsetting){:target="_blank"}