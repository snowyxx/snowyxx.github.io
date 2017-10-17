---
layout: post
title:  "python笔记：模块"
date:   2013-01-30 14:52:12 +0800
categories: 笔记
tags:   [python]
---
导入模块

    #!python
    #filename using_sys.py
    import sys
    
    print('The command line arguments are:')
    for i in sys.argv:
        print(i)
    
    print('\n\nThe PYTHONPATH is',sys.path,'\n')


    #from sys import argv

---

    #!python
    # filename using_name.py
    if __name__=='__main__':
        print('This program is being run by itself')
    else:
        print('I am imported from other module')

----

    #!python
    #filename mymodule.py
    def sayhi():
        print('Hi, this is mymodule speaking.')
    __version__='0.618'
    something='something'

使用

    #!python
    #filename mymodule_demo.py
    import mymodule
    mymodule.sayhi()
    print('Version',mymodule.__version__)
    print(mymodule.something)
    #from mymodule import sayhi,__version__
    #sayhi()
    #print('Version',__version__')

---

Zen of python:

    >>>import this

---

    >>>import sys
    >>>dir(sys)
    >>>dir()
    >>>a=5
    >>>dir()
    >>>del a
    >>>dir()

