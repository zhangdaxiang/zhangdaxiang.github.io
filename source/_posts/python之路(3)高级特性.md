---
title: python之路(3):高级特性
date: 2016-11-13 14:20:23
tags: python
categories: python
---
高级特性呢能使得我们代码越来越少,越来越精简。代码这个东西，当然是越少越好了。
### 列表生成式
有个需求，要把列表[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]的每个值加1，有两种写法：
```python
#第一种
for i in range(len(L));
	L[i]+=1
#第二种
for index,i in enumerate(L):
	L[index]+=1
```
我们使用列表生成式的话可以这样写：
```python
[i+1 for i in L]
```
感觉既简单又清晰。
for循环后面还可以加上if判断，这样我们就可以筛选出仅偶数的平方：
```
>>> [x * x for x in range(1, 13) if x % 2 == 0]
[4, 16, 36, 64, 100,144]
```
还可以使用两层循环，可以生成全排列：
```python
>>> [m + n for m in 'ABC' for n in 'XYZ']
['AX', 'AY', 'AZ', 'BX', 'BY', 'BZ', 'CX', 'CY', 'CZ']
```
也可以同时作用在两个变量来生成列表表达式：
```python
>>> people={'name':'张大象','age':'25','city':'hangzhou'}
>>> [k+'='+v for k,v in people.items()]
['city=hangzhou', 'name=张大象', 'age=25']
```
### 生成器
通过列表生成式,我们可以很方便地生成一个列表。但是如果我们要生成一个数量很大的列表，这样会占用很多的空间和资源。如果我们只访问其中的一部分元素，那其他的元素所占的空间都浪费了。python有一个叫生成器(generator)的东西，可是使得我们在循环的时候，边循环边生成，这样就不会浪费很多空间了。
创建一个生产器常用的有两种方法，第一种方法很简单。就是把列表生成式的`[]`改成`()`，这样就是一个generator了：
```python 
>>> L = [i*i for i in range(8)]
>>> L
[0, 1, 4, 9, 16, 25, 36, 49]
>>> g = (i*i for i in range(8))
>>> g
<generator object <genexpr> at 0x000000000348FBF8>
```
可以看到,`L`是一个列表，而`g`是一个生成器。
要调用的话,我们可以使用`next()`函数：
```python
>>> next(g)
0
>>> next(g)
1
>>> next(g)
4
>>> next(g)
9
>>> next(g)
16
>>> next(g)
25
>>> next(g)
36
>>> next(g)
49
>>> next(g)
Traceback (most recent call last):
  File "<pyshell#13>", line 1, in <module>
    next(g)
StopIteration
```
生成器保存的是算法,每次调用`g`，就是计算出下一个元素的值，直到最后一个元素，没有元素的时候，就会报出`StopIteration`的错误。
当然了,一直用`next()`调用生成器显得很麻烦，其实我们也可以用`for`循环来迭代生成器：
```python
>>> g=(i*i for i in range(8))
>>> for i in g:
	print(i)

	
0
1
4
9
16
25
36
49
```
而且全部迭代完了以后,也不会报错。
我们也可以使用函数的方法来生成一个生成器,比如说斐波拉契数列很难用列表生成式写出来,但是写一个函数很简单,同样的,我们也可以用函数生成一个斐波拉契数列的生成器,只要函数里有`yield`就可以了。这就是生成生成器的另一种方法：
```python
def fib(max):
	a,b,n=1,1,0
	while n<max:
		yield a
		a,b=b,a+b
		n+=1
	return 'done'
>>> for i in fib(5):
	print(i)

	
1
1
2
3
5
```
这一类生成器的规则是，每次执行遇到`yield`则会返回，然后停止，直到下次运行的时候，就会从`yield`处接着执行。而且我们发现，用`for`循环来执行函数生成器，并不能获得`return`的值。这是因为，`return`值包含在`StopIteration`的`value`中。
可以这样来捕获`StopIteration`值：
```python
g=fib(5)
while True:
	try:
		x=next(g)
		print(x)
	except StopIteration as e:
		print(e.value)
		break
1
1
2
3
5
done
```
成了,这样就捕获到了`return`的值。
(2017年2月更新)
今天了解到了生成器还有`send()`和`throw()`两种方法，深感之前学的都是皮毛。总结一下这两个方法,看这个示例代码：
```python
def gen():
	for i in range(5):
		tmp = yield i
		if tmp == 'hello':
			print('zhangdaxiang')
		else:
			print(str(tmp))
```
```python
>>> g=gen()
>>> next(g)
0
```
我们先调用`next()`方法，使生成器运行到`yield`位置，此时暂停执行环境，并返回`yield`的值。所以返回出来的是1，并暂停执行环境。
```python
>>> next(g)
None
1
```
可以看到,这里返回了两个值`None`和1，是不是感觉很奇怪。其实也很简单，因为上一次运行到`yield 0`暂停运行环境，现在恢复运行，先给`tmp`赋值(但是这里`tmp`获得的值不是`i`的值，而是`send`方法传过来的值)。由于我们没有调用`send()`方法，所以`tmp`的值是`None`。运行顺序是，先`print`出`tmp`的值，然后运行到`yield 1`，返回1，然后暂停执行环境。
```python
>>> g.send('hello')
zhangdaxiang
2
```
上一次我们运行到`yield 1`后暂停，现在我们输入`send('hello')`，程序接收到`hello`并给`tmp`赋值`hello`。此时`tmp=='hello'`为真，所以打印出`zhangdaxiang`，然后执行到下一次`yield 2`，返回2。（next()等价于send(None)）
当`yield`全部运行结束以后将会报出`StopIteration`的错误，看下面这段代码：
```python
def stoperror(x):
	if x=='daxiang':
		yield x
	else:
		print('nono')
>>> g=stoperror('bangbang')
>>> next(g)
nono
Traceback (most recent call last):
  File "<pyshell#1>", line 1, in <module>
    next(g)
StopIteration
```
果然,先打印出`nono`，然后因为没有`yield`，就`StopIteration`报错了。
再通过下面这段代码，我们来理解`throw()`方法的使用：
```python
def throwerror(x):
	try:
		yield x
	except ValueError:
		print('value error')
	finally:
		print('clean')

>>> g=throwerror('daxiang')
>>> next(g)
'daxiang'
>>> g.throw(ValueError)
value error
clean
Traceback (most recent call last):
  File "<pyshell#2>", line 1, in <module>
    g.throw(ValueError)
StopIteration
```
先调用`next()`，返回`daxiang`，然后用`throw()`传入一个`ValueError`的错误，`except`捕捉然后打印出`value error`和`clean`。
更神奇的是,我们可以用生成器在单线程的情况下实现并发运算：
```python 
def fun1():
	for i in range(4):
		yield 'a'+str(i)
def fun2():
	for i in range(4):
		yield 'b'+str(i)
L=[]
L.append(fun1())
L.append(fun2())
def run(L):
	for i in L:
		try:
			print(next(i))
		except StopIteration:
			pass
		else:
			L.append(i)
run(L)
```
### 迭代器
#### 迭代器原理
能够直接作用于`for`循环的对象就可以称之为**可迭代对象**(Iterable)，我们可以用`isinstance()`来判定一个对象是否为可迭代对象。
```python
>>> from collections import Iterable
>>> isinstance((x for x in range(5)),Iterable)
True
>>> isinstance([1,2,3],Iterable)
True
>>> isinstance('zhangdaxiang',Iterable)
True
>>> isinstance(1000,Iterable)
False
```
以此同时,生成器不但可以使用`for`循环，还可以调用`next()`函数，来返回下一个值，最好抛出`StopIteration`错误来终止。对于这种可以调用`next()`的可迭代对象，称之为迭代器。
同样也可以使用`isinstance()`函数来判断一个对象是否为`Iterator`(迭代器)：
```python
>>> from collections import Iterator
>>> isinstance((x for x in range(5)),Iterator)
True
>>> isinstance([1,2,3],Iterator)
False
>>> isinstance('zhangdaxiang',Iterator)
False
```
可以看到，生成器不但是`Iterable`也是`Iterator`，而`str`,`list`和`dict`这些，只是`Iterable`而不是`Iterator`。
对于`str`,`list`和`dict`这些不是迭代器的对象，可以使用`iter()`函数来让他变成迭代器：
```python
>>> isinstance(iter([1,2,3]),Iterator)
True
>>> isinstance(iter('zhangdaxiang'),Iterator)
True
```
`str`,`list`和`dict`这些对象不是迭代器的原因是，在`python`中，迭代器`Iterator`是一个数据流，我们可以通过`next()`函数来计算出下一个值，直到`StopIteration`报错。我们可以把它看作一个有序的数据流，但是我们并不知道他的具体长度。所以，`Iterator`是惰性的，只有需要返回下一个值的时候它才会计算。
实际上，在`python`中，`for`循环就是通过不断地调用`next()`来实现的：
```python
for i in range(5):
	print(i)
```
等价于
```python
g=iter(range(5))
while True:
	try:
		print(next(g))
	except StopIteration:
		break
```
#### 利用Iterator和filter求质数
上一篇我们学习了`filter()`筛选函数，现在我们可以用`Iterator`来表示无限序列，所以我们可以来利用`Iterator`和`filter`求所有质数。
要计算质数我们首先要理解一个算法，叫作埃氏筛法：
1.先把1给剔除，因为数学界中1既不是质数也不是合数。
2.将当前队列中最小的数2和2的倍数全部剔除。
3.将当前队列中最小的数3和3的倍数全部剔除。
4.将当前队列中最小的数5和5的倍数全部剔除。
5.不断筛选下去，直到范围内所求的数全部剔除。
首先我们构建一个从3开始的奇数数列，我们可以用`Iterator`来表示一个无限数列：
```python 
def odd():
	n=1
	while True:
		n=n+2
		yield n
```
然后再定义一个筛选函数：
```python
def fun(n):
	return lambda x:x%n!=0
```
然后定义一个生成器，不断返回质数：
def p_number():
	yield 2
	num=odd()
	while True:
		n=next(num)
		yield n
		num=filter(fun(n),num)
