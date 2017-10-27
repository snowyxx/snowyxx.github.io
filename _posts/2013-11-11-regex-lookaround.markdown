---
layout: post
title:  "正则表达式匹配不包含某字符串的行"
date:   2013-11-11 11:21:19 +0800
categories: blog
tags:   [regex]
---
    ^(.(?!stringToExclude))*$
    ^((?!hede).)*$
    ^(?!sample)\w.*$


**Lookaround**

正则表达式中两个有用的结构: 向前查找 & 向后查找 (lookahead & lookbehind), 统称为 lookaround.

lookaround 去判断一个模式在字符串中是否存在, 不管匹配是否成功, 都不返回, 因此 lookaround 也称为断言(assertions).

lookaround 就像字符串的开始结束, 单词边界锚点(^ $ /b)一样, 是 "zero-width assertions".


**Lookahead**

- Positive lookahead
    
    my(?=here) 会匹配 myhere 成功，匹配其中的 my , (?=here) 用来判断 my 之后是否有 here ,有就匹配成功, 否则匹配失败; 匹配成功后 here 不返回;
    
    my(?=here)$ 匹配 myhere 失败, 因为 (?=here) 只是判断 my 之后是否有 here, 他只是一种判定, 并不匹配, 因此 myehre 中 here不能与 $ 匹配, 因此 my(?=here)$ 匹配 myhere 失败;

- Negative lookahead

    my(?!here) 若字符串中有 my, 且 my 后跟的不是 here, 则匹配成功

**Lookbebind**

- Positive lookbehind

    (?<=my)here 若字符串中有 here, 且之前是 my, 则匹配成功

- Negative lookbind

    (?<\!my)here 若字符串中有 here, 且之前不是 my, 则匹配成功


参考：      
[http://stackoverflow.com/questions/406230/regular-expression-to-match-string-not-containing-a-word](http://stackoverflow.com/questions/406230/regular-expression-to-match-string-not-containing-a-word){:target="_blank"}          
[http://fineonly.com/solutions/regex-exclude-a-string](http://fineonly.com/solutions/regex-exclude-a-string){:target="_blank"}          
[http://localhost:4000/blog/2013/11/11/regex-lookaround.html](http://localhost:4000/blog/2013/11/11/regex-lookaround.html){:target="_blank"}