---
layout: post
title:  "使用Mondo rescue备份还原Debian系统(多图)"
date:   2012-04-09 12:59:10 +0800
categories: 笔记
tags:   [linux]
---
之前给的Linux系统还原光盘是用ghost作的，当用debian 5， ext3分区的时候，不能使用ghost8，只能ghost11，这些到都不是问题，就是当还原的新硬盘的时候，第一次启动的时候总是显示GRUB，然后就不能继续启动了。找了好久都没有找到解决方法。回避方法是再用ghost还原一次后就可以启动了。

现在要做新硬件的系统，不想再用ghost了，找其他的方法。。。

从这里看到的方法：[http://www.turbolinux.com.cn/turbo/wiki/doku.php?id=syadmin:system-backup](http://www.turbolinux.com.cn/turbo/wiki/doku.php?id=syadmin:system-backup){:target="blank"} （这个页面打开的时候，Symentac会报毒。。。）选择使用Mondo。。。          

[Mondo Rescue主页](http://www.mondorescue.org/downloads.shtml){:target="blank"}

### 在Debian中安装    
如果用FTP：ftp://ftp.mondorescue.org  user: anonymous  #获取resource list文件（用wget， should be more easy:)                  
`wget ftp://ftp.mondorescue.org/debian/6.0/mondorescue.sources.list`                

    cd **debian/5.0**
    get **mondorescue.sources.list**
    bye
    cat mondorescue.sources.list >> /etc/apt/sources.list
    apt-get update
    apt-get install mondo
    
### 备份
运行mondoarchive，按照向导配置备份参数即可。备份很快，900MB的原系统要几分钟就可以生成iso文件，iso文件大300MB多。

### 还原
把iso刻录到关盘中，然后使用其引导（就是mindi引导程序，单独的Minidi ISO有60MB左右）
引导后直接回车就自动恢复了。可以根据提示选择其他回复方式。

### 截图
备份和还原过程截图。
可以备份到当前系统所在的硬盘
![m1](/images/m1.png)    
       
生成ISO所在的地址          
![m2](/images/m2.png)           

![m3](/images/m3.png)           

![m4](/images/m4.png)           

![m5](/images/m5.png)           

![m6](/images/m6.png)           

![m7](/images/m7.png)           

要备份的NTFS分配。             
![m8](/images/m8.png)           

哪些文件夹不备份。proc和dev（要还原的系统的硬件一般不一样），还有要去除上面设置的iso文件所在的路径              
![m9](/images/m9.png)           

下面的3图不知道神马意思。。。             
![m10](/images/m10.png)           

![m11](/images/m11.png)           

![m12](/images/m12.png)           

![m13](/images/m13.png)           

![m14](/images/m14.png)           

![m15](/images/m15.png)           

![m16](/images/m16.png)           

![m17](/images/m17.png)           

![m18](/images/m18.png)           

还原              
启动后，由生成的ISO引导。在下面的图直接回车，使用自动模式              
![m19](/images/m19.png)           

![m20](/images/m20.png)           

会面的过程是创建分区，格式化，恢复。。。                
![m21](/images/m21.png)           

下面的步骤没必要。。。我认为。。。               
![m22](/images/m22.png)           

下面的图中OK后，执行命令mkinitrd提示命令不存在。。。应该是path问题。直接exit退出。。。                
![m23](/images/m23.png)           

最后完成重新启动系统。                 
![m24](/images/m24.png)  

