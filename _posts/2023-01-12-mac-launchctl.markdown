---
layout: post
title:  "使用launchctl在Mac下设置计划任务"
date:   2023-01-12 15:50:05 +0800
categories: blog
tags:   [mac, 计划任务, launchctl]
---

好久没有花1个小时以上来调查一个技术问题了。。。而且2022年一篇博客都没有写。

想在Mac设置个计划任务，定时执行我的一个python脚本。

首先想到的是crontab，但是说已不建议使用，但还是倔强试了试。没有成功。过了几天确实需要这个定时任务，所以改用launchctl，结果我的脚本就是不能执行。

搜索了好久，问题才解决。下面记录下用launchctl做计划任务的过程。

### 1. 准备要定期执行的脚本

```shell
#!/bin/sh
echo `date +"%Y-%m-%d %H:%M:%S"`
set -x
cd /Users/yan/Documents/ZohoRepository
/Library/Frameworks/Python.framework/Versions/2.7/bin/python2.7 mygit.py my mail
```
修改脚本权限： `chmod 777 mygit.sh`

### 2. 准备plist文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
  <key>Label</key>
  <string>com.mygit.plist</string>
  <key>ProgramArguments</key>
  <array>
    <string>/Users/username/Documents/ZohoRepository/mygit.sh</string>
  </array>
  <key>StartCalendarInterval</key>
  <array>
  <dict>
        <key>Minute</key>
        <integer>16</integer>
        <key>Hour</key>
        <integer>9</integer>
        <key>Weekday</key> 
        <integer>1</integer>     
  </dict>
  <dict>
        <key>Minute</key>
        <integer>16</integer>
        <key>Hour</key>
        <integer>9</integer>
        <key>Weekday</key> 
        <integer>2</integer>     
  </dict>
  <dict>
        <key>Minute</key>
        <integer>16</integer>
        <key>Hour</key>
        <integer>9</integer>
        <key>Weekday</key> 
        <integer>3</integer>     
  </dict>
  <dict>
        <key>Minute</key>
        <integer>16</integer>
        <key>Hour</key>
        <integer>9</integer>
        <key>Weekday</key> 
        <integer>4</integer>     
  </dict>
  <dict>
        <key>Minute</key>
        <integer>16</integer>
        <key>Hour</key>
        <integer>9</integer>
        <key>Weekday</key> 
        <integer>5</integer>     
  </dict>
    </array>
<key>StandardOutPath</key>
<string>/Users/username/Documents/ZohoRepository/run.log</string>
<key>StandardErrorPath</key>
<string>/Users/username/Documents/ZohoRepository/run.err</string>
</dict>
</plist>
```
保存为com.mygit.plist文件，复制到/Library/LaunchAgents文件夹

### 3. 添加为服务

转到/Library/LaunchAgents目录，执行命令：

`launchctl load -w com.mygit.plist`

这个时候应该就会执行了（launchctl start）。但是我的脚本出现问题，在run.err日志：

`/bin/sh: /Users/username/Documents/ZohoRepository/mygit.sh: Operation not permitted`

这里花费了好多时间来找解决方法：

*在Mac的设置（13.01 版本） - 隐私与安全性 - 完全磁盘访问权限， 添加/bin下的bash或sh*

### 故障排查工具

plist文件检查：plutil

launchcontrol：launchctl的图形工具 - 可以编辑plist文件、查看错误、查看日志等等


### 参考

下面几个网页比较有参考价值：

- https://zhuanlan.zhihu.com/p/388287366

- https://www.kith.org/jed/2022/02/15/launchctl-scheduling-shell-scripts-on-macos-and-full-disk-access/
