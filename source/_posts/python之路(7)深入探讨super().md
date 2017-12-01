---
title: python之路(7):深入探讨super()
date: 2016-12-01 10:08:57
tags: python
categories: python
---

### 入门使用
在类的继承中，如果我们在子类重新定义了父类的同名方法，子类里的方法就会覆盖父类的。如果我们想调用父类的方法，就有两种：
1.直接绑定父类的名称。
2.使用`super()`。
```python
#创建一个父类
class Father():
	def __init__(self):
		self.name = self.__class__.__name__
	def say_hello(self):
		print('Father')
		print(self.name)

#绑定的方法调用父类say_hello
class Son1(Father):
	def say_hello(self):
		print('-'*20)
		print('Son1')
		#这里的self就是子类Son1的实例
		Father.say_hello(self)

#super()调用父类say_hello
class Son2(Father):
	def say_hello(self):
		print('-'*20)
		print('Son2')
		#这里的self就是子类Son1的实例
		super(Son2,self).say_hello()

#实例化子类
son1 = Son1()
son1.say_hello()
son2 = Son2()
son2.say_hello()

#输出
>>> 
--------------------
Son1
Father
Son1
--------------------
Son2
Father
Son2
```
这样咋一看，两种方法是一样的。`super()`好像很简单，无非就是获取了父类的方法，然后调用了父类的方法。实际上`super()`和父类没有关系。。。。。，上面的例子只是因为`super()`刚好获取到的类是父类。
#### 深入探讨
如果我们用一个复杂一点的例子，就很容易看出问题所在了：
```python
#创建一个基类Base
class Base():
	def __init__(self):
		print('enter Base')
		print('leave Base')

#创建父类为Base的两个子类A,B
class A(Base):
	def __init__(self):
		print('enter A')
		super(A,self).__init__()
		print('leave A')
		
class B(Base):
	def __init__(self):
		print('enter B')
		super(B,self).__init__()
		print('leave B')

#创建A,B的子类C
class C(A,B):
	def __init__(self):
		print('enter C')
		super(C,self).__init__()
		print('leave C')

#输出
>>> c=C()
enter C
enter A
enter B
enter Base
leave Base
leave B
leave A
leave C
```
可以看到，如果`super()`是继承父类的话，`enter A`的下一句应该是`enter Base`，而不是`enter B`。

#### MRO列表
实际上`super()`的调用顺序是根据`MRO`列表来的，也称之为**方法解析顺序列表**，它决定了类的继承顺序。
我们可以通过以下的方式来获取：
```python
>>> C.mro() #or C.__mro__ or C().__class__.mro()
[<class '__main__.C'>, <class '__main__.A'>, <class '__main__.B'>, <class '__main__.Base'>, <class 'object'>]
```
使用这个要注意三点原则：
1.子类永远排在父类的前面。
2.有多个父类的话，按照`MRO`列表中的顺序来判断。
3.下一个类如果有两种选择的话，选择第一个父类。
#### super元类
super()`的代码如下：
```python
def super(cls,self):
	#获取MRO列表
	mro = self.__class__.mro()
	#获取当前类cls的下一个类
	return mro[mro.index(cls) + 1]
```
这里的`cls`是类，`self`是实例。当我们使用`super(cls,self)`就会`self`的`MRO`列表里搜寻`cls`类的下一个类。
我们运行`super(C,self).__init__()`，因为
```python
#self.__class__.mro()的结果mro列表
[<class '__main__.C'>, <class '__main__.A'>, <class '__main__.B'>, <class '__main__.Base'>, <class 'object'>]
```
所以下一个类是`<class '__main__.A'>`,打印`enter A`然后运行`super(A,self).__init__()`,因为下一个是类是` <class '__main__.B'>`，所以打印`enter B`，而不是`enter Base`。
豁然开朗。