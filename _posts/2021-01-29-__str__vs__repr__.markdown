---
layout: post
title:  "__str__vs__repr__"
date:   2021-01-29 09:24:28 +0800
categories: jekyll update
---
#### 标准库的类
我们先通过 `date` 模块里的 `today` 类来直观认识下 `__str__()`  和 `__repr__()` 。
```python
>>> from datetime import date
>>> today = date.today()
>>> print(today)
2021-01-18
>>> today
datetime.date(2021, 1, 18)
```
在上面的例子中，首先创建一个 `today` 对象，然后输出 `today` 对象。我们发现，直接输出 `today` 对象和调用 `print` 函数打印 `today` 对象的输出是不一样的。直接输出 today 对象的结果为 `datetime.date(2021, 1, 18)` ，调用 print 函数打印的输出为 `2021-01-18` 。这是因为调用 `print` 函数打印 `today` 对象隐式调用了 `str()` 方法，而 `str()` 方法会隐式地调用对象的 `__str__()` 方法。直接输出对象会隐式的调用对象的 `__repr__()` 方法。我们也可以直接调用 `str()` 方法和 `repr()` 方法进行输出。
```python
>>> from datetime import date
>>> today = date.today()
>>> str(today)
'2021-01-18'
>>> repr(today)
'datetime.date(2021, 1, 18)'
```
输出的结果和上面是一样的。
#### 自定义类
1. 类中不包含 `__str__()` 和 `__repr__()`
```python
>>> class Dog:
...     def __init__(self, name, age):
...         self.name = name
...         self.age = age
...
...
...
>>> my_dog = Dog("willie", 3)
>>> my_dog
<__main__.Dog object at 0x7f86e42c4c10>
>>> print(my_dog)
<__main__.Dog object at 0x7f86e42c4c10>
```
在 `Dog`  类中，我们没有定义方法 `__str__` 和 `__repr__` 。可以看到在没有定义 `__str__` 和 `__repr__` 方法的情况下，不管是直接输出对象还是调用 `print()` 函数进行输出，输出结果都是 `<__main__.Dog object at 0x7f86e42c4c10>`。在这种情况下使用的是缺省的 `__str__()` 和 `__repr__()` 。
2. 类中包含  `__str__()`
下面在类中添加 `__str__()` 方法。
```python
>>> class Dog:
...     def __init__(self, name, age):
...         self.name = name
...         self.age = age
...
...
...     def __str__(self):
...         return f"{self.__class__.__name__}({self.name}, {self.age})"
...
...
>>> my_dog = Dog("willie", 3)
>>> my_dog
<__main__.Dog object at 0x7f86e4a20520>
>>> print(my_dog)
Dog(willie, 3)
```
在添加 `__str__` 方法之后，对对象直接进行输出的结果和没有添加方法 `__str__()` 是一样的。但是调用  `print()`  函数进行输出时，输出结果变成了 `Dog(willie, 3)`，这是因为在定义了 `__str__()` 方法之后，调用 `print()` 函数进行输出时，调用了类的 `__str__()` 方法。
3. 类中包含 `__str__()` 和 `__repr__()`
接下来我们继续添加 `__repr__()` 方法。
```python
>>> class Dog:
...     def __init__(self, name, age):
...         self.name = name
...         self.age = age
...
...
...     def __str__(self):
...         return f"{self.__class__.__name__}({self.name}, {self.age}),__str__()"
...
...
...     def __repr__(self):
...         return f"{self.__class__.__name__}({self.name}, {self.age}),__repr__()"
...
...
>>> my_dog = Dog("willie", 3)
>>> my_dog
Dog(willie, 3),__repr__()
>>> print(my_dog)
Dog(willie, 3),__str__()
```
在继续添加 `__repr__()` 方法之后，对对象进行直接输出时，调用 `__repr__()` 方法，输出结果为 `Dog(willie, 3),__repr__()`。调用 `print()` 函数对对象进行输出时，调用 `__str__()` 方法，输出结果为 `Dog(willie, 3),__str__()` 。
4. 类中包含  `__repr__()`
```python
>>> class Dog:
...     def __init__(self, name, age):
...         self.name = name
...         self.age = age
...
...
...     def __repr__(self):
...         return f"{self.__class__.__name__}({self.name}, {self.age})"
...
...
>>> my_dog = Dog("willie", 3)
>>> my_dog
Dog(willie, 3)
>>> print(my_dog)
Dog(willie, 3)
```
在去掉 `__str__()` 方法之后，不管是直接输出对象还是调用 `print()` 函数进行输出，输出结果都是 `Dog(willie, 3)`。这表明，当类中只包含 `__repr__()` 方法时，不管是直接输出还是调用 `print()` 函数进行输出，都会调用类的 `__repr__()` 方法。
#### 区别
`__str__()` 用于为最终用户创建输出，而 `__repr__()` 主要用于调试和开发。
#### 建议
为任何自定义的类添加 `__repr__()` 方法。
#### 总结
当自定义类中没有定义 `__str__()` 和 `__repr__()` 时，在进行对象的输出时，会调用默认的  `__str__()` 和 `__repr__()` ；当类中只包含  `__str__()` 时，调用 `print()` 函数进行对象的输出，会调用 `__str__()`，直接输出调用默认的  `__repr__()`；当类中既包含  `__str__()` 又包含  `__repr__()` 时，调用 `print()` 函数进行对象的输出，会调用 `__str__()`，直接输出会调用  `__repr__()`；当类中只包含  `__repr__()` 时，调用 `print()` 函数进行对象的输出和直接输出都会调用 `__repr__()`。