```
可以看到,因为`p_number()`也是一个无限序列，加一个跳出条件就可以调用：
​```python
for i in p_number():
	if i < 1000:
		print(i)
	else:
		break
```
### 装饰器
软件开发的时候有一个原则，叫作"开放-封闭"原则，简单来说，就是已经实现功能的代码不允许被修改，但是可以被拖着，即：
封闭：已经实现的代码块不允许被修改。
开放：可以对已实现的功能进行拓展。
所以我们在给一个函数添加新功能的时候,一般就不会直接修改原有的函数模块,而是在函数外部添加新的代码块来添加新功能。`python`中就有这么一个语法糖来实现这个，叫做**装饰器**`decorator`。装饰器实际上就是一个把原函数作为参数传入的高阶函数，返回另一个在内部定义的函数。
#### 基本写法
我们举一个"开放-封闭"的例子：
比如说我们的视频网站有好多版块：
```python
def animal():
	print('aniaml'.join(['-'*10]*2))
def china():
	print('china'.join(['-'*10]*2))
def japan():
	print('japan'.join(['-'*10]*2))
```
现在有一个要求，我们的`animal`版块和`china`版块，需要会员登录才能访问，而`japan`版本不受影响。秉着"开放-封闭"的原则，我们不修改原有的函数，在外部直接添加会员登录的新功能。
```python
user_status=False#如果用户验证通过，则会改为True
def login(fun):
	def inner(*args,**kwargs):
		_user_name='zhangdaxiang'
		_password='123456'
		global user_status
		if user_status==False:
			username=input('输入用户名：')
			password=input('输入密码：')
			if username==_user_name and password==_password:
				print('登录成功！')
				user_status=True
			else:
				print('密码错误！')
	
		if user_status==True:
			return fun(*args,**kwargs)
	return inner#返回内部函数inner

