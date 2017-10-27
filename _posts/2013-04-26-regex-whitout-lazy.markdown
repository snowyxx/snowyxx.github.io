---
layout: post
title:  "正则表达式不支持非贪婪模式的时候怎么办？"
date:   2013-04-26 15:02:53 +0800
categories: blog
tags:   [regex]
---
非贪婪模式（lazy或non-greedy）就是最小匹配，但是在一些正则表达式中是不支持的（basic nor extended Posix/GNU），一个小技巧如下：                
我要处理的内容             
`ATTR{address}=="00:0d:48:27:86:9d", ATTR{dev_id}=="0x0" NAME="eth0"`               
命令：`sed  '/eth0/{s/\(ATTR{address}=="\).\+"/\1DDDD"/}'`             
这个结果就是.\+会一直匹配到最后的",一个小技巧就是             
命令：`sed  '/eth0/{s/\(ATTR{address}=="\)[^"]\+"/\1DDDD"/}'`

另外：                         
perl中的非贪婪模式符合是?
有的regex是用{-}


参考：[http://stackoverflow.com/questions/1103149/non-greedy-regex-matching-in-sed](http://stackoverflow.com/questions/1103149/non-greedy-regex-matching-in-sed){:target="blank"}