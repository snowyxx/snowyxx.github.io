---
layout: post
title:  "python笔记：IO"
date:   2013-02-05 16:07:48 +0800
categories: 笔记
tags:   [python]
---
-###input()  print()

    #!python
    #filename user_input.py
    def reverse(text):
        return text[::-1]

    def is_palindrome(text):
        return text==reverse(text)

    something=input('Enter text:')
    if(is_palindrome(something)):
        print('Yes, it is a palindrome')
    else:
        print('No, it is not a palindrome')

### string (str类）， help(str)

### 文件

    #!python
    #filename using_file.py
    poem='''\
    Programming is fun
    When the work is done
    if you wanna make your work also fun:
        use Python!
    '''

    f=open('poem.txt','w')
    f.write(poem)
    f.close()

    f=open('poem.txt')
    while True:
        line=f.readline()
        if len(line)==0:
            break
        print(line,end='')
    f.close()

### pickle

    #!python
    #filename pickling.py
    import pickle

    shoplistfile='shoplist.data'

    shoplist=['apple','mango','carrot']

    f=open(shoplistfile,'wb')
    pickle.dump(shoplist,f)
    f.close()

    del shoplist

    f=open(shoplistfile,'rb')
    storedlist=pickle.load(f)
    print(storedlist)

---
参考:[python input output](http://www.swaroopch.com/notes/python_en-input_output/){:target="_blank"}
