---
layout: post
title:  "python输出重定向到文件时候的UnicodeEncodeError问题"
date:   2015-09-22 09:38:04 +0800
categories: blog
tags:   [python]
---
python 2.7, 重定向输出到文件时候出现错误：

    UnicodeEncodeError: 'ascii' codec can't encode characters in position 41-48: ordinal not in range(128)

解决方法：

    % PYTHONIOENCODING=UTF-8 python xxx.py

参考：[http://stackoverflow.com/questions/4545661/unicodedecodeerror-when-redirecting-to-file](http://stackoverflow.com/questions/4545661/unicodedecodeerror-when-redirecting-to-file){:target="_blank"}

---

###python 2.7打开文件带编码参数

    import io
    with open(fname, "rt", encoding="utf-8") as f:
    [http://stackoverflow.com/questions/10971033/backporting-python-3-openencoding-utf-8-to-python-2](http://stackoverflow.com/questions/10971033/backporting-python-3-openencoding-utf-8-to-python-2){:target="_blank"}

###另外：解码unicode

    >>> s="\u5730\u56fe\u89c6\u56fe"
    >>> print s
    \u5730\u56fe\u89c6\u56fe
    >>> print s.decode("unicode-escape")
    地图视图

###另外：代码中设定编码

    # coding: UTF-8

    reload(sys)
    sys.setdefaultencoding("utf-8")

###另另外：解码html code

    Python 2.6-3.3
    You can also use the HTML parser from the standard lib

    >>> import HTMLParser
    >>> h = HTMLParser.HTMLParser()
    >>> print h.unescape('&pound;682m')
    £682m
    see http://docs.python.org/2/library/htmlparser.html

    Python 3.4+
    HTMLParser.unescape is deprecated, and was supposed to be removed in 3.5, although it was left in by mistake. It will be removed from the language soon. Instead, use html.unescape():

    import html
    print(html.unescape('&pound;682m'))
    see https://docs.python.org/3/library/html.html#html.unescape

###Linux获取文件的编码：

    file --mime known_issues.html~
    known_issues.html~: text/html; charset=iso-8859-1

###Linux转换文件编码：

    iconv -f iso-8859-1 -t utf-8 known_issues.html~ > known_issues.html.utf-8

参考：             
[http://stackoverflow.com/questions/2087370/decode-html-entities-in-python-string](http://stackoverflow.com/questions/2087370/decode-html-entities-in-python-string){:target="_blank"}          
[http://www.zhihu.com/collection/58495075](http://www.zhihu.com/collection/58495075){:target="_blank"}