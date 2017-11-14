---
title: python之路(2):函数
date: 2016-11-08 13:53:24
tags: python
categories: python
---

### 函数基础
#### 函数的定义
函数是指一个封装好的语句集合,我们想要调用这些语句,只需要调用这些函数名即可。使用函数的话，我们可以获得以下的几个特性：
1.减少代码的重复性。
2.增加程序的可维护性。
3.使得程序变得可拓展。
定义函数：
```python
def fun(): #函数名称
	pass #函数内容
fun() #调用函数
```
注意,上面的函数内容是`pass`，这代表该函数是一个空函数，即直接略过函数内容，调用他没有任何意义。如果想要定义一个有实际内容的函数，将其中的`pass`改为实际代码即可。
#### 函数的返回
在函数中使用`return`可以返回函数的运行结果：
```python
def num(a,b):
	sum=a+b
	return sum #返回函数运行结果
>>> num(1,2)
3
```
如果没有`return`，函数运行完毕后也会返回一个`None`，也就是`return None`，可以简写成`return`。
函数也可以返回多个值：
```python
def sum(a,b,c):
	sum1=a+b
	sum2=a+c
	return sum1,sum2
>>> sum(1,2,3)
(3, 4)
```
可以看到,函数返回多个值,实际上是返回一个`tuple`。
#### 函数的参数检查
python中有很多可以直接调用的函数,称之为内置函数。如果是内置函数输入类型不正确的参数，就会报错。而我们定义的函数，因为没有参数检查，所以虽然也会报错，但是python不会替我们检查，所以和内置函数的报错不一样。为了使得报错更直观，我们可以引入参数检查。
我们定义一个求绝对值的函数：
```python
def my_abs(x):
	if x>=0:
		return x
	else:
		return -x
```
比较`my_abs`和内置函数`abs`的报错信息有什么区别：
```python
>>> my_abs('A')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "<stdin>", line 2, in my_abs
TypeError: unorderable types: str() >= int()
>>> abs('A')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: bad operand type for abs(): 'str'
```
可以看到，内置函数有参数检查。
为`my_abs`添加一个参数检查,检查传入参数是否为浮点数或整数：
```python
def my_abs(x):
	if not isinstance(x,(int,float)):
		raise TypeError('error type!')
	if x>=0:
		return x
	else:
		return -x
```
这时候，如果输入参数错误的话，python会进行参数检查：
```python
>>> my_abs('A')
Traceback (most recent call last):
  File "<pyshell#0>", line 1, in <module>
    my_abs('A')
  File "C:\Users\Administrator\Desktop\123.py", line 3, in my_abs
    raise TypeError('error type!')
TypeError: error type!
```
### 函数的参数
定义函数的时候,我们把函数的名称和参数确定下来,函数接口的定义就已经完成了。使用者只需要了解函数的怎么正确传递参数，以及函数返回什么类型的值就可以了。内部封装好的逻辑，使用者不用去管。
python函数定义非常灵活，参数在除位置参数外,还有默认参数,可变参数,关键字参数。
#### 默认参数
我们在定义一个函数的时候,可以为一个或者更多的参数设置默认值,这样的参数在调用的时候如果没有输入,则会使会用默认值。使用默认参数的话，可以大大降低函数调用的难度，而一旦需要更复杂的调用时，又可以传递更多的参数来实现。无论是简单调用还是复杂调用，函数只需要定义一个：
```python
def sum(a,b,c=3):
	sum1=a+b
	sum2=a+c
	return sum1,sum2
>>> sum(1,2,2)
(3, 3)
>>> sum(1,2,3)
(3, 4)
>>> sum(1,2) #这里就使用了默认参数，c默认为3，和上面是一样的
(3, 4)
```
使用默认参数要注意两点：
1.位置参数在前，默认参数在后。
2.变化大的默认参数在前，变化小的默认参数在后。
还有一点值得注意的是，默认参数有一个巨大的坑，是关于可变对象的。我们要记住，默认参数尽量使用不可变对象。
```python
def my_add(L=[]):
	L.append('*')
	return L
```
我们来尝试调用这个函数：
```python 
>>> my_add() #一开始调用结果是对的
['*']
>>> my_add() #再次调用结果就不对了
['*', '*']
>>> my_add()
['*', '*', '*']
```
这是因为,在定义函数的时候,默认参数的值已经被计算出来了,就是[]，而默认参数L也是一个变量，指向的是[],每次调用函数，L只想的对象也变了，所以默认参数的内容也就随之改变了。
#### 可变参数
当我们不能确定使用者要传入多少参数时,可以定义一个可变参数,用`*args`,仅仅需要在参数前面加一个`*`就可以了。实际上，可变参数是作为一个`tuple`传入的。
可以这样定义:
```python
def my_sum(*numbers):
	sum=0
	for i in numbers:
		sum+=i
	return sum
>>> my_sum(1,2,3,4,5,6)
21
```
如果传入的参数是一个列表,我们也可以灵活调用我们的可变参数函数:
```python
>>> L=[1,2,3,4,5,6]
>>> my_sum(*L)
21
```
#### 关键字参数
可变参数是允许传入0或多个参数,组成一个`tuple`。而关键字参数则是允许你传入0或多个带有参数名的参数，组成一个`dict`,可以使用关键字参数`*kw`来表示。
定义如下：
```python
def people(name, age, **kw):
	print('name:', name, 'age:', age, 'other:', kw)
```
可以看到，`people`函数除了`name`和`age`两个位置参数以外，还有一个关键词参数`kw`。我们在调用的时候，可以只传入位置参数：
```python
>>> people('张大象',25)
name: 张大象 age: 25 other: {}
```
也可以传入任意个关键词参数：
```python
>>> people('张大象',25,city='杭州')
name: 张大象 age: 25 other: {'city': '杭州'}
>>> people('张大象',25,city='杭州',job='student')
name: 张大象 age: 25 other: {'city': '杭州', 'job': 'student'}
```
关键词参数的作用就体现在这里，我们保证能够接收到`name`和`age`这两个参数，但是如果调用者传入更多的参数，我们也可以接收到。
同样的，类似于可变参数，我们也可以灵活地调用关键词参数：
```python
>>> others={'city':'杭州','job':'student'}
>>> people('张大象',25,**others)
name: 张大象 age: 25 other: {'city': '杭州', 'job': 'student'}
```
#### 命名关键字参数
命名关键字参数的作用是限制关键字参数的开放性,限制可以传入的关键字参数。比如我们限制只能传入`city`和`job`这两个关键词参数：
```python
def people(name,age,*,city,job):
	print(name,age,city,job)
```
和`**kw`关键词参数不同，使用了一个特殊分隔符`*`，后面的参数就视为命名关键字参数。
调用方法如下:
```python
>>> people('张大象',25,city='杭州',job='student')
张大象 25 杭州 student
>>> people('张大象',25,city='杭州',sex='man') #传入其他参数则会报错
Traceback (most recent call last):
  File "<pyshell#2>", line 1, in <module>
    people('张大象',25,city='杭州',sex='man')
TypeError: people() got an unexpected keyword argument 'sex'
```
如果前面的参数已经有`*args`可变参数，就不想要特殊分隔符`*`，可变参数后面的就是命名关键字参数。
```python
def peope(name,age,*args,city,job):
	print(name,age,args,city,job)
```
而且命名关键字参数必须传入参数名，这和位置参数不同。如果没有传入参数名，将会报错：
```python
>>> people('张大象',25,'杭州','student')
Traceback (most recent call last):
  File "<pyshell#0>", line 1, in <module>
    people('张大象',25,'杭州','student')
TypeError: people() takes 2 positional arguments but 4 were given
```
调用的时候没有因为没有参数名，python把四个参数都看作位置参数，所以报错。
 ### 递归函数
 如果一个函数在函数内部调用了自己，那这个函数就是一个递归函数。具有以下特效：
 1.必须要有一个比较明确的结束条件。
 2.没进行一次递归，数据规模总比上一次要减少。
 3.递归效率不高，递归次数太多会造成栈溢出。
 #### 阶乘
