---
layout: post
title:  "统计字符串中每个字符出现的次数 "
date:   2012-01-13 16:15:53 +0800
categories: blog
tags:   [perl,java]
---
### Pelr：
`perl -e '$/=undef;$_=<>; printf "$&:%d\n", s/$&//g while /\S/;' test.txt`

### Java：
{% highlight java %}
import java.util.*;
public class charCounter {  
    public static void main(String args[]){ 
        String s = "abcDFDSFDSS,adfa....****中国SSGdad";
        Map<Character, Integer> result = getCharMaps(s);
        System.out.println(result);
        
   } 
    public static Map<Character, Integer> getCharMaps(String s) {
        Map<Character, Integer> map = new HashMap<Character, Integer>();
        for(int i = 0; i < s.length(); i++) {
            Character c = s.charAt(i);
            Integer count = map.get(c);
            map.put(c, count == null ? 1 : count + 1);
        }
        return map;
    }
} 
{% endhighlight %}

参考：     
[http://topic.csdn.net/u/20071031/21/25716ed2-64b5-44a5-b335-e61ca5ee4356.html](http://topic.csdn.net/u/20071031/21/25716ed2-64b5-44a5-b335-e61ca5ee4356.html){:target="blank"}
[http://www.docin.com/p-44064652.html](http://www.docin.com/p-44064652.html){:target="blank"}