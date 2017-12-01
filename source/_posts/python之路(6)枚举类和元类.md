---
title: python之路(6):枚举类和元类
date: 2016-11-24 10:03:13
tags: python
categories: python
---
### 枚举类
#### 定义
如果我们定义的类的属性是有限且固定的，我们可以使用枚举类。
我们首先要从`enum`模块中导入`Enum`，然后在定义类的时候继承`Enum`：
```python
from enum import Enum
class Weekday(Enum):
	Sunday = 1
	Monday = 2
	Tuesday = 3
	Wednesday = 4
	Thursday = 5
	Friday = 6
	Saturday = 7
```
这样我们就成功定义了星期的枚举类`Weekday`，他有固定的7个成员，比如`Weekday.Sunday`,`Weekday.Monday`等。而且每个成员都有他固定名称和值，比如说`Weekday.Sunday`的名称是`Sunday`，值是1。而且之前我们就知道，我们定义的类也是一种数据类型，和`list`,`str`这些数据类型没有任何区别。所以，每个成员的数据类型，都是他们所属于的枚举类。
有几点需要注意一下：
1.成员的名称不允许重复：
```python
from enum import Enum
class Weekday(Enum):
	Sunday = 1
	Sunday = 2
```
这样子会有报错`TypeError: Attempted to reuse key: 'Sunday'`。
2.成员的值一般情况下是可以相同的。两个值相同的成员，第二个成员会视为第一个成员的别名：
```python
class Weekday(Enum):
	Sunday = 1
	Sunday_two = 1
```
`Weekday.Sunday`和`Sunday_two`具有相同的值，所以`Sunday_two`是作为`Sunday`的别名存在的。而且如果我们想获取值相同的成员，只能取到第一个。
3.如果我们想限制枚举类，希望不能有重复值，可以使用`@unique`这个装饰器：
```python
from enum import Enum,unique

@unique
class Weekday(Enum):
	Sunday = 1
	Sunday_two = 1
```
这样就会报重复值的错误`ValueError: duplicate values found in <enum 'Weekday'>: Sunday_two -> Sunday`。
#### 枚举取值
可以直接通过成员名称来获取成员：
```python
>>> Weekday['Sunday']
<Weekday.Sunday: 1>
```
通过成员的值来获取成员：
```python
>>> Weekday(1)
<Weekday.Sunday: 1>
```
也可以通过成员来取他的名称和值：
```python
>>> Week = Weekday.Sunday
>>> Week.name
'Sunday'
>>> Week.value
1
```
还可以使用`for`循环来遍历整个`Weekday`枚举类。但是如果有重复值，只会遍历第一个成员：
```python
from enum import Enum
class Weekday(Enum):
	Sunday = 1
	Monday = 2
	Tuesday = 3
	Wednesday = 4
	Thursday = 5
	Friday = 6
	Saturday = 7
	Sunday_two = 1

for i in Weekday:
	print(i)

#输出
Weekday.Sunday
Weekday.Monday
Weekday.Tuesday
Weekday.Wednesday
Weekday.Thursday
Weekday.Friday
Weekday.Saturday
```
如果我们希望重复的值也被遍历出来，可以加一个魔法方法`__members__`：
```python
for i in Weekday.__members__.items():
	print(i)

#输出
('Sunday', <Weekday.Sunday: 1>)
('Monday', <Weekday.Monday: 2>)
('Tuesday', <Weekday.Tuesday: 3>)
('Wednesday', <Weekday.Wednesday: 4>)
('Thursday', <Weekday.Thursday: 5>)
('Friday', <Weekday.Friday: 6>)
('Saturday', <Weekday.Saturday: 7>)
('Sunday_two', <Weekday.Sunday: 1>)
```
#### 枚举成员比较
可以用`is,is not`和`==,!=`进行比较，但是不能比较大小：
```python
>>> Weekday.Sunday is Weekday.Sunday
True
>>> Weekday.Sunday is not Weekday.Monday
True
>>> Weekday.Sunday == Weekday.Sunday
True
>>> Weekday.Sunday != Weekday.Sunday
False
```
### 元类
#### 吐槽
元类真的很复杂，一开始看廖大关于元类的教程，完全看得云里雾里。感觉解释性的语言太少了，有点跳跃，如果对于类的概念理解不充分，就很难看懂。看第一遍的时候，只记住了这么一句话，元类就是生产类的工厂，我们可以把类看作是元类的“实例”。没办法，只能各种找关于元类的文档，看了挺多的，还是不得其法。这个时候看到了`Python`界领袖 `Tim Peters`的一句话：
>“元类就是深度的魔法，99%的用户应该根本不必为此操心。如果你想搞清楚究竟是否需要用到元类，那么你就不需要它。那些实际用到元类的人都非常清楚地知道他们需要做什么，而且根本不需要解释为什么要用元类。”

