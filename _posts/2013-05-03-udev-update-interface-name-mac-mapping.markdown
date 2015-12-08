---
layout: post
title:  "使用udev规则文件更改多个网卡的名称"
date:   2013-05-03 11:09:20 +0800
categories: blog
tags:   [linux]
---
Linux使用udev来管理硬件，`/lib/udev/write_net_rules`程序根据`persistent-net-generator.rules`中定义的规则来生成`/etc/udev/rules.d/70-persistent-net.rules`文件，网卡的名称就是基于此文件命名。

**persistent-net-generator.rules**文件中定义了网卡命名排序规则，默认基于MAC等，但是在我的系统Debain 6中，并不随人愿，udev支持邮件列表说在新版本的Linux中运行并不好。所以只有通过修改**/etc/udev/rules.d/70-persistent-net.rules**文件来重命名网卡名称。

规则文件中可以根据网卡的许多属性来定义，查看网卡属性的方法有

1. `ethtool eth?`  或`ethtool -i eth?`
2. `udevadm info -a -p /sys/class/net/eth?`
3. udev测试（不用重启看效果） `udevadm test eth?`

另外还有一个工具ifrename可以通过定义自己的配置文件/etc/iftab来设置网卡名称。

用来修改debain 8个网卡名称的脚本（根据mac地址顺序命名）

    #!/bin/bash
    ruleFile="/etc/udev/rules.d/70-persistent-net.rules"
    for ifcfgpath in `ls /sys/class/net |grep eth` ; do
    dev=`echo ${ifcfgpath}`
    ethtype=`ethtool ${dev} | grep "FIBRE" |wc -l`
    if [ $ethtype -lt 1 ]; then
    TmacStr="${TmacStr} "`cat /sys/class/net/${dev}/address`
    else
    FmacStr="${FmacStr} "`cat /sys/class/net/${dev}/address`
    fi
    done

    TmacArr=($TmacStr)
    for((i=0;i<${#TmacArr[@]};i++))
    do
            n=i
            min=${TmacArr[$i]}
            for((j=i+1;j<${#TmacArr[@]};j++))
            do
                    if [[ "${TmacArr[$j]}" < "$min" ]]
                    then
                            n=$j
                            min=${TmacArr[$j]}
                    fi
            done
            t=${TmacArr[$n]}
            TmacArr[$n]=${TmacArr[$i]}
            TmacArr[$i]=$t
    done
    for((i=0;i<${#TmacArr[@]};i++))
    do
    sed -i ''/eth${i}/{s@\\\(ATTR{address}==\"\\\)[^\"]\\+@\\1${TmacArr[$i]}@}''  ${ruleFile}
    #echo $#
    done

    FmacArr=($FmacStr)
    for((i=0;i<${#FmacArr[@]};i++))
    do
            n=i
            min=${FmacArr[$i]}
            for((j=i+1;j<${#FmacArr[@]};j++))
            do
                    if [[ "${FmacArr[$j]}" < "$min" ]]
                    then
                            n=$j
                            min=${FmacArr[$j]}
                    fi
            done
            t=${FmacArr[$n]}
            FmacArr[$n]=${FmacArr[$i]}
            FmacArr[$i]=$t
    done
    #t=${FmacArr[1]}
    #FmacArr[1]=${FmacArr[2]}
    #FmacArr[2]=$t
    for((i=0;i<${#FmacArr[@]};i++))
    do
    sed -i ''/eth$(expr $i + 4)/{s@\\\(ATTR{address}==\"\\\)[^\"]\\+@\\1${FmacArr[$i]}@}'' ${ruleFile}
    done
    echo "To be reboot..."
    init 6

[source code](https://github.com/snowyxx/MyTest/tree/master/udev_network_interface_name_mac){:target="_blank"}    


注：
shell脚本调试方法：        
`bash/sh -x xxx.sh`

参考：             
[http://packages.debian.org/squeeze/ifrename](http://packages.debian.org/squeeze/ifrename){:target="_blank"}            
[http://www.debian.org/doc/manuals/debian-reference/ch05.en.html#_the_network_interface_name](http://www.debian.org/doc/manuals/debian-reference/ch05.en.html#_the_network_interface_name){:target="_blank"}                
[http://www.debian.org/doc/manuals/debian-reference/ch03.en.html#_the_udev_system](http://www.debian.org/doc/manuals/debian-reference/ch03.en.html#_the_udev_system){:target="_blank"}                  
[http://www.reactivated.net/writing_udev_rules.html](http://www.reactivated.net/writing_udev_rules.html){:target="_blank"}