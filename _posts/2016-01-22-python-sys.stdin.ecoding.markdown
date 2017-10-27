---
layout: post
title:  "python system.stdin.encoding为None的问题"
date:   2016-01-22 14:45:05 +0800
categories: blog
tags:   [python,java]
---
在执行[通过API来管理阿里云](/blog/2016/01/20/use-api-to-manage-aliyun.html)中脚本的时候遇到问题：     
在命令行中执行的时候一切正常，但是当我用java的Runtime.exec执行的时候，执行失败。退出码为1。经高人指点终于找到解决方法，记录如下：

* java输出stderr
* python sys.stdin.encoding的设置

## java输出stderr

一直关注标准输出和退出码（`Runtime.getRuntime().exec().waitFor()`）为1，忽略了stderr，所以没有找到退出码为1的原因。这时候就要打印`Runtime.getRuntime().exec().getErrorStream()`

{% highlight java %}
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;


public class execute {
    public static void main(String[] args) {
        String command="";
        command = "python /monitor.py";
        Process p;
        BufferedReader br=null;
        BufferedReader errorbr=null;
        StringBuffer output=new StringBuffer();
        int exitvalue = 0;
        String line;
        try {
            p=Runtime.getRuntime().exec(command);
            br=new BufferedReader(new InputStreamReader(p.getInputStream()));
            errorbr=new BufferedReader(new InputStreamReader(p.getErrorStream()));
            while((line=br.readLine())!=null){
                output.append(line+ "\n");
            }           
            while((line=errorbr.readLine())!=null){
                output.append(line+ "\n");
            }
            br.close();
            exitvalue = p.waitFor();
        } catch (IOException e) {
            e.printStackTrace();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println(output);
        System.out.println(exitvalue);
        
    }

}

{% endhighlight %}

然后终于看到python的错误：

    Traceback (most recent call last):
      File "/Users/yan/Documents/git/aliyun-python-demo/monitor.py", line 106, in <module>
        domains = getDomainList()
      File "/Users/yan/Documents/git/aliyun-python-demo/monitor.py", line 24, in getDomainList
        domains_dirc = domains.getResponse()
      File "/Users/yan/Documents/git/aliyun-python-demo/aliyun/api/base.py", line 220, in getResponse
        signature = sign(self.__access_key_secret,parameters)
      File "/Users/yan/Documents/git/aliyun-python-demo/aliyun/api/base.py", line 35, in sign
        canonicalizedQueryString += '&' + percent_encode(k) + '=' + percent_encode(v)
      File "/Users/yan/Documents/git/aliyun-python-demo/aliyun/api/base.py", line 45, in percent_encode
        res = urllib.quote(encodeStr.decode(sys.stdin.encoding).encode('utf8'), '')
    TypeError: decode() argument 1 must be string, not None

## python sys.stdin.encoding的设置

根据上面错误信息，sys.stdin.encoding为None是根本原因。

sys.stdin.encoding是根据操作系统环境值来的。在命令行／终端中不是None，但是在重定向、管道、第三方工具（sublime text、pycharm），当然还有通过java Runtime运行的时候，就会是None。下面2个网页把这事说得很明白了，产生原因就不抄在这里了。            
[Stackoverflow.com](http://stackoverflow.com/questions/4545661/unicodedecodeerror-when-redirecting-to-file){:target="_blank"}       
[https://www.v2ex.com/t/163786](https://www.v2ex.com/t/163786){:target="_blank"}

**首先是解决我的问题，即java Runtime中的解决方法：设置`PYTHONIOENCODING=utf-8`**

* Java使用`public Process exec(String[] cmdarray,String[] envp)`方法
    
    {% highlight java %}
    String envp[] = new String[1];
    envp[0]= "PYTHONIOENCODING=utf-8";
    p=Runtime.getRuntime().exec(command,envp);
    {% endhighlight %}
    
* 如果你不更改java，那么使用批处理／shell脚本
    - 批处理
    
            set PYTHONIOENCODING=utf-8
            python.exe c:\monitor.py
       
    - shell脚本

            export PYTHONIOENCODING=utf-8
            python /monitor.py

**sublime text设置`PYTHONIOENCODING=utf-8`**

将Sublime Text默认的 Python Build System的配置文件Python.sublime-build复制到/Packages/User文件夹下加上设置 PYTHONIOENCODING环境变量为UTF-8的内容

    {
        "shell_cmd": "python -u \"$file\"",
        "file_regex": "^[ ]*File \"(...*?)\", line ([0-9]*)",
        "env": {"PYTHONIOENCODING": "utf8"},
        "selector": "source.python"
    }

**PyCharm设置`PYTHONIOENCODING=utf-8`**

File -> Settings... -> Editor -> File Encodings，设置: "IDE Encoding", "Project Encoding"和"Default encoding for properties files" 为utf-8

---

我测试了几种在python代码文件中设置sys.stdin.encoding的方法，都不灵。记录一下供以后参考：

`os.environ['PYTHONIOENCODING'] = os.environ.get("PYTHONIOENCODING","UTF-8")`               
这个sys.stdin.encoding还是None

    import codecs
    sys.stdout = codecs.getwriter("utf-8")(sys.stdout) 
    sys.stdin = codecs.getreader("utf-8")(sys.stdin) 

这个sys.stdin.encoding还是None

    os.environ['PYTHONIOENCODING'] = os.environ.get("PYTHONIOENCODING","utf-8")
    os.execvpe(sys.argv[0],sys.argv,os.environ

这个Windows Mac都不能执行