顿时计上心来，嘿嘿，正好看的头大，不如我就先把元类放着吧，等以后能用到的时候，再来好好研究一下，岂不美哉。
然而鲁迅先生说过：
>“逃避并不能解决问题，只有在一个问题上苦下功夫，反复操练，才能学会！”

（鲁迅：“劳资没说过。。。”）
好吧，实际上是，在看到`ORM`时，发现用元类是真的很方便，那就回过头来好好把元类研究透彻。
#### 元类和类的关系
看到有大佬把元类和类的关系概括地挺好，我也非常喜欢。那就是：
>“道生一，一生二，二生三，三生万物。”

1.道的话就是`type`。
2.一就是元类`metaclass`(也叫作类生成器)。
3.二就是类`class`(也叫作实例生成器)。
4.三就是实例`instance`。
5.万物就是实例的属性和方法，我们平时调用的也就是这个。

首先来看看我们平时用`class`来定义类，然后生成实例的写法。我们举一个`Hello`的栗子：
```python
#首先定义一个class类
class Hello():
	def say_hello(self,name='world'):
		print("Hello,%s"%name)
#生成Hello()实例
hello = Hello()
#调用hello实例的方法
hello.say_hello()
#输出
>>> Hello,world
```
这是我们平时最常见的写法，但是实际上，我们是用`class`定义了一个类，然后`python`调用了`tpye()`来生成他。这些都是在`python`解释器里面完成的，当然我们也可以直接使用`type()`来动态生成一个类。所以我们可以用这种写法：
```python
#先定义类的方法say_hello
def say_hello(self,name='world'):
	print("Hello,%s"%name)
#使用type动态生成类
Hello = type('Hello',(object,),{'say_hello':say_hello})
```
这里`type()`依次传入三个函数：
1.类的名称。
2.类所继承的父类，这里是一个`tuple`，如果只继承一个父类，记得用`tuple`的单元素写法。
3.类的属性和方法，是一个字典`dict`。

