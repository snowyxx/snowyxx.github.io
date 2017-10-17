---
layout: post
title:  "python笔记：函数"
date:   2013-01-28 15:15:25 +0800
categories: 笔记
tags:   [python]
---

    #!python
    #filename func_local.py
    x=50
    
    def func(x):
        print('x is',x)
        x=2
        print('Changed local x to',x)
    
    func(x)
    print('x is still',x)

----

    #!python
    #filename func_global.py
    x=50
    def func():  
        global x
        print('x is ',x)    
        x=2
        print('changed global x to',x)

    func()
    print('Value of x is ',x)

---

    #!python
    #filename func_default.py
    def say(message, times=1):
        print(message*times)

    say('Hello')
    say('Hello ',5)

---

    #!python
    #filename func_key.py
    def func(a,b=5,c=10):
        print('a is',a,'and b is',b,'and c is',c)
    
    func(3,7)
    func(25,c=24)
    func(c=50,a=100)

---

    #!python
    #filename total.py
    def total(initial=5,*numbers,**keywords):
        count=initial
        for number in numbers:
            count+=number
        for key in keywords:
            count+=keywords[key]
        return count

    print(total(10,1,2,3,vegetable=50,fruits=100))

----

    #!python
    #filename keyword_only.py
    def total(initial=5,*numbers,extra_number):
        count=initial
        for number in numbers:
            count+=number
        count+=extra_number
        print(count)

    total(10,1,2,3,extra_number=50)
    total(10,1,2,3)

输出：

    66
    Traceback (most recent call last):
      File "keyword_only.py", line 11, in <module>
        total(10,1,2,3)
    TypeError: total() missing 1 required keyword-only argument: 'extra_number'

---

    #!python
    #filename func_return.py
    def func1():
        x=5
        return x
    def func2():
        pass
    def func3():
        print('I do nothing here')
    print(func1())
    print(func2())
    print(func3())

--------------------------------------------------------

    #!python
    #file name func_doc.py
    def printMax(x,y):
        '''Prints the maximum of two number.

        The two values must be integers.'''
        x=int(x)
        y=int(y)

        if x>y:
            print(x,'is maximum')
        else:
            print(y,'is maximum')

    printMax(3,5)
    print(printMax.__doc__)

说明：   
          
>The convention followed for a docstring is a multi-line string where the first line starts with a capital letter and ends with a dot. Then the second line is blank followed by any detailed explanation starting from the third line. You are strongly advised to follow this convention for all your docstrings for all your non-trivial functions.
You can try it out on the function above - just include help(printMax) in your program. 

---

>Note the terminology used - the names given in the function definition are called parameters whereas the values you supply in the function call are called arguments.

参考：[python funcations](http://www.swaroopch.com/notes/python_en-functions/){:target="_blank"}
