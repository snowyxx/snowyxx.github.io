---
layout: post
title:  "Firefox禁用自动前后缀"
date:   2011-04-27 17:21:33 +0800
categories: blog
tags:   [firefox]
---
Firefox会把不能打开的页面自动添加上www. 和 .com的前后缀。
在地址栏中输入`about:config`
 
然后过滤找到**browser.fixup.alternate**，对出现的三项进行修改。