animal=login(animal)
china=login(china)

>>> animal()
输入用户名：zhangdaxiang
输入密码：123456
登录成功！
----------aniaml----------
>>> china()
----------china----------
>>> japan()
----------japan----------
```
实际上按照装饰器的写法，也可以写成这样：
```python
@login
def animal():
	print('aniaml'.join(['-'*10]*2))

@login
def china():
	print('china'.join(['-'*10]*2))	

def japan():
	print('japan'.join(['-'*10]*2))
```
实际上
```python
@login
def animal():
	pass
```
等价于
```python
animal=login(animal)
```
#### 添加新功能
如果我们想再新加一个功能,比如登录之后输出一个欢迎语句,直接在修饰器里面改变就好了：
```python
user_status=False
def login(fun):
	def inner(*args,**kwargs):
		_user_name='zhangdaxiang'
		_password='123456'
		global user_status
		if user_status==False:
			username=input('输入用户名：')
			password=input('输入密码：')
			if username==_user_name and password==_password:
				print('登录成功！')
				user_status=True
			else:
				print('密码错误！')
	
		if user_status==True:
			print('welcom to %s'%fun.__name__)#添加一个欢迎语句，输出函数名称
			return fun(*args,**kwargs)
	return inner

@login
def animal():
	print('aniaml'.join(['-'*10]*2))

