---
layout: post
title:  "使用notepad++批量修改文件的编码"
date:   2015-08-05 11:18:41 +0800
categories: blog
tags:   [other]
---
有一批文件要把文件格式从ANSI修改为UTF-8 

npp只能一个一个文件地修改。实现批量修改的方法：

1. Plugins 安装Python Script
2. Plugins - Python Script - New Script

{% highlight python %}
import os
import sys
from Npp import notepad

filePathSrc="文件夹路径"
for dir,dirs,files in os.walk(filePathSrc):
for fn in files:
if fn.endswith(".html"):
notepad.open(os.path.join(dir,fn))
notepad.runMenuCommand("Encoding","Convert to UTF-8 without BOM") #npp的语言要设置为英文
{% endhighlight %}

Plugins - Python Script -Scripts - 你的脚本
