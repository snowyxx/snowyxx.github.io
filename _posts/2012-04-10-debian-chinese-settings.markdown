---
layout: post
title:  "Debian中文环境设置"
date:   2012-04-10 14:24:15 +0800
categories: blog
tags:   [linux]
---
## 安装gnome
`apt-get install x-window-system-core gnome-core`

## 配置时区
`dpkg-reconfigure tzdata `
`vim /etc/default/rcS  #change UTC's value`

## 配置locales
`dpkg-reconfigure locales`          
选中：  
       
    en_US.UTF-8
    zh_CN.GB2312
    zh_CN.UTF-8
    zh_CN.GBK
    zh_TW.BIG5
    zh_TW.UTF-8
    
默认（default）：en_US.UTF-8

## 安装Windows字体
`mkdir /usr/share/fonts/windows`            
把Windows的font中的文件复制到这个目录中               
`cd /usr/share/fonts/windows`           
`chmod 644 *`           
`mkfontscale `          
`mkfontdir`         
`fc-cache -fv`          

## 安装其他中文字体
`apt-get install xfonts-intl-chinese ttf-arphic-uming wqy* `

## 安装中文输入法ibus
`aptitude install ibus ibus-pinyin`

## 安装rar
`aptp-get install rar unrar`


参考:         
[http://hi.baidu.com/wangcomeoffit/blog/item/d4ee340abe3779830b7b825e.html](http://hi.baidu.com/wangcomeoffit/blog/item/d4ee340abe3779830b7b825e.html){:target="blank"}