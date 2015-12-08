---
layout: post
title:  "python交互模式下的tab自动补全"
date:   2015-01-07 10:39:37 +0800
categories: blog
tags:   [python]
---
    import sys
    sys.path

在显示的path选择一个，转到那个路径，添加一个新文件tab.py：

    ​# python startup file
    import sys
    import readline
    import rlcompleter
    import os
    readline.parse_and_bind('tab: complete')
    histfile = os.path.join(os.environ['HOME'], '.pythonhistory')

然后启动python交换模式

    import tab

接下来就可以使用tab自动补​

​**Mac**
因为默认没有readline，要使用homebrew安装，还要安装最新版的python或python3

    brew install readline python

在sys.path一个路径中添加文件tab.py：

    try:
        import readline
    except ImportError:
        print("Module readline not available.")
    else:
        import rlcompleter
        readline.parse_and_bind("tab: complete")

然后启动python交换模式，import sys

补充：如果brew安装新版python后，系统默认还是老版本，检查/etc/paths和​~/.bash_profile 文件，保证/usr/local/bin在/usr/bin前面

运行`source ​~/.bash_profile`

参考：             
[http://www.cnblogs.com/jiraiya1024/p/3498459.html​](http://www.cnblogs.com/jiraiya1024/p/3498459.html​){:target="_blank"}          
[https://nicolas.perriault.net/code/2010/python-tab-completion/](https://nicolas.perriault.net/code/2010/python-tab-completion/){:target="_blank"}          
[http://stackoverflow.com/questions/5157678/python-homebrew-by-default](http://stackoverflow.com/questions/5157678/python-homebrew-by-default){:target="_blank"}