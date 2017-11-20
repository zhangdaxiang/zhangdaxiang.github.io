---
title: python之路(5)面向对象
date: 2017-11-15 16:15:01
tags: python
categories: python
---
### 面向过程编程
讨论面向对象编程的时候，首先要知道与之相对的面向过程编程。先来说面向过程编程，也叫作`top-down languages`，顾名思义，就是一步步执行整个程序，从上到下，从头到尾。基本的思路就是我们如果要解决一个问题,先把这个问题分成小的问题,小的步骤,从而来降低其复杂性。
但是这样有一个致命的缺点，就是代码的可维护性和可读性比较差，如果代码的某些地方要修改，那么从上至下的所有地方都要修改，很不方便。


我们来举个栗子，我们要输出一个学生的信息，比如说姓名和年龄。
如果我们用面向过程编程的话，就分两步，第一步先用`dict`定义一个学生的信息：
```python
student1 = {'name':'zhangdaxiang','age':25}
student2 = {'name':'xiaohui','age':26}
```
然后第二步是用函数打印出学生的信息：
```python
def print_info(std):
	print("%s's age is %d"%(student1['name'],student1['age']))
print_info(student1)
```
### 面向对象编程
而面向对象编程的中心思想则是，"万物皆对象"，把计算机程序视为一系列对象的集合，程序的运行，就是在各个对象之间相互传递信息。这样的话，就有很好的可维护性和拓展性，如果我们想修改什么，只需要修改涉及的对象即可。
#### 类和实例化
如果是使用面向对象编程的话，就是设计一个对象，把姓名和年龄这些个人信息都包含在对象里，叫作**属性**，把打印信息的函数也包含在对象里，叫作**方法**。也就是说，一个对象包含所有的东西，我们要得到属性，可以直接调用这个对象。我们要使用这个对象的方法，也可以直接调用这个对象。所有的东西都封装在对象里，外部仅仅是调用他。
按上面所说的，`student`就是一个对象，我们称之为**类**，包含`name`和`age`两个属性，还有`print_info`这个方法。
```python
class Student():
	def __init__(self,name,age):
		self.name = name
		self.age = age
	def print_info(self):
		print("%s's age is %d"%(self.name,self.age))
```
这样就定义了一个类`class`，我们一般把类名的首字母大写。同时，`student1`和`student2`是`student`这个类的两个具体实现，我们称之为类的实例。概括一些，`Student`类是指学生，而`student1`和`student2`这两个实例则是两个具体的学生个体。
要打印学生信息化的话，先将类实例化，然后每个实例打印各自的信息：
```python
>>> student1 = Student('zhangdaxiang',25)
>>> student2 = Student('xiaohui',25)
>>> student1.name
'zhangdaxiang'
>>> student1.age
25
>>> student1.print_info()
zhangdaxiang's age is 25
```
我们可以给实例的属性重新赋值，也可以给实例添加一个新的属性：
```python
>>> student1.name = 'zhangxiaoxiang'
>>> student1.name
'zhangxiaoxiang'
>>> student1.city = 'hangzhou'
>>> student1.city
'hangzhou'
>>> student2.city
Traceback (most recent call last):
  File "<pyshell#8>", line 1, in <module>
    student2.city
AttributeError: 'Student' object has no attribute 'city'
```
#### 访问限制
从上面我们给`name`属性重新赋值可以看出来，我们现在依然可以通过代码来修改实例的内部属性。我们如果想让内部的属性无法访问的话，我们可以在属性名称前加两条下划线`__`.
```python
class Student():
	def __init__(self,name,age):
		self.__name = name
		self.__age = age
	def print_info(self):
		print("%s's age is %d"%(self.__name,self.__age))
student1 = Student('zhangdaxiang',25)
student2 = Student('xiaohui',25)
```
这样子，我们就不能直接从外部访问这两个属性了：
```python
>>> student1.__name
Traceback (most recent call last):
  File "<pyshell#0>", line 1, in <module>
    student1.__name
AttributeError: 'Student' object has no attribute '__name'
```
实际上不能访问的的原因是因为属性名称变成了`_Student__name`，尝试一下：
```python
>>> student1._Student__name
'zhangdaxiang'
```
如果我们想在外部获取这些属性，可以在类的里面定义一个获取属性的方法：
```python
class Student():
	...

	def get_name(self):
		return self.__name
	
	def get_age(self):
		return self.__age
```
如果想修改类的内部属性的话呢，也可以再增加一个`set`的方法。而且这样做有一个好处，可以加上条件判定。比如我们希望修改的年龄在20岁以上：
```python
class Student():
	...

	def set_age(self,age):
		if age>20:
			self.__age=age
		else:
			print('年龄太小啦！')
```
我们运行一下就可以看到，果然和我们想的一样：
```python
>>> student1.get_age()
25
>>> student1.set_age(10)
年龄太小啦！
>>> student1.set_age(21)
>>> student1.get_age()
21
```
值得注意的是，在`python`中，变量名类似于`__XXX__`这样的是属于特殊变量，是可以直接访问的。还有一种是变量面前只有一个下划线的`_`，比如说`_name`，这样的也是可以直接访问的。但是加这个下划线就是为了告诉我们，虽然我可以被访问，但是请默认我为不可以访问的变量。
#### 继承和多态
我们在定义一个类的时候，是可以继承另外一个类的，新定义的类就称之为子类，被继承的类被称为父类。比如我我们先定义一个类作为父类：
```python
class People():
	def eat(self):
		print('people is eating')
```
然后我们要建两个子类`teacher`和`student`，因为这两个子类也是人，所以可以把`people`当作父类来继承：
```python
class Teacher(People):
	pass

class Student(People):
	def eat(self):
		print('student is eating')

	def play(self):
		print('student is play')
```
继承的话，子类可以得到父类所以的属性和方法。也就是说，身为子类的`student`和`teacher`可以获得人`people`这个父类的所以属性和方法。
```python
>>> teacher = Teacher()
>>> student = Student()
>>> teacher.eat()
people is eating
>>> student.eat()
student is eating
>>> student.play()
student is play
```
从上面可以看出，子类继承了父类的方法，但是如果子类自己又定义了一个同名的方法，则会覆盖父类的方法。而且，子类也可以自己定义新的方法，比如说`play`，这就是多态。
要理解多态，我们首先要知道，我们定义的这些类，和`python`自带的`list`，`str`等等都是一样的，都可以看作一种数据类型。我们可以使用`isinstance()`来判定数据类型。
```python
>>> a = list()
>>> b = People()
>>> c = Student()
>>> isinstance(a,list)
True
>>> isinstance(b,People)
True
>>> isinstance(c,Student)
True
>>> isinstance(c,People)
True
>>> isinstance(b,Student)
False
```
我们可以看到`c`不但是`Student`类型的，还是`People`类型的。但是`b`只是`People`类型，却不是`Student`类型。这是因为`Student`类继承了`People`这个父类。
总结一下，如果一个实例的数据类型是某个子类，那么他的数据类型也是父类。
接下来用一个例子来理解多态的灵活性。
```python
def eat_twice(people):
	people.eat()
	people.eat()
```
这个函数，可以传入`people`数据类型的参数。我们直接传入上面那些类的实例，其实也就是`people`数据类型的变量。
```python
eat_twice(People())
eat_twice(Student())
eat_twice(Teacher())
```
这些都是上面定义了的，如果我们要新增加一个继承了`People`类的`Doctor`，我们也可以这样写：
```python
class Doctor(People):
	def eat(self):
		print('doctor is eating')
>>> eat_twice(Doctor())
doctor is eating
doctor is eating
```
可以看到的是,`People`每新增一个子类，都可以传入以`People`数据类型为参数的函数中，这就是多态优越的地方。我们只需要知道传入的是`People`类型，而不需要知道他的子类型。只要传入了，我们就会安装`People`类型来处理，调用他的`run`的方法。而且，由于`python`是一种动态语言，数据类型是鸭子类型，就是说"一个东西看起来像是鸭子，那他就是鸭子。"翻译过来就是，如果一个对象，有`eat`这个方法，我们就认为他是`People`数据类型的，可以被`eat_twice`调用。
#### 获取对象信息
出了使用`instance()`之外，我们也可以使用`type()`函数来判定数据类型。
```python
>>> type(123)
<class 'int'>
>>> type('123')
<class 'str'>
>>> type(None)
<class 'NoneType'>
>>> type([1,2,3])
<class 'list'>
```
也可以判断函数或者类：
```python
>>> type(abs)
<class 'builtin_function_or_method'>
>>> type(People)
<class 'type'>
>>> type(Student)
<class 'type'>
```
也可以判定一个变量是否是某种类型：
```python
>>> type('123')==str
True
```
判断基本数据类型可以直接写`int`，`str`等，但如果要判断一个对象是否是函数可以使用types模块中定义的常量：
```python
>>> import types
>>> def fun():
...     pass
...
>>> type(fun)==types.FunctionType
True
>>> type(abs)==types.BuiltinFunctionType
True
>>> type(lambda x: x)==types.LambdaType
True
>>> type((x for x in range(10)))==types.GeneratorType
True
```
如果我们想要获得一个对象所有的属性和方法,可以使用`dir()`。
```python 
dir('123')
```
会返回一系列`__XXX__`的方法，比如说`__len__`返回长度,下面两种写法其实是一样的：
```python
>>> len('123')
3
>>> '123'.__len__()
3
```
我们也可以在自己的类里面添加一个`__len__`方法。
```python
class People():
	def __len__(self):
		return 20
>>> people=People()
>>> len(people)
20
```
我们还可以用`hasattr()`，`getattr()`和`setattr()`这三个函数来操作对象的属性和方法：
```python
class People():
	def __init__(self,name):
		self.name=name
	def __len__(self):
		return 20
>>> people=People('zhangdaxiang')
>>> hasattr(people,'name')
True
>>> getattr(people,'name')
'zhangdaxiang'
>>> setattr(people,'name','xiaohui')
>>> getattr(people,'name')
'xiaohui'
>>> hasattr(people,'__len__')
True
>>> getattr(people,'__len__')
<bound method People.__len__ of <__main__.People object at 0x00000000033EEF98>>
```
如果`getattr`一个不存在的方法或属性，则会报错，我们可以添加第三个参数使得不存在则返回第三个参数：
```python
>>> getattr(people,'age','no')
'no'
```
#### 类的属性和实例属性
我们的类，也可以拥有自己的属性，而且这个类的每个实例都可以访问：
```python
class People():
	name = 'zhangdaxiang'
people = People()
>>> people = People()
>>> people.name #实例可以访问类的属性
'zhangdaxiang'
>>> people.name = 'xiaohui' #给实例赋一个name属性的值
>>> people.name #属性名称相同，实例覆盖类
'xiaohui'
>>> print(People.name) #类的属性还在
zhangdaxiang
>>> del people.name #删除类的属性
>>> people.name
'zhangdaxiang'
```