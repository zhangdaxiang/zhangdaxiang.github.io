---
title: python之路(8):IO编程
date: 2017-12-04 14:56:42
tags: python
categories: python
---
平常，我们用电脑浏览器访问页面，我们对网页服务器说，我们要你的`HTML`，这个是向网页服务器发数据，叫作`Output`。然后网页服务器把数据传过来，我们接收，这个叫作`Input`。再比如说我们从内存写入到磁盘文件，是`Output`，从磁盘文件读到内存是`Input`。
这个地方我之前经常弄混，后来记住了，这个"进出"是相对于内存来说的，就明了了。
### 文件读写
文件读写是最基本的`IO`操作。现代操作系统，一般不允许程序直接操作磁盘。所以，读写文件都是请求操作系统打开一个文件对象（通常称为文件描述符），然后通过系统提供的接口把数据写入这个文件对象，或者从这个文件对象读取数据。
#### 读文件
我们可以通过`open()`函数传入文件名和标识符来打开一个文件：
```python
f=open('/users/text.txt','r')
```
这里的`r`表示这个文件是读。如果要打开的文件不存在，`open()`函数就会报出`IOError`的错误。如果打开成功，我们可以使用`read()`方法来读取文件的内容：
```python
f.read()
```
操作完毕以后，需要用`close()`方法来关闭这个文件。如果不关闭的话，这个文件对象就会一直占用系统资源。而且如果`open()`报错的话，会影响程序接下去运行，我们一般用`try...finally...`的方法来解决这个问题：
```python
try:
	f = open('/user/text.txt','r')
	print(f.read())
finally:
	if f:
		f.close()
```
这样太麻烦了，所以有一种用`with`的便捷的写法，这样写`python`会自己关闭文件：
```python
with open('/user/text.txt','r'):
	print(f.read())
```
这样就可以实现和上面一样的功能。
要注意的是，`read()`方法会一次读取文件的所有内容。可以使用`read(size)`来一次读取`size`大小的内容，反复调用，从而获得全部。也可以使用`readlines()`来获取文件的一行内容。
这些都是在`utf-8`编码下文本的情况，如果要读取视频，图片等二进制的文件，就要使用`rb`标识符：
```python
with open('/user/picture1.jpg','rb') as f:
	print(f.read())
#输出
b'\xff\xd8\xff\xe1\x00\x18Exif\x00\x00...' # 十六进制表示的字节
```
如果文件对象不是`utf-8`，可以使用`encoding`参数来读取，比如说`gbk`格式的：
```python
with open('/user/text.txt','r',encoding='gbk') as f:
	print(f.read())
```
有时候如果文件编码不规范的话，很容易产生错误，最简单方法是忽略，`open()`有一个`errors`来实现这个：
```python
with open('/user/text.txt','r',encoding='gbk',errors='ignore') as f:
	print(f.read())
```
#### 写文件
写文件和读文件的唯一区别就是把标识符改成`w`和`wb`。而且如果没有`close()`的话，会导致一部分写入的数据没有保存，所以一定要`close()`。同样的，`with`写法可以帮我们自动`close()`。
### 目录操作
`python`的目录操作基本都在内置的`os`模块中，使用前记得导入。
#### 环境变量
`python`中的环境变量，全部保存在`os.environ`中：
```python
#导入os模块
import os
#查看环境变量
os.environ
```
如果想查看某个环境变量的值，比如`PATH`，可以使用`os.environ.get('key')`：
```python
os.environ.get('PATH')
#输出
'C:\\Ruby23-x64\\bin;C:\\Program Files (x86)\\Intel\\iCLS Client\\;'
#如果环境变量不存在
os.environ.get('x','default')
#输出
'default'
```
#### 操作文件和目录
`python`操作文件的函数都在`os`模块和`os.path`模块中：
```python
#查看当前目录的绝对路径
>>> os.path.abspath('.')
'C:\\Users\\Administrator\\Desktop'
#在某个目录下创建一个新目录，把新目录的完整路径表示出来
>>> os.path.join('\\test1\\test2','test3')
'\\test1\\test2\\test3'
#然后创建一个目录
os.mkdir('C:\\Users\\Administrator\\Desktop\\test1\\test2\\test3')
#删除目录
os.rmdir('C:\\Users\\Administrator\\Desktop\\test1\\test2\\test3')
```
想要拼接两个路径，使用`os.path.join()`函数。
在Linux/Unix/Mac下返回：
```python
part-1/part-2
```
在Windows下会返回：
```python
part-1\\part-2
```
还可以使使用`os.path.split()`和`os.path.splitext()`这两个函数来分割路径：
```python
#这个函数分割至文件名
>>> print(os.path.split('C:\\Users\\Administrator\\Desktop\\test1\\test2\\test3.txt'))
('C:\\Users\\Administrator\\Desktop\\test1\\test2', 'test3.txt')
#这个函数分割至文件后缀名
>>> print(os.path.splitext('C:\\Users\\Administrator\\Desktop\\test1\\test2\\test3.txt'))
('C:\\Users\\Administrator\\Desktop\\test1\\test2\\test3', '.txt')
```
如果当前目录下有一个文件名`text3.txt`，文件操作的比如重命名，删除可以用一下两个函数：
```python
#重命名
os.rename('text3.txt','text4.txt')
#当然也可以这样写
os.rename('C:\\Users\\Administrator\\Desktop\\test1\\test2\\test3.txt','C:\\Users\\Administrator\\Desktop\\test1\\test2\\test4.txt')
#删除
os.remove('text3.txt')
```
可以使用以下两个函数来判定是否为目录和文件：
```python
#判定是否为目录
os.path.isdir(path)
#判定是否为文件
os.path.isfile(path)
```
可以使用一下的代码来实现筛选出当前目录下所有`.py`文件：
```python
[x for x in os.path.listdir('.') if os.path.isfile(x) and os.path.splitext(x)[1]=='py']
```
但是`os`模块里面没有移动文件的函数。。。感觉很不方便。好在`shutil`模块中有类似的函数来补充使用。
### 序列化
#### 序列化
一般情况下，我们在内存中执行程序，如果定义了一个变量：
```python
d = dict(name='zhangdaxiang',age=23,city='hangzhou')
```
这样子，变量是存储在内存中的，如果我们在程序中改变了这个`dict`，而没有保存到磁盘里。结算程序时，变量占用的内存被回收，下次运行程序，这个变量还是没有改变之前的值。想把对象保存到磁盘，就要序列化。
我们把变量变成可以存储或者传输的过程就叫作序列化。而把序列化对象重新读取到内存中，就称作反序列化。
`python`中提供了`pickle`模块来实现这个需求。
可以用`pickle.dumps()`序列化成一个`bytes`，也可以使用`pickle.dump()`直接把对象序列化然后写入`file-like Object`中：
```python
#导入pickle模块
import pickle
d = dict(name='zhangdaxiang',age=23,city='hangzhou')
#打开一个file-like Object文件
with open('dump.txt','wb') as f:
	#写入序列化对象
	pickle.dump(d,f)

#序列化bytes
>>> pickle.dumps(d)
b'\x80\x03}q\x00(X\x03\x00\x00\x00ageq\x01K\x17X\x04\x00\x00\x00nameq\x02X\x0c\x00\x00\x00zhangdaxiangq\x03X\x04\x00\x00\x00cityq\x04X\x08\x00\x00\x00hangzhouq\x05u.'
```
可以使用`pickle.loads()`直接把一个`bytes`对象反序列化，也可以用`pickle.load()`函数从一个`file-like Object`中反序列化对象：
```python
import pickle
#打开一个file-like Object
with open('dump.txt','rb') as f:
	#反序列化
	d = pickle.load(f) #因为变量之前的内存已经回来，反序列化出来的变量虽然值和之前一样，但是并不是一个变量了
	print(d)


#直接反序列化bytes
d = dict(name='zhangdaxiang',age=23,city='hangzhou')
#反序列化bytes
>>> pickle.loads(pickle.dumps(d))
{'city': 'hangzhou', 'name': 'zhangdaxiang', 'age': 23}
```
#### json
如果我们想在不同的程序直接传递数据，可以把对象转换成`json`格式，`json`格式就是一个字符串，所以任何程序都可以读取。
`python`提供了一个`json`模块来完成转换，和序列化类似的，也有`dump()`和`dumps()`来转换成`json`，也有`load()`和`loads()`来把`json`转换成`python`对象。比如说转换一个`dict`对象：
```python
>>> import json
>>> d = dict(name='zhangdaxiang',age=23,city='hangzhou') 
#返回json对象
>>> json.dumps(d)
'{"city": "hangzhou", "age": 23, "name": "zhangdaxiang"}'
```
和序列化类似，`dump()`函数是直接把`json`写入一个`file-like Object`对象。
反序列化的`loads()`和`load()`函数也类似：
```python
#定义json对象
>>> json_str = '{"city": "hangzhou", "age": 23, "name": "zhangdaxiang"}'
#反序列化，返回dict
>>> json.loads(json_str)
{'name': 'zhangdaxiang', 'city': 'hangzhou', 'age': 23}
```
从上面可以看出，`json`中的`{}`是对应`python`中的`dict`，这样就可以互相转换。但是更多的时候，我们是用一个类来表示一个对象，如果用`json`模块来转换的话，就会报`TypeError`的错误。不必惊慌，`dumps()`函数有一个可选参数`default`，这个参数可以把对象转换成可以转换为`json`的对象，然后就可以和之前转换`dict`一样转换了。一般情况下，我们可以定义一个函数把类转换为`dict`来解决：
```python
#导入json
import json
#定义一个类
class People():
	def __init__(self,name,age,city):
		self.name = name
		self.age = age
		self.city = city
#定义一个转化函数
def People2dict(x):
	return {'name': x.name
	,'age':x.age
	,'city':x.city}
#实例化类
people = People('zhangdaxiang',23,'city')
#转换为json
print(json.dumps(people,default=People2dict))
#输出
{"age": 23, "city": "hangzhou", "name": "zhangdaxiang"}
```
这样就和之前一样了。
我们也可以使用一个偷懒的方法，因为类的实例一般都有`__dict__`方法的话，`people.__dict__`后就可以认为是一个`dict`。我们只需要如此操作即可：
```python
print(json.dumps(people,default=lambda x:x.__dict__))
#输出
{"name": "zhangdaxiang", "age": 23, "city": "hangzhou"}
```
同样的，我们想要反序列化`json`字段的话，我们先用`loads()`返回一个`dict`，然后自定义一个函数来把`dict`传入`object_hook`来转化成类。
```python
#定义json对象
json_str = '{"city": "hangzhou", "age": 23, "name": "zhangdaxiang"}'
#字典转化为类实例
def dict2People(x):
	return People(x['name'],x['age'],x['city'])
#反序列化
print(json.loads(json_str,object_hook = dict2People))
#输出
<__main__.People object at 0x0000000003496EB8>
```
这样就打印出了实例对象的内存地址。
	