计算阶乘`n! = 1 x 2 x 3 x ... x n`，用函数fact(n)表示:
```python
def fact(n):
	if n==1:
		return n
	else:
		return n*fact(n-1)
```
计算机中，函数的调用是通过栈这种数据结构实现的。每调用一次函数，就占用一个栈帧，函数返回一次就减少一个栈帧。因为栈帧不是无限的，所以要注意使用递归的时候，防止占用栈帧过多，导致栈溢出。
#### 二分查找
```python
data = [1, 3, 6, 7, 9, 12, 14, 16, 17, 18, 20, 21, 22, 23, 30, 32, 33, 35]
def mid_search(data,num):
	print(data)
	if len(data)>1:
		mid_num=int(len(data)/2)
		if num == data[mid_num]:
			print('找到数字',data[mid_num])
		elif num > data[mid_num]:
			print('数字在%s右边'%data[mid_num])
			return mid_search(data[mid_num+1:],num)
		elif num < data[mid_num]:
			print('数字在%s左边'%data[mid_num])
			return mid_search(data[0:mid_num],num)
	elif len(data)==1:
		if num==data[0]:
			print('找到数字',data[0])
		else:
			print('没有这个数字')
```
### 匿名函数
匿名函数就是不需要显式的指定函数:
```python
#这段代码
def my_sum(a,b):
    return a+b
print(my_sum(1,2))
 
#换成匿名函数
my_sum=lambda a,b:a+b
print(my_sum(1,2))
```
### 高阶函数
我们知道,变量可以指向一个函数,这样的话,一个函数就可以接收另外一个函数作为参数:
```python
def fun(x,y,f):
	return f(x)+f(y)
```
#### map/reduce
`python`有两个非常有用的函数`map()`和`reduce()`。
先说`map()`，`map()`接受两个参数，一个是函数，另一个是`Iterable`。`map()`依次将函数作用于`Iterable`的每一个元素，然后返回一个`Iterator`。
举个栗子,现在有一个函数`f(x)=x+1`，我们可以使用`map()`函数来使得整个函数作用在列表`[1,2,3,4,5,6,7]`的每一个元素上。
```python 
def fun(x):
	x = x+1
	return x
L = map(fun,[1,2,3,4,5,6,7])
>>> print(list(L))
[2, 3, 4, 5, 6, 7, 8]
```
因为返回的L是一个惰性序列`Iterator`，我们使用`list()`将每一个元素计算出来并返回一个`list`。
接下来看`reduce()`的用法，`reduce`是将一个函数作用在一个序列[x1,x2,x3,x4.....]上，这个函数需要接收两个参数，然后将返回的结果和下一个参数继续传给这个函数。就是：
```
reduce(f,[x1,x2,x3,x4])=f(f(f(x1,x2),x3),x4)
```
这个例子其实是没什么用的,但是我们可以根据这个例子来再配合`map()`来写出`str`和`int`相互转换的函数：
```python
from functools import reduce
def str2int(s):
	def fun(x,y):
		return 10*x+y
	def str2num(x):
		return {'0': 0, '1': 1, '2': 2, '3': 3, '4': 4, '5': 5, '6': 6, '7': 7, '8': 8, '9': 9}[x]
	return reduce(fun,map(str2num,s))
>>> str2int('19941105')
19941105
```
这个其实就和`python`内置的`int()`效果是一样的。