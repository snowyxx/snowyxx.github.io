---
layout: post
title:  "python笔记：控制结构"
date:   2013-01-28 13:32:23 +0800
categories: 笔记
tags:   [python]
---

    #!python
    #filename if.py
    number =23
    guess= int(input('Enter an integer:'))
    
    if guess==number:
        print('Congratulations, you guessed it.')
        print('(but you do not win any prizes!)')
    elif guess
        print('No, it is a little highter than that')
    else:
        print('No, it is a little lower than that')
    
    print('Dnoe')

---

    #!python
    #filename while.py
    number=23
    running=True
    
    while running:
        guess=int(input('Enter a integer:'))
    
        if guess==number:
            print('Congratulations, you guessed it.')
            running=False
        elif guess< number:
            print('No, it is a little highter that that.')
        else:
            print('No, it is a little lower that that')
    else:
        print('The while loop is over.')
    
    print('Done')   

---

    #!python
    #filename: for.py

    for i in range(1,5):
        print(i)
    else:
        print('The for loop is over')       

---

    #!python
    #filename: break.py
    while True:
        s=input('Enter something:')
        if s=='quit':
            break
        print('Length of the string is', len(s))
    else:
        print('The loop is over') #这句永远都不会执行到，因为break语句不执行和循环相对应的else
    print('Done')

----

    #!python
    #filename:continue.py
    while True:
        s=input('Enter something:')
        if s=='quit':
            break
        if len(s)<3:
            print('Too small')
            continue
        print('Input is of sufficient length')

---
参考: [python control flow](http://www.swaroopch.com/notes/python_en-control_flow/){:target="_blank"}
