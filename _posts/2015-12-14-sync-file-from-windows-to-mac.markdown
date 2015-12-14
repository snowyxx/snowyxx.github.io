---
layout: post
title:  "从Windows同步文件到Mac"
date:   2015-12-14 11:27:05 +0800
categories: blog
tags:   [python,mac]
---
要把Windows上的文件同步到Mac中，而且有些文件夹／文件是不想同步的。

**方法**:共享Windows上的文件夹，在Mac上mount上，然后使用`rsync` 同步到Mac本地。使用python来生成剔除同步的文件列表。

步骤：    

1. [挂载Windows共享文件夹](/blog/2013/11/13/mac-samba.html)。

2. 运行python脚本，同步文件。 ([source code](https://github.com/snowyxx/MyTest/blob/master/syncdoc.py))
