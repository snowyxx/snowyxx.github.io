---
layout: post
title:  "python笔记：开始"
date:   2013-01-25 10:50:53 +0800
categories: blog
tags:   [python]
---
开始学习python。

选择的教程：[A Byte of Python （python简明教程）](http://www.swaroopch.com/notes/python/){:target="_blank"}             
[中文在线阅读](http://www.swaroopch.com/notes/Python_cn-Preface/){:target="_blank"}

### IDLE使用
说明：[http://docs.python.org/2/library/idle.html](http://docs.python.org/2/library/idle.html){:target="_blank"}

**几点操作：**       
更改目录

    >>> from os import chdir 
    >>> chdir("C:/myOtherWork")

当前目录

    >>> from os.path import abspath 
    >>> abspath('.')

添加当前路径到path中

    >>> import sys 
    >>> sys.path.append('.')

上一条/下一跳命令

    Alt-p/Alt-n

在IDLE中运行py文件

    exec(open("MySums.py").read())

交互式帮助

    help(xxx) help('xxx') help()
    
### Linux中用env命令定义python所在位置

    #!/usr/bin/env python

### String

- 单引号和双引号好没有任何区别
- 三引号"""/'''用于多行

### 物理行和逻辑行
- ；用来把物理行分为逻辑行（这个真的没有必要使用）
- / 用来把多行合并为一行

参考：         
[http://stackoverflow.com/questions/6087405/python-changing-idles-path-in-win7](http://stackoverflow.com/questions/6087405/python-changing-idles-path-in-win7){:target="_blank"}