#### 定义元类metaclass
我们可以用`type()`直接生成`class`类，也可以先生成`metaclass`元类，然后批量生成`class`类。一般，我们讲元类命名成后缀为`MetaClass`，还是用一个`Hello`的栗子来说明：
```python
#先创建一个“打招呼”的元类
class HelloMetaClass(type):
	def __new__(cls,name,bases,attrs):
		attrs['say_'+name]=lambda self,value,language=name:print(language+','+value)
		return type.__new__(cls,name,bases,attrs)
```
这里要说一下的是`__new__`这个魔法方法。`__new__`经常和`__init__`弄混淆，我们通常以为`__init__`是类实例化时调用的第一个方法，实际上`__new__`还在`__init__`之前调用。`__new__`魔法方法是用来生成类的实例的，而`__init__`是用来初始化这个生成的实例（要先生成实例才能初始化，这样是`__new__`方法先执行的原因）。然后我们在使用元类生成类的时候，可以使用`metaclass`参数引入元类`HelloMetaClass`。因为类实际上是由元类生成的，所以元类一定是一个可以创建类的东西。之前也提到了，我们可以把类看作是元类的一个实例。所以，元类在生成类的时候，实际上就是“实例化”，会调用`HelloMetaClass`这个元类的`__new__`方法，返回了一个`type`后的产物，即类。`__new__`方法里面的`cls`参数就类似于`self`参数，是要被“实例化”的"实例"。
#### 元类生成类
```python
#用元类创建类
class Hello(object,metaclass=HelloMetaClass):
	pass
#生成实例
hello=Hello()
#使用实例方法
hello.say_Hello('world!')
#输出
>>> Hello,world!
```
如果我们想使用元类生成另外一个名称不同的类，也是可以的：
```python
#用元类创建类
class Sayolala(object,metaclass=HelloMetaClass):
	pass
#生成实例
sayolala=Sayolala()
#使用实例方法
sayolala.say_Sayolala('japan!')
#输出
>>> Sayolala,japan!
```
可以看到，我们只是修改了要生成的类，没有修改元类里面的`__new__`方法，就可以实现自己想要的功能。这样也体现了元类生成类的"定制性"。
#### 元类编写ORM
`ORM`即是对象—关系映射，其实就是我们把一个类看作一个表，每个实例看作一个行。
首先我们创建一个`Field`类，这个是用来保存字段名和字段类型的：
```python
class Field():
	def __init__(self,name,column_type):
		self.name = name
		self.column_type = column_type
	#直接打印类的实例会调用这个魔法方法
	def __str__(self):
		return '<%s,%s>'%(self.__class__.__name__,self.name)
```
进一步我们可以在继承`Field`的基础上，创建`StrField`类来存储字符串，创建`IntField`来存储整型等。
```python
class StrField(Field):
	def __init__(self,name):
		super(StrField,self).__init__(name,'varchar(100)')
		
class IntField(Field):
	def __init__(self,name):
		super(IntField,self).__init__(name,'int')
```
这里我们使用`super()`来使得实例`self`可以直接调用父类`Field`的初始化方法`__init__`。这样咋一看，好像`super()`就是继承父类的方法，实际上`super()`和父类是没有关系的。这个在多继承的时候更为复杂，之后我会用另外一篇博客记录。
首先创建一个元类`ModelMetaClass`：
```python
class ModelMetaClass(type):
	def __new__(cls,name,bases,attrs):
		#排除对Model类的修改
		if name == 'Model':
			return type.__new__(cls,name,bases,attrs)
		#定义一个字典存放Field类型的属性
		mappings = {}
		for k,v in attrs.items():
			if isinstance(v,Field):
				print('find mapping: %s ===> %s'%(k,v))
				mappings[k] = v
		#删除已赋值到mappings的属性
		for k in mappings.keys():
			#不删除的话，在后面调用Model基类的`__getattr__`魔法方法会有问题
			attrs.pop(k)
		#保存列和属性的映射关系
		attrs['_mappings'] = mappings
		#保存表名
		attrs['_table'] = name
		return type.__new__(cls,name,bases,attrs)
```
这个元类实现了以下几个功能:
1.排除对`Model`类的修改。
2.对类的每一个属性进行遍历，如果是`Field`类型的，则将键值绑定到`mappings`中存储起来。
3.删除传入`mappings`中的属性。
4.创建一个属性`_mappings`来存储列和类型的映射关系。
5.创建一个属性`_table`来存储表名。
定义一个基类`Model`：
```python
class Model(dict,metaclass=ModelMetaClass):
	#调用实例不存在的属性时,会调用这个魔法方法	
	def __getattr__(self,key):
		try:
			return self[key]
		except KeyError:
			raise AttributeError("'Model' object has no attribute '%s'" % key)
	#__setattr__的魔法方法会拦截所有属性的赋值语句，然后调用这个方法。而且，这个应该是貌似只有对实例的属性赋值才会拦截，对类属性赋值不会拦截，还没有测试过。
	def __setattr__(self,key,value):
		self[key]=value
	#定制save方法，输出sql语句
	def save(self):
		fields = []
		args = []
		for k,v in self._mappings.items():
			fields.append(v.name)
			args.append(getattr(self,k,'Null'))
		sql = 'insert into %s(%s) values(%s)'%(self._table,','.join(fields),','.join([str(i) for i in args]))
		print('SQL:%s'%sql)
		print('ARGS:%s'%str(args))
```
创建一个继承了`Mode`的`User`类，这个在`ORM`中也被看作是一个表，类的每个属性被看作是一个列。
```python
class User(Model):
#定义类的属性到列的映射
#这下面的类的属性赋值就是我认为的不会触发__setattr__的
	id = IntField('id')
	name = StrField('name')
	email = StrField('email')
	password = StrField('password')
u = User(id=12345, name='Batman', email='batman@nasa.org', password='iamback')
u.save()
```
通过不到70行的代码实现了一个简单的`ORM`，很好用。
#### 结语
真让人头大啊，让人头大啊，人头大啊，头大啊，大啊，啊。。。。
