---
layout: post
title:  "使用线程转储分析Java的高CPU和死锁问题"
date:   2017-02-27 14:58:05 +0800
categories: blog
tags:   [java]
---

* 目录
{:toc}

### 问题

- java进程占用高CPU利用率
- 处理器性能下降缓慢

### 分析问题方法

1. 找到高CPU利用率的进程ID。 

    - Linux `top`   
    - Windows  `taskmgr` 
    - 或者Windows `wmic path Win32_PerfFormattedData_PerfProc_Process get Name,PercentProcessorTime,ThreadCount,CreatingProcessID`
    
    ![tasklist](/images/td.png)
    一个可以用来模拟高CPU利用率的java代码：

    ```java
    package test;
    public class ThreadDumpTest {
            public void test(){
                    for (int i = 0; i < 10 ; i++) {
                            Thread th=new Thread(new TR(i));
                            th.setName(“MyThread-“+(1000+i));
                            th.start();
                    }
            }
            public static void main(String[] args) {
                    ThreadDumpTest t=new ThreadDumpTest();
                    t.test();
            }
            private class TR implements Runnable{
                    int ins=0;
                    TR(int i){
                            ins=i;
                    }
                    public void run(){
                            while (true) {
                                    if(ins!=5) {
                                            try {
                                                    Thread.sleep(10000);
                                            } catch (Exception e) {
                                                    e.printStackTrace();
                                            }
                                    }
                            }
                    }
            }
    }
    ```    
    
