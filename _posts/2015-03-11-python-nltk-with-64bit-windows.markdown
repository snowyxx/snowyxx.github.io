---
layout: post
title:  "64位Windows使用NLTK"
date:   2015-03-11 13:32:09 +0800
categories: 笔记
tags:   [python]
---
从NLTK的网站看到只支持32位的Windows：   
Install Python: [http://www.python.org/download/releases/3.4.1/](http://www.python.org/download/releases/3.4.1/){:target="_blank"} (avoid the 64-bit versions


我恰好正在使用64位的Python，所以也想一起使用64位的NLTK。
从[http://www.lfd.uci.edu/~gohlke/pythonlibs](http://www.lfd.uci.edu/~gohlke/pythonlibs/){:target="_blank"}/找到NLTK         
下载合适的版本，当前我下载的是         
nltk‑3.0.1‑py2.py3‑none‑any.whl

当然还有64位的Numpy           
numba‑0.17.0‑cp34‑none‑win_amd64.whl

然后使用`pip install xxx.whl`安装

然后有意思的事是，当我使用nltk.clean_html的时候出现了错误：

    To remove HTML markup, use BeautifulSoup's get_text() function

好可爱啊。 原因：           
[https://github.com/nltk/nltk/commit/39a303e5ddc4cdb1a0b00a3be426239b1c24c8bb](https://github.com/nltk/nltk/commit/39a303e5ddc4cdb1a0b00a3be426239b1c24c8bb){:target="_blank"}

那么就安装个64位的BeautifulSoup吧：               
还是在[http://www.lfd.uci.edu/~gohlke/pythonlibs](http://www.lfd.uci.edu/~gohlke/pythonlibs){:target="_blank"}下载：         
beautifulsoup4‑4.3.2‑py3‑none‑any.whl

但是beautifulsoup的get_text()没有去掉script标签中的内容。                          
几种解决方法：[http://stackoverflow.com/questions/328356/extracting-text-from-html-file-using-python](http://stackoverflow.com/questions/328356/extracting-text-from-html-file-using-python){:target="_blank"}