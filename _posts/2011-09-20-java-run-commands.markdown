---
layout: post
title:  "Java执行系统命令并输出结果"
date:   2011-09-20 12:44:16 +0800
categories: 笔记
tags:   [java]
---
{% highlight java %}
    public static void getFreeSpace(String path) throws Exception {
        Process p = Runtime.getRuntime().exec("df -h /" + path);
        //Windows should add "cmd /c" at the fround of command
        InputStream reader = new BufferedInputStream(p.getInputStream());
        // StringBuffer buffer=new StringBuffer();//if use the StringBuffer, the
        // Chinese in output will be garbled.
        ByteArrayOutputStream bos = new ByteArrayOutputStream();
        int c = -1;
        while ((c = reader.read()) != -1) {
            // buffer.append((char)c);
            bos.write(c);
        }
        // String output=buffer.toString();
        String output = bos.toString();
        System.out.println(output);
    }
{% endhighlight %}