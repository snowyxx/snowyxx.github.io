---
layout: post
title:  "mac设置和使用笔记"
date:   2013-07-26 15:05:33 +0800
categories: blog
tags:   [mac]
---
1. 在Finder的标题显示完整路径
    
        defaults write com.apple.finder _FXShowPosixPathInTitle -bool YES

2. 在Finder中显示隐藏文件
   
        defaults write com.apple.Finder AppleShowAllFiles YES 

3. cvs              
   通过安装xcode，然后在xcode的preference>Downloads中安装Command Line Tools，这样cvs客户端就安装上了。在~/.bash_profile（没有创建）中输入
        
        CVSROOT=:pserver:USERNAME:PASSWORD@DOMAIN.COM/CVSROOT_DIRECTORY
        export CVSROOT
   
   保存，然后运行source .bash_profile来立即生效                 
   
    从xcode 5.0.1开始，移除了cvs，通过下面的方法安装cvs命令行客户端
    
        ruby -e "$(curl -fsSL https://raw.github.com/mxcl/homebrew/go)"
        brew tap epichub/homebrew-epicbrews
        brew install cvs

4. svn      
   通过安装xcode，然后在xcode的preference>Downloads中安装Command Line Tools
 
5. 开发模式 developer mode     
    初次运行xcode的时候提示启用开发者模式，当时没有启用，如果之后启用在终端中输入
    
        DevToolsSecurity -enable

6. delete键                      
     
     - 直接按删除光标前的字符。                           
     - fn＋delete  删除光标后的字符。
     - fn＋option＋delete  删除光标后的单词
     - option＋delete  删除光标前的单词
     - command＋delete  删除光标前整行

7. 关闭屏幕，类似Widnows的 win＋L
    
        shift+control+power

8. 使用MAC自带的apache运行一个简单的web服务器              
    创建~/Sites/index.html文件
    
        sudo vim /etc/apache2/users/.conf
    
    加入：
    
         /Sites/">
        Options Indexes MultiViews
        AllowOverride None
        Order allow,deny
        Allow from all
   
        sudo launchctl load -w /System/Library/LaunchDaemons/org.apache.httpd.plist
   
    通过http://localhost  http://localhost/~访问。


9. 通过python来创建简单web服务器        
    到你网站文件夹下运行
    
        python -m SimpleHTTPServer 8000

10. snmp配置
     
    [http://www.paessler.com/knowledgebase/en/topic/41843-how-do-i-activate-snmp-on-mac-os-in-order-to-monitor-it-with-prtg](http://www.paessler.com/knowledgebase/en/topic/41843-how-do-i-activate-snmp-on-mac-os-in-order-to-monitor-it-with-prtg){:target="_blank"}

11. 监视各个进程的网络流量

      - nettop
      - Rubbernet  这个软件不错

12. sublime
     - 添加右键菜单：打开Automator，然后新建一个Service，动作选择为Run Shell Scripts，将动作拖入右边框内后，然后输入源选择为Files or Folders，并作为as arguments，在下方的输入栏中输入

                /Applications/Sublime\ Text\ 2.app/Contents/SharedSupport/bin/subl -n "$@"

        然后保存为Open-in-Sublime,就可以在右键中使用Sublime打开文件或文件夹。

     - 终端启动：$ sudo ln -s "/Applications/Sublime Text 2.app/Contents/SharedSupport/bin/subl" /usr/bin/subl
    
     - 中文显示为乱码     
        使用package control安装ConvertToUTF8和GBK Encoding Support。
        我安装ConvertToUTF8的时候显示下载失败。所以就下载了zip包，然后放到了packages文件下来手动安装。

13. finder中快速浏览文本文件启动选择内容
        
        defaults write com.apple.finder QLEnableTextSelection -bool TRUE;killall Finder

14. 删除打开方式中重复或没用的程序
    
        /System/Library/Frameworks/CoreServices.framework/Versions/A/Frameworks/LaunchServices.framework/Versions/A/Support/lsregister -kill -r -domain local -domain system-domainuser

15. 重启sshd

        sudo launchctl unload  /System/Library/LaunchDaemons/ssh.plist
        sudo launchctl load -w /System/Library/LaunchDaemons/ssh.plist 
        sudo launchctl list
     
     