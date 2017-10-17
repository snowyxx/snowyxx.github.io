---
layout: post
title:  "转：java.util.StringTokenizer"
date:   2011-09-20 10:27:35 +0800
categories: 笔记
tags:   [java]
---

转自：[http://www.blogjava.net/chunkyo/archive/2007/01/15/94045.html](http://www.blogjava.net/chunkyo/archive/2007/01/15/94045.html){:target="blank"}

功效:将字符串以定界符为界，分析为一个个的token（可理解为单词），定界符可以自己指定。 


## 构造函数。

#### StringTokenizer(String str) ：
构造一个用来解析str的StringTokenizer对象。java默认的分隔符是“空格”、“制表符(‘\t’)”、“换行符(‘\n’)”、“回车符(‘\r’)”。

#### StringTokenizer(String str, String delim) ：
构造一个用来解析str的StringTokenizer对象，并提供一个指定的分隔符。如new StringTokenizer("A|001:B|002:C|003",":");

### StringTokenizer(String str, String delim, boolean returnDelims) ：
构造一个用来解析str的StringTokenizer对象，并提供一个指定的分隔符，同时，指定是否返回分隔符。

## 方法。
1. 所有方法均为public；
2. 书写格式：［修饰符］　<返回类型> <方法名（［参数列表］）

    - int countTokens() ：返回nextToken方法被调用的次数。如果采用构造函数1和2，返回的就是分隔符数量(例2)。
    - boolean hasMoreTokens() ：返回是否还有分隔符。
    - boolean hasMoreElements() ：结果同2。
    - String nextToken() ：返回从当前位置到下一个分隔符的字符串。
    - Object nextElement() ：结果同4。
    - String nextToken(String delim) ：与4类似，以指定的分隔符返回结果。

## 例子。

split 与 StringTokenizer 的萧萧区别!

{% highlight java %}
import java.util.StringTokenizer;
public class StringTokenizerDemo
{
   public static void main(String args[])
  {
      String str="100|66,55:200|567,90:102|43,54";
      String abc="A|B|C|D";
      StringTokenizer strToke=new StringTokenizer(str,":");//默认不打印分隔符
      //StringTokenizer strToke=new StringTokenizer(str,":",true);//打印分隔符
      //StringTokenizer strToke=new StringTokenizer(str,":",false);//不打印分隔符
      int size=strToke.countTokens();//3 & 5
      System.out.println("strToke count = "+size);
     while(strToke.hasMoreElements())
    {
       System.out.println(strToke.nextToken());
       //System.out.println(strToke.nextElement()); //效果同上
     }
     //String[] str_abc=str.split("\\|");//结果与StringTokenizer一样
     String[] str_abc=str.split("|");//得到不同的结果
     for(int i=0;i<str_abc.length;i++)
     {
        System.out.println(str_abc[i]);
     }
  }
}
{% endhighlight %}