---
layout: post
title:  "几种通过IPMI获取IBM服务器硬件信息的方法"
date:   2016-08-24 14:42:05 +0800
categories: 笔记
tags:   [python, IPMI]
---

* 目录
{:toc}


### IPMI

IPMI（Intelligent Platform Management Interface）即智能平台管理接口是使硬件管理具备“智能化”的新一代通用接口标准。
用户可以利用IPMI监视服务器的物理特征，如温度、电压、电扇工作状态、电源供应以及机箱入侵等。
IPMI最大的优势在于它是独立于CPU BIOS和OS的，所以用户无论在开机还是关机的状态下，只要接通电源就可以实现对服务器的监控。
IPMI是一种规范的标准，其中最重要的物理部件就是BMC(Baseboard Management Controller)，一种嵌入式管理微控制器，它相当于整个平台管理的“大脑”，通过它 IPMI可以监控各个传感器的数据并记录各种事件的日志。
是管理基于Intel架构的企业系统中所使用的外围设备采用的一种工业标准，该标准由英特尔、惠普、NEC、美国戴尔电脑和SuperMicro等公司制定。
一般这时候会接触到“带外”这个概念：

> 带外：使用低级硬件智能管理（带外）而不使用操作系统进行管理（带内）。

[Intel官方网站](http://www.intel.com/content/www/us/en/servers/ipmi/ipmi-home.html)

BMC：
![bmc](/images/IPMI_BMC.jpg)
IPMI：
![ipmi](/images/IPMIBlockDiagram.gif)

### 启用IPMI和配置网络 

- DELL服务器启动过程中，会提示用户按下Ctrl+E进行有关设置

    ![setupdellipmi](/images/ipmiSetupDell.gif)

- IBM开机自检的过程中根据提示按F1进入UEFI设置

    ![setupibmipmi](/images/imm.gif)
    
### Web控制台 － IMM

使用浏览器打开http://<上面配置的IP地址>。在接下来的登录界面中输入默认的用户名USERID和密码PASSW0RD，其中0是数字0，非字母O。

登录后就可以进行telnet/ssh、SNMP等配置。当然还可以查看各硬件的数据。例如电源、风扇、温度等。IMM2和IMM相比，提供更多内存、磁盘等信息。

黑色界面为代表的IMM界面（M4以前的），在配置完成IP地址后，需要点击左侧的 Restart IMM重新启动IMM。以蓝色界面为代表的IMM2界面（M4以后的），在点击Apply应用配置以后，需要在当前页面停留3分钟左右。不需要重启IMM，配置会自动生效。生效后，需要重新配置笔记本IP。重启IMM不影响操作系统的正常运行，可放心操作。

登录后的IMM的界面如下：
![immconsole](/images/imm1.gif)
IMM2的界面如下：
![imm2console](/images/imm2.gif)

### 命令行telnet/ssh

所有的命令：

```shell
                        Legacy CLI Application

system>?
          ?  --  Display command list
   clearcfg  --  Resets the IMM to its default settings
   clearlog  --  Clear IMM event log
      clock  --  Display/set date, time, GMT offset, and dst setting
    console  --  Exit CLI, attach to serial console
   dhcpinfo  --  View DHCP server assigned settings
        dns  --  Display the current configuration of the IMM
       exit  --  Exit CLI (log off)
       fans  --  Displays the fan speed for all system fans
       help  --  Display command list
    history  --  Display history of last 8 commands
   identify  --  Control system locator LED
   ifconfig  --  Ethernet configuration
       ldap  --  LDAP configuration
        ntp  --  Displays and configures the Network Time Protocol
passwordcfg  --  Password configuration
    portcfg  --  Serial port configuration
      power  --  Control server power
    readlog  --  Displays the IMM event log, five entries at a time
      reset  --  Reset server
    resetsp  --  Reset IMM
        slp  --  View/edit SLP parameters
      srcfg  --  Serial redirection configuration
        ssl  --  SSL configuration
  syshealth  --  System Health
      temps  --  Display system temperatures
   timeouts  --  Server timeouts configuration
      users  --  User profile configuration
     update  --  Update firmware
     usbeth  --  Inband (Ethernet over USB) interface
      volts  --  Displays all the voltages and voltage thresholds
        vpd  --  Display VPD

system>
```

python的telnet连接方法：

```python
import telnetlib
import os
host = '192.168.0.199'
username = 'USERID'
password = 'PASSW0RD'
finish = 'system>'
linesep = os.linesep
commands = ['fans','volts','temps','syshealth']

tn = telnetlib.Telnet(host, port=23, timeout=10)
tn.read_until('login : ')
tn.write(username + linesep)
tn.read_until('Password:')
tn.write(password + linesep)
tn.read_until(finish)
output=''

for command in commands:
    tn.write(command+linesep)
    output+=tn.read_until(finish)
tn.close()
```

### IPMI工具

以ipmiutil为例（http://ipmiutil.sourceforge.net/）连接IPMI端口623/udp

ipmiutil说明：

```shell
$ ipmiutil
ipmiutil ver 2.95
Usage: ipmiutil <command> [other options]
   where <command> is one of the following:
    alarms  show/set the front panel alarm LEDs and relays
    leds    show/set the front panel alarm LEDs and relays
    discover    discover all IPMI servers on this LAN
    cmd send a specified raw IPMI command to the BMC
    config  list/save/restore BMC configuration parameters
    dcmi    get/set DCMI parameters
    ekanalyzer  run EKeying analyzer on FRU files (deprecated, see fru)
    events  decode IPMI events and display them
    firewall    show/set firmware firewall functions
    fru show decoded FRU inventory data, write asset tag
    fwum    OEM firmware update manager extensions
    getevt  get IPMI events and display them, event daemon
    getevent    get IPMI events and display them, event daemon
    health  check and show the basic health of the IPMI BMC
    hpm HPM firmware update manager extensions
    lan show/set IPMI LAN parameters and PEF table
    picmg   show/set picmg extended functions
    power   issue IPMI reset or power control to the system
    reset   issue IPMI reset or power control to the system
    sel show/clear firmware System Event Log records
    sensor  show Sensor Data Records, readings, thresholds
    serial  show/set IPMI Serial & Terminal Mode parameters
    sol start/stop an SOL console session
    smcoem  SuperMicro OEM functions
    sunoem  Sun OEM functions
    delloem Dell OEM functions
    tsol    Tyan SOL console start/stop session
    wdt show/set/reset the watchdog timer
   common IPMI LAN options:
       -N node  Nodename or IP address of target system
       -U user  Username for remote node
       -P/-R pswd  Remote Password
       -E   use password from Environment IPMI_PASSWORD
       -F   force driver type (e.g. imb, lan2)
       -J 0 use lanplus cipher suite 0: 0 thru 14, 3=default
       -T 1 use auth Type: 1=MD2, 2=MD5(default), 4=Pswd
       -V 2 use priVilege level: 2=user(default), 4=admin
       -Y   prompt for remote password
       -Z   set slave address of local MC
For help on each command (e.g. 'sel'), enter:
   ipmiutil sel -?
ipmiutil , usage or help requested
```

python调用ipmiutil方法：

```python
import subprocess

def runCMDBySubProcess(cmd):
    p = subprocess.Popen(cmd, stdout=subprocess.PIPE, stderr=subprocess.PIPE,shell=True)
    output, err = p.communicate()
    p_status = p.wait()
    return (p_status, output)

def main(host, user, passwd):
    sensorCmd = 'ipmiutil sensor  -N '+host+' -U '+user+' -P '+passwd+' -s'
    healthCmd = 'ipmiutil health  -N '+host+' -U '+user+' -P '+passwd

    sensorOutput = runCMDBySubProcess(sensorCmd)
    healthOutput = runCMDBySubProcess(healthCmd)
```

### SNMP

在IMM中配置SNMP代理。如下图：

![setupsnmp](/images/immSNMP.jpg)

IMM的mib文件在IBM Fix Central下载IMM升级包后解压找mib文件。

然后在MIB浏览器中查看硬件数据：

![mibbrowser](/images/snmpmib.gif) 


Java编程获取：

```java
import java.util.ArrayList;
import java.util.HashMap;

import com.adventnet.snmp.beans.SnmpTarget;

public class IpmiSnmpTest {

    public static void main(String[] args) {
        SnmpTarget target = new SnmpTarget();
        String host="192.168.0.199";
        String community="public";
        target.setTargetHost(host);
        target.setTargetPort(161);
        target.setCommunity(community);
        target.setSnmpVersion(0);
        target.setTimeout(30);
        processIBMHWData(target);
    }

    private static void processIBMHWData(SnmpTarget target) {
        HashMap<String,String> tables = new HashMap<String,String>();
        tables.put("TMP", ".1.3.6.1.4.1.2.3.51.3.1.1.2");
        tables.put("VOLT", ".1.3.6.1.4.1.2.3.51.3.1.2.2");
        tables.put("FAN", ".1.3.6.1.4.1.2.3.51.3.1.3.2");
        tables.put("Error", ".1.3.6.1.4.1.2.3.51.3.2.1.1");
        
        HashMap<String,String> attributes=new HashMap<String,String>();
        attributes.put("Power Status", ".1.3.6.1.4.1.2.3.51.3.5.1.1.0");
        attributes.put("System Status",".1.3.6.1.4.1.2.3.51.3.5.1.4.0");

        ArrayList resultt=getTableRows(tables);
            // to handle the result
        String results=getAttributes(attributes);
            // to handle the result
        
    }

    private static ArrayList getTableRows(SnmpTarget target,String oid){
        //method to get table data from snmp agent
    }
    private static String getAttributes(SnmpTarget target,String oid){
        //method to get attribute data from snmp agent
    }
}
```

### SNMP陷阱

在IMM配置SNMP陷阱发送到网管系统：
![snmptrap](/images/immSNMPTraps.jpg) 

---

** [请戳这里查看所有相关代码](https://github.com/snowyxx/MyTest/tree/master/ipmi)

---

参考：                                   
- [配置Nagios监控IPMI硬件设备](http://www.linuxde.net/2013/10/15408.html) 
- [使用 ipmitool 实现 Linux 系统下对服务器的 ipmi 管理](https://www.ibm.com/developerworks/cn/linux/l-ipmi/) 
- [WebNMS SNMP API help](https://www.webnms.com/snmp/help/snmpapi/snmpv1/index.html)
- [WebNMS SNMP API java doc](https://www.webnms.com/snmp/help/snmpapi/snmpv3/javadocs/index.html)
