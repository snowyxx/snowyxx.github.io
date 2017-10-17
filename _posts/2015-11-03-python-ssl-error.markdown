---
layout: post
title:  "abort: error: [SSL: SSLV3_ALERT_HANDSHAKE_FAILURE] sslv3 alert handshake failure "
date:   2015-11-03 12:12:53 +0800
categories: 笔记
tags:   [mac,python]
---

    abort: error: [SSL: SSLV3_ALERT_HANDSHAKE_FAILURE] sslv3 alert handshake failure (_ssl.c:590) 

change the below mentioned value in ssl.py (/usr/lib/python2.7/ssl.py) file. 

    Existing value: 

    _DEFAULT_CIPHERS = ( 
    'ECDH+AESGCM:DH+AESGCM:ECDH+AES256:DH+AES256:ECDH+AES128:DH+AES:ECDH+HIGH:' 
    'DH+HIGH:ECDH+3DES:DH+3DES:RSA+AESGCM:RSA+AES:RSA+HIGH:RSA+3DES:!aNULL:' 
    '!eNULL:!MD5' 
    ) 


New Value: 

    _DEFAULT_CIPHERS = ( 
    'DEFAULT:!aNULL:!eNULL:!LOW:!EXPORT:!SSLv2' 
    ) 


Apple disabled root less privileges for third part apps for which we need to disable System Integrity Check in Recovery Mode.

Shutdown the machine ---> Press & Hold Command + R ---> Choose preferable language ---> Utilities ---> Terminal

Run the command with out Quotes `csrutil disable` so that it will disable System Integrity Check

Refer: [https://www.quora.com/How-do-I-turn-off-the-rootless-in-OS-X-El-Capitan-10-11](https://www.quora.com/How-do-I-turn-off-the-rootless-in-OS-X-El-Capitan-10-11){:target="_blank"}