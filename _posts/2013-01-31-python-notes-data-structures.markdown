---
layout: post
title:  "python笔记：数据结构"
date:   2013-01-31 16:34:14 +0800
categories: blog
tags:   [python]
---
python有4种内置的数据结构（表示一组数据的集合）：            
list :
           
- 使用方括号[]
- 可修改     
- help(list)

---

    #!python
    #filename using_list.py
    #this is my shopping list
    shoplist=['apple','mango','carrot','banan']
    print('I have',len(shoplist),'items to purchase.')
    print('These items are:',end=' ')
    for item in shoplist:
        print(item, end=' ')

    print('\nI also have to buy rice.')
    shoplist.append('rice')
    print('My shopping list is now',shoplist)

    print('I will sort my shopping list now.')
    shoplist.sort()
    print('Sorted shopping list is',shoplist)

    print('The first item I wil buy is',shoplist[0])

    olditem=shoplist[0]
    del shoplist[0]
    print('I bought the',olditem)
    print('my shopping list is now',shoplist)


    delimiter = '_*_'
    mylist = ['Brazil', 'Russia', 'India', 'China']
    print(delimiter.join(mylist))

---

tuple:

- 使用小括号()
- 不可修改
- 空的tuple：emptytuple()
- 只有个一个元素的tuple：singletuple(x,)
- list和tuple在其他的list、tuple中的索引不变（这和perl不同）

---

    #!python
    #filename using_tuple.py
    zoo=('python','elephant','penguin')#括号是可选的
    print('Number of animals in the zoo is',len(zoo))

    new_zoo='monkey','camel',zoo
    print('Number of cages in the new zoo is',len(new_zoo))
    print('All animals in new zoo are',new_zoo)
    print('Animals bought from old zoo are',new_zoo[2])
    print('Last animal brought from old zoo is', new_zoo[2][2])
    print('Number oof animals in the new zoo is',len(new_zoo)-1+len(new_zoo[2]))

---

dictionary:

- help(dict)

---

    #!python
    #filename using_dict.py
    ab={'Swaroop'   :'swaroop@swaroopch.com',
        'Larry'     :'larry@wall.org',
        'Matsumoto' :'matz@ruby-lang.org',
        'Spammer'   :'spammer@hotmail.com'
        }
    print("Swaroop's address is",ab['Swaroop'])

- 另外一种定义dict的方法

{% highlight python %}
    bob=dict(name='Bob Smith',age=42,pay=30000,job='dev')
{% endhighlight %}

- 还可以这样定义       

{% highlight python %}
    >>> sue={}
    >>> sue['name']='Sue Jones'
    >>> sue['age']=45
    >>> sue['pay']=40000
    >>> sue['job']='hdw'
    >>> sue
    {'job': 'hdw', 'pay': 40000, 'age': 45, 'name': 'Sue Jones'}
{% endhighlight %}

- 还有zip：

{% highlight python %}
    >>> names=['name','aga','pay','job']
    >>> values=['Sue Jones',45,40000,'hdw']
    >>> list(zip(names,values))
    [('name', 'Sue Jones'), ('aga', 45), ('pay', 40000), ('job', 'hdw')]
    >>> sue=dict(zip(names,values))
    >>> sue
    {'aga': 45, 'pay': 40000, 'job': 'hdw', 'name': 'Sue Jones'}
{% endhighlight %}

- 删除

{% highlight python %}
    del ab['Spammer']
    print('\nThere are {0} contacts in the address-book\n'.format(len(ab)))
    for name,address in ab.items():
        print('Contact {0} at {1}'.format(name,address))
{% endhighlight %}

- 添加

{% highlight python %}
    ab['Guido']='guido@python.org'
    if 'Guido' in ab:
        print("\nGuido's address is", ab['Guido'])
{% endhighlight %}

---

sequence

- 主要功能是“关系”和“索引”

---

    #!python
    #filename seq.py
    shoplist=['apple','mango','carrot','banana']
    name='swaroop'
    #索引
    print('Item 0 is',shoplist[0])
    print('Item 1 is',shoplist[1])
    print('Item 2 is',shoplist[2])
    print('Item 3 is',shoplist[3])
    print('Item -1 is',shoplist[-1])
    print('Item -2 is',shoplist[-2])
    print('Item -3 is',shoplist[-3])
    print('Item -4 is',shoplist[-4])
    print('Charactoer 0 is',name[0])
    #片段
    print('\nItem 1 to 3 is',shoplist[1:3])
    print('Item 2 to end is',shoplist[2:])
    print('Item 1 to -1 is',shoplist[1:-1])
    print('Item start to end is', shoplist[:])

    print('\ncharacter 1 to 3 is',name[1:3])
    print('character 2 to end is',name[2:])
    print('character 1 to -1 is',name[1:-1])
    print('character start to end is',name[:])

    #step
    print('\nDefault step is 1',shoplist[::1])
    print('step is 2',shoplist[::2])
    print('step is 3',shoplist[::3])
    print('step is -1',shoplist[::-1])

---

set

- 无序

---
    #!python
    #filename set.py
    bri=set(['brazil','russia','india'])
    print('india' in bri)
    print('usa' in bri)

    bric=bri.copy()
    bric.add('china')
    print(bric.issuperset(bri))

    bri.remove('russia')
    print(bri&bric)
    print(bri.intersection(bric))

---
reference

---
    #!python
    #filename reference.py
    print('Smiple Assignment')
    shoplist=['apple','mango','carrot','banana']

    mylist=shoplist

    del shoplist[0]
    print('shoplist is',shoplist)
    print('mylist is',mylist)

    print('Copy by making a full slice')
    mylist=shoplist[:]
    del mylist[0]

    print('shoplist is',shoplist)
    print('mylist is',mylist)

---

参考：[python Data Structures](http://www.swaroopch.com/notes/python_en-Data_Structures/){:target="_blank"}