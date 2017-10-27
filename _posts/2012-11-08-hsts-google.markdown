---
layout: post
title:  "使用HSTS解决Chrome中gooogle搜索页面不能打开的问题"
date:   2012-11-08 09:33:42 +0800
categories: blog
tags:   [other]
---
最近不能使用google搜索。。。把搜索引擎改为https://www.google.hk后，点击搜索结果的链接还是用http，页面打不开，每次手动输入https://太抓狂，下面的方法可以每次都用https。。。
在Chrome浏览器里面打开 ：chrome://net-internals/#hsts ，然后在Add Domains 里面加入www.google.com.hk


参考：                     
[http://linux.cn/thread/9651/1/1/](http://linux.cn/thread/9651/1/1/){:target="_blank"}              
[http://dev.chromium.org/sts](http://dev.chromium.org/sts){:target="_blank"}      