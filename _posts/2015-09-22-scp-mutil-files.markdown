---
layout: post
title:  "scp复制多个文件"
date:   2015-09-22 09:29:35 +0800
categories: blog
tags:   [linux]
---
复制本地多个文件

    $ scp foo.txt bar.txt username@remotehost:/path/directory/

复制远程多个文件

    $ scp username@remotehost:/path/directory/\{foo.txt,bar.txt\} .

参考：[http://www.binarytides.com/linux-scp-command/](http://www.binarytides.com/linux-scp-command/){:target="_blank"}