@login
def china():
	print('china'.join(['-'*10]*2))	

def japan():
	print('japan'.join(['-'*10]*2))

>>> animal()
输入用户名：zhangdaxiang
输入密码：123456
登录成功！
welcom to animal
----------aniaml----------
>>> china()
welcom to china
----------china----------
>>> japan()
----------japan----------
```
#### 带参数的装饰器
如果`decorator`本身也需要传入一个参数，就需要编写一个返回`decorator`的高阶函数，比如：
```python
user_status=False
def login(text):
	def inner1(fun):
		def inner2(*args,**kwargs):
			_user_name='zhangdaxiang'
			_password='123456'
			global user_status
			if user_status==False:
				username=input('输入用户名：')
				password=input('输入密码：')
				if username==_user_name and password==_password:
					print('登录成功！')
					user_status=True
				else:
					print('密码错误！')
	
			if user_status==True:
				print('welcom to %s,%s'%(fun.__name__,text))
				return fun(*args,**kwargs)
		return inner2
	return inner1

animal=login('thx')(animal)#login('thx')返回inner1，所以inner1('thx')返回inner2。
china=login('thx')(china)

>>> animal()
输入用户名：zhangdaxiang
输入密码：123456
登录成功！
welcom to animal,thx
----------aniaml----------
>>> china()
welcom to china,thx
----------china----------
>>> japan()
----------japan----------
```
和预期一样,我们也可以用这种写法：
```
@login('thx')
def animal():
	print('aniaml'.join(['-'*10]*2))

@login('thx')
def china():
	print('china'.join(['-'*10]*2))	

def japan():
	print('japan'.join(['-'*10]*2))
```
实际上，
```python
@log('thx')
def animal():
	pass
```
等价于
```python
anmial=login('thx')(animal)
```
首先执行`login('thx')`，返回的是`inner1`函数，再调用返回的函数，参数是`animal`函数，返回值最终是`inner2`函数。
#### 返回函数的__name__
但是需要注意的是，`aninmal`函数经过装饰器装饰以后，他的`__name__`已经从原来的`animal`变成了`inner2`:
```python
>>> animal.__name__
'inner2'
```
因为最后返回的那个`inner2()`函数名字就是`inner2`，所以需要把原始函数的`__name__`等属性复制到`inner2()`中，否则一些依赖函数签名的代码执行就会出错。
不过并不需要编写`inner2.__name__=func.__name__`这样的代码，`python`内置的`functools.wraps`就是专门处理这个的，所以一个完整的`decorator`的写法是这样的：
```python
import functools
def login(fun):
	@functools.wraps(func)
	def inner(*args,**kwargs):
		return func(*args,**kwargs)
	return inner
```
或者对于有参数的`decorator`：
```python
import functools
def login(text):
	def inner1(fun):
		@functools.wraps(fun)
		def inner2(*args,**kwargs):
			print（'%s'%fun.__name__）
			return fun(*args,**kwargs)
		return inner2
	return inner1
```
只需要最返回函数前加上`@functools.wraps(fun)`即可。