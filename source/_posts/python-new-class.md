---
title: Python面向对象中：经典类和新式类的区别
date: 2017-07-30 10:08:47
categories: learnpython
tags: 
  - Python
---

> Python 2.x中默认都是经典类，只有显式继承了object才是新式类  
Python 3.x中默认都是新式类，不必显式的继承object  
当类是经典类时，多继承情况下，会按照深度优先方式查找  
当类是新式类时，多继承情况下，会按照广度优先方式查找

1. 写法不一样
```
class A:
    pass

class B(object):
    pass
```
2. 在多继承中，新式类采用广度优先搜索，而经典类是采用深度优先搜索。
```
# 经典类例子
class A:
    def __init__(self):
        print 'This is A'
    def save(self):
        print 'save method from A'
class B (A):
    def __init__(self):
        print 'this is B'
class C (A):
    def __init__(self):
        print 'this is C'
    def save(self):
        print 'save mothod from C'
class D(B,C):
    def __init__(self):
        print 'this is D'
d=D()
d.save()
# 输出结果：经典类的搜索顺序是D->B->A，是一种深度优先查找方式
this is D
save method from A

# 新式类例子：
class A(object):
    def __init__(self):
        print 'This is A'
    def save(self):
        print 'save method from A'
class B (A):
    def __init__(self):
        print 'this is B'
class C (A):
    def __init__(self):
        print 'this is C'
    def save(self):
        print 'save mothod from C'
class D(B,C):
    def __init__(self):
        print 'this is D'
d=D()
d.save()

# 运行结果：新式类的搜索顺序是D->B->C->A，是一种广度优先查找方式
this is D
save mothod from C
```
3. 新式类新增
    1. 新式类对象可以直接通过`__class__`属性获取自身类型:`type`
    2. 新式类增加了`__getattribute__`方法
    3. 新式类增加了`__slots__`内置属性, 可以把实例属性的种类锁定到`__slots__`规定的范围之中。
    ```
    class Person(object):
    __slots__ = ('name', 'age')   #只允许Person拥有name和age属性
    def __init__(self):
        self.name='aline'
        self.age=20
        self.sex='female'
    person=Person()
    
    # 运行结果：报错
    AttributeError: 'Person' object has no attribute 'sex'
    ```