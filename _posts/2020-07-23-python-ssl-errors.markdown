---
layout: post
title:  "Mac下python 2.7的2个ssl错误解决"
date:   2020-02-27 15:50:05 +0800
categories: blog
tags:   [mac, python, ssl]
---

## 1. `SSLError: [SSL: TLSV1_ALERT_PROTOCOL_VERSION] tlsv1 alert protocol version (_ssl.c:590)`

环境： macOS 10.15.6; python 2.17.10

这个错误是因为ssl版本不匹配造成的。搜索发现多少在用pip的时候出现这个问题，还有用reqeusts也会遇到这个问题。是在用urllib.urlopen的时候遇到的。响应的解决方法：

1. 用pip的时候出现这个问题：`curl https://bootstrap.pypa.io/get-pip.py | python`
2. 用reqeusts时：通过安装一个包解决 `pip install-U requests [security]`
3. 以上方法不能解决我的urllib.urlopen遇到的问题。有解决方法是升级到python3。可是我的脚步要在2.7环境下运行，最后发现升级到最新版本的python2.7 （2.7.18）可解决我的问题。

macOS升级默认的python很简单，去[官网](https://www.python.org/downloads/)下载最新的2.7安装程序，安装即可。

## 2. `IOError: [Errno socket error] [SSL: CERTIFICATE_VERIFY_FAILED] certificate verify failed (_ssl.c:727)`

在解决了问题1后，使用urllib.urlopen的时候又出现了这个问题。解决方法是在代码中加入：

```python
import ssl
ssl._create_default_https_context = ssl._create_unverified_context
```

另外参考Mac下另外一个python的ssl错误：[sslv3 alert handshake failure](/blog/2015/11/03/python-ssl-error.html)