2. 找到该进程ID中的高CPU的线程ID。

    - Linux在top中按`shift + h`  
    - Windows  `pslist -d 337xx(进程ID)` (<https://technet.microsoft.com/en-us/sysinternals/bb896682.aspx>)

    ![threadlist](/images/tdth.png)
    
    另外一个java命令直接使用进程名称获得线程ID `ps -mo pid.lwp.stime.time.cpu -C java` 
    
    ```shell
    [user@linux ~]$ ps -mo pid.lwp.stime.time.cpu -C java
         PID         LWP          STIME                  TIME        %CPU
    10029               -         Dec07          00:02:02           99.5
             -       10039        Dec07          00:00:00              0.1
             -       10040        Dec07          00:00:00           95.5
    ```  
    
    把线程ID，转为16进制，例如7087转为1BAF

3. 对该线程ID的线程转储（Thread Dump）`jstack -f 5824`  
    
    > jstack 来自JDK
    
    得到的线程转储例子：
    
    ```log
    Full thread dump Java HotSpot(TM) Client VM (1.5.0_06-b05 mixed mode):
    “DestroyJavaVM” prio=1 tid=0x083d2d78 nid=0x1ba2 waiting on condition [0x00000000..0xbff4bf60]
    “MyThread-1009” prio=1 tid=0x08510a88 nid=0x1bb3 waiting on condition [0xa9cca000..0xa9ccb020]
            at java.lang.Thread.sleep(Native Method)                                             
            at test.ThreadDumpTest$TR.run(ThreadDumpTest.java:45)                   
            at java.lang.Thread.run(Thread.java:595)                                             
    “MyThread-1008” prio=1 tid=0x08510318 nid=0x1bb2 waiting on condition [0xa9d4b000..0xa9d4bea0]
            at java.lang.Thread.sleep(Native Method)                                             
            at test.ThreadDumpTest$TR.run(ThreadDumpTest.java:45)                                
            at java.lang.Thread.run(Thread.java:595)                                             
    “MyThread-1007” prio=1 tid=0x0850f298 nid=0x1bb1 waiting on condition [0xa9dcc000..0xa9dccf20]
            at java.lang.Thread.sleep(Native Method)                                             
            at test.ThreadDumpTest$TR.run(ThreadDumpTest.java:45)                                
            at java.lang.Thread.run(Thread.java:595)                                             
    “MyThread-1006” prio=1 tid=0x0850d818 nid=0x1bb0 waiting on condition [0xa9e4d000..0xa9e4dda0]
            at java.lang.Thread.sleep(Native Method)                                             
            at test.ThreadDumpTest$TR.run(ThreadDumpTest.java:45)                                
            at java.lang.Thread.run(Thread.java:595)                                             
    “MyThread-1005” prio=1 tid=0x0850c9f8 nid=0x1baf runnable [0xa9ece000..0xa9ecee20]
            at test.ThreadDumpTest$TR.run(ThreadDumpTest.java:43)                    
            at java.lang.Thread.run(Thread.java:595)                                 
    “MyThread-1004” prio=1 tid=0x0850b8e8 nid=0x1bae waiting on condition [0xa9f4f000..0xa9f500a0]
            at java.lang.Thread.sleep(Native Method)                                             
            at test.ThreadDumpTest$TR.run(ThreadDumpTest.java:45)                                
            at java.lang.Thread.run(Thread.java:595)                                             
    “MyThread-1003” prio=1 tid=0x0850aac8 nid=0x1bad waiting on condition [0xa9fd0000..0xa9fd1120]
            at java.lang.Thread.sleep(Native Method)                                             
            at test.ThreadDumpTest$TR.run(ThreadDumpTest.java:45)                                
            at java.lang.Thread.run(Thread.java:595)                                             
    “MyThread-1002” prio=1 tid=0x085099b8 nid=0x1bac waiting on condition [0xaa051000..0xaa051fa0]
            at java.lang.Thread.sleep(Native Method)                                             
            at test.ThreadDumpTest$TR.run(ThreadDumpTest.java:45)                                
            at java.lang.Thread.run(Thread.java:595)                                             
    “MyThread-1001” prio=1 tid=0x08508a28 nid=0x1bab waiting
    on condition [0xaa0d2000..0xaa0d3020]
            at java.lang.Thread.sleep(Native Method)                                             
            at test.ThreadDumpTest$TR.run(ThreadDumpTest.java:45)
            at java.lang.Thread.run(Thread.java:595)
    “MyThread-1000” prio=1 tid=0x08508520 nid=0x1baa waiting on condition [0xaa153000..0xaa153ea0]
            at java.lang.Thread.sleep(Native Method)
            at test.ThreadDumpTest$TR.run(ThreadDumpTest.java:45)
            at java.lang.Thread.run(Thread.java:595)

    ```

#### 找出高CPU占用的线程

在线程转储中搜索16位线程ID，定位到具体的java方法。 `nid=0x1baf `

```log
“MyThread-1005” prio=1 tid=0x0850c9f8 nid=0x1baf runnable [0xa9ece000..0xa9ecee20]
        at test.ThreadDumpTest$TR.run(ThreadDumpTest.java:43)                    
        at java.lang.Thread.run(Thread.java:595)       
```

#### 找出死锁线程

线程转储中的死锁示例：

```log
" DB-Processor-13" daemon prio=5 tid=0x003edf98 nid=0xca waiting for monitor entry [0x000000000825f000]
java.lang.Thread.State: BLOCKED (on object monitor)
                at beans.ConnectionPool.getConnection(ConnectionPool.java:102)
                - waiting to lock <0xe0375410> (a beans.ConnectionPool)
                at beans.cus.ServiceCnt.getTodayCount(ServiceCnt.java:111)
                at beans.cus.ServiceCnt.insertCount(ServiceCnt.java:43)
"DB-Processor-14" daemon prio=5 tid=0x003edf98 nid=0xca waiting for monitor entry [0x000000000825f020]
java.lang.Thread.State: BLOCKED (on object monitor)
                at beans.ConnectionPool.getConnection(ConnectionPool.java:102)
                - waiting to lock <0xe0375410> (a beans.ConnectionPool)
                at beans.cus.ServiceCnt.getTodayCount(ServiceCnt.java:111)
                at beans.cus.ServiceCnt.insertCount(ServiceCnt.java:43)
" DB-Processor-3" daemon prio=5 tid=0x00928248 nid=0x8b waiting for monitor entry [0x000000000825d080]
java.lang.Thread.State: RUNNABLE
                at oracle.jdbc.driver.OracleConnection.isClosed(OracleConnection.java:570)
                - waiting to lock <0xe03ba2e0> (a oracle.jdbc.driver.OracleConnection)
                at beans.ConnectionPool.getConnection(ConnectionPool.java:112)
                - locked <0xe0386580> (a java.util.Vector)
                - locked <0xe0375410> (a beans.ConnectionPool)
                at beans.cus.Cue_1700c.GetNationList(Cue_1700c.java:66)
                at org.apache.jsp.cue_1700c_jsp._jspService(cue_1700c_jsp.java:120)
```

在线程转储中搜索**BLOCKED**的线程，然后搜索哪个线程持有阻塞线程等待的锁。然后分析这个线程影响性能的原因。


### 用代码生成线程转储的方法

使用JMX**ThreadMXBean**和**ThreadInfo**类

![code to generate java thread dump](/images/Generate-Java-Thread-Dump-Programmatically.png)

---

参考：                                   

- <https://dzone.com/articles/how-analyze-java-thread-dumps>
- <https://blogs.manageengine.com/application-performance-2/appmanager/2011/02/09/identify-java-code-consuming-high-cpu-in-linux-linking-jvm-thread-and-linux-pid.html>
- <http://crunchify.com/how-to-generate-java-thread-dump-programmatically/>
- <https://github.com/oldratlee/useful-scripts/blob/master/show-busy-java-threads.sh>
- <https://my.oschina.net/u/1859679/blog/1552290>