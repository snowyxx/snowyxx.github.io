---
layout: post
title:  "忘记Linux（Redhat）密码？"
date:   2012-09-07 10:44:29 +0800
categories: 笔记
tags:   [linux]
---
修改Redhat的root密码，可以通过单用户模式修改。

方法是启动的时候在开始菜单选择kernel那行，如果没有这行就按**tab键**

按**e**进入编辑，在这行最后添加**single**。然后保存启动（按b）

启动后使用passwd修改后重启。