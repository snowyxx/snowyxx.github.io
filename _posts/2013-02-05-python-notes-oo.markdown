---
layout: post
title:  "python笔记：类"
date:   2013-02-05 14:38:45 +0800
categories: 笔记
tags:   [python]
---
    #!python
    #filename objvar.py
    class Robot:
        '''Represents a robot, with a name.'''

        #class variable
        population=0

        def __init__(self,name):
            '''Initializes the data.'''
            self.name=name
            print('Initializing {0}'.format(self.name))

            Robot.population+=1
            
        def __del__(self):
            ''' I am dying...'''
            print('{0} is being destroyed!'.format(self.name))
            Robot.population-=1

            if Robot.population==0:
                print('{0} was the last one.'.format(self.name))
            else:
                print('There are still {0:d} robots working.'.format(Robot.population))

        def sayHi(self):
            '''Creeting by the robot.

        Yeah, they can to that.'''
            print('Creeting, my masters call me {0}.'.format(self.name))

        def howMany():
            '''Prints the current population.'''
            print('We have {0:d} robots.'.format(Robot.population))
        howMany=staticmethod(howMany)

    droid1=Robot('WYL-01')
    droid1.sayHi()
    Robot.howMany()


    droid2=Robot('WYL-02')
    droid2.sayHi()
    Robot.howMany()

    print('Destorying robot....')
    del droid1
    del droid2

    Robot.howMany()

--------------------------------------------------------------------------


    #!python
    #filename inherit.py
    class SchoolMember:
        '''Represents any school member.'''
        def __init__(self,name,age):
            self.name=name
            self.age=age
            print('Initialized SchoolMember:{0}'.format(self.name))
        def tell(self):
            '''Tell me details'''
            print('Name:"{0}" Age:"{1}"'.format(self.name,self.age),end=" ")

    class Teacher(SchoolMember):
        '''Represents to a teacher.'''
        def __init__(self,name,age,salary):
            SchoolMember.__init__(self,name,age)
            self.salary=salary
            print('Initialized Teacher:{0}'.format(self.name))
        def tell(self):
            SchoolMember.tell(self)
            print('Salary:"{0}"'.format(self.salary))

    class Student(SchoolMember):
        '''Represents to a student.'''
        def __init__(self,name,age,marks):
            SchoolMember.__init__(self,name,age)
            self.marks=marks
            print('Inintialized Student:{0}'.format(self.name))
        def tell(self):
            SchoolMember.tell(self)
            print('Marks:"{0:d}"'.format(self.marks))

    t=Teacher('Mrs. Shrividya',40,3000)
    s=Student('Swaroop',25,75)

    print()#print a blank line

    members=[t,s]
    for member in members:
        member.tell()

---

参考：[python object oriented programming](http://www.swaroopch.com/notes/python_en-object_oriented_programming/){:target="_blank"}
