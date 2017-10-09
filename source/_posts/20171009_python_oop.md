---
title: Python中的面向对象
date: 2017-10-9
tags: python
category: 技术
---
![](https://joyc.github.io/images/20171009python_oop.jpg)

## Python说四十二章经之三入门面向对象

[TOC]

> 面向对象三大特性：封装、继承和多态

1. 创建类
2. 创建方法  
```python
# 构造方法
__init__(self,arg)
obj = 类('a1')

# 普通方法
obj = 类('xxx')
obj.普通方法名()
```
### 1. 封装  

```python
class Bar:
    def __init__(self, n, a):
        self.name = n
        self.age = a

b1 = Bar('alex', 123)
b2 = Bar('SDF', 23)
```
4. 类成员：字段，方法和属性。
    1. 字段：普通字段，静态字段
    2. 方法：普通方法，类方法，静态方法
    3. 属性：普通属性
    > 注：所有成员中，只有普通字段的内容保存对象中，即：根据此类创建了多少对象，在内存中就有多少个普通字段。而其他的成员，则都是保存在类中，即：无论对象的多少，在内存中只创建一份。

### 2. 继承

#### 多继承

python子类（派生类）可以继承多个父类（基类）

#### 经典类和新式类的区别：
> Python 2.x中默认都是经典类，只有显式继承了object才是新式类  
> Python 3.x中默认都是新式类，不必显式的继承object  
> 当类是经典类时，多继承情况下，会按照深度优先方式查找  
> 当类是新式类时，多继承情况下，会按照广度优先方式查找  

1. 写法不一样
  ```python
  class A:
      pass

  class B(object):
      pass
  ```

2. 在多继承中，新式类采用广度优先搜索，而经典类是采用深度优先搜索。
  ```python
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

  # 注意：在上述查找过程中，一旦找到，则寻找过程立即中断，便不会再继续找了
  ```
3. 新式类新增
    1. 新式类对象可以直接通过`__class__`属性获取自身类型:`type`
    2. 新式类增加了`__getattribute__`方法
    3. 新式类增加了`__slots__`内置属性, 可以把实例属性的种类锁定到`__slots__`规定的范围之中。
    ```python
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

### 3. 多态

python原生支持多态

### 4. 类成员

类成员：字段，方法和属性。

1. 字段：普通字段，静态字段
2. 方法：普通方法，类方法，静态方法
3. 属性：普通属性

> 注：所有成员中，只有普通字段的内容保存对象中，即：根据此类创建了多少对象，在内存中就有多少个普通字段。而其他的成员，则都是保存在类中，即：无论对象的多少，在内存中只创建一份。

#### 字段

- `类.字段` 可以直接访问

##### 普通字段

保存在对象中，只能通过对象访问。

##### 静态字段

当使用公用字段时候，可以使用静态字段来节省内存空间。

保存在类中，可通过兑现访问，也可通过类访问。

#### 方法

- `类.方法()` 加括号访问方法

##### 普通方法

保存在类中，需要先创建对象，然后通过对象调用或者类来调用。

一般通过创建的对象来调用，因为后者需要传参。

参数`self` 指示对象。

##### 静态方法

保存在类中，通过`@staticmethod` 装饰器来创建，方法可以不加`self`参数。

```python
class Foo:

    def bar(self):
        print('bar')

    @staticmethod
    def sta():
        print('123')

    @staticmethod
    def stat(a1, a2):
        print(a1, a2)

Foo.sta()
Foo.stat(1, 2)
```

##### 类方法

保存在类中，用`@classmethod` 来创建类方法。参数一般写成`cls` ，执行时会被自动传入，指示当前类。

```python
class Foo:
    @classmethod
    def classmd(cls):
        # cls是当前类名
        print(cls)
        print('classmd')
```

##### 应用场景

如果对象中需要保存一些值，执行某功能时，需要使用对象中的值，使用普通方法。不需要任何对象中的值时，使用静态方法。

##### 属性

保存在类中，使用`@property` 装饰器创建，像普通定义方法一样需要self参数，像调用属性一样不加括号。

```python
@property
def per(self):
  return 1
sbj = Foo()
r = obj.per
print(r)
```

##### 成员修饰符

属性和方法都有分为公有成员和私有成员

- 私有成员：

   前方加俩下划线： __字段名  

  无法直接访问，只能间接访问，继承父类内部的私有也不能执行，只能执行自己类内部的。

```python
class Foo:

    def __init__(self, name, age):
        self.name = name
        self.age = age
        self.__age = age # 私有,外部无法直接访问

    def show(self):
        return self.__age

obj = Foo('robin', 26)
print(obj.name)
ret = obj.show()
print(ret)


class Foo:
    __v = '123'	# 静态属性

    def __init__(self):
        pass
    def show(self):
        return Foo.__v
    @staticmethod
    def stat():
        return Foo.__v

# ret = Foo().show()
# print(ret)

ret = Foo.stat()
print(ret)

class Foo:

    def __f1(self): #私有方法
       return 123
    def f2(self):	#通过方法f2调用私有的f1方法
        r = self.__f1()
        return r

obj = Foo()
ret = obj.f2()
print(ret)
```

##### 特殊成员

- `__init__` 由类()自动执行
- `__call__` 方法 对象() ，类()() 自动执行

```python
class Foo:
    def __init__(self):
        print('init')

    def __call__(self, *args, **kwargs):
        print('call')

obj = Foo()
obj() # 对象加括号执行的call特殊方法
Foo()() # 执行方式等同于上面的obj()
```

- `__add__`方法

  连个对象相加时，自动执行第一个对象的`__add__` 方法，并且将第二个对象当作参数传递进入

- `__int__` int(对象)

- `__str__` str()

- `__del__` 析构方法，对象被销毁()时，自动执行。

- `__dict__` 将对象中封装的所有成员（包括不可见私有成员），内容通过字典的形式返回

- `__getitem__` ,`__setitem__` 和`__delitem__` 只有第一个getitem有返回值。

- `__iter__` 方法，如果类中有这个方法，对象即为可迭代对象，`对象.__iter__()` 的返回值是迭代器 。

#### metaclass 元类/原始类

1. python中一切皆对象。
2. 创建对象时候默认执行type类中的init方法。
