---
title: python之路(4):模块
date: 2016-11-15 10:37:48
tags: [python,module]
categories: python
---
在`python`中，一个`.py`文件就是一个模块，我们可以将模块导入，直接使用里面已经写好的功能，所以`python`的拓展性是非常好的。但是如果我们的`.py`文件和`python`原有的模块名称重复了，就会出现问题。要解决这种情况，有一种方法是引入包。比如说我们的`zhangdaxiang.py`模块以及`bangbang.py`模块与系统原有模块重名了。我们可以选择一个顶层包名`mypy`,文件结构就成了这样：
```
mypy 
|____ __init__.py
|____ zhangdaxiang.py
|____ bangbang.py
```
这个时候,模块名就变成了`mypy.zhandaxiang`和`mypy.bangbang`，只要包名不一样，就没有问题。要注意的，每一个包目录下面都会有一个`__init__.py的文件`，这个文件是必须存在的，否则，`Python`就把这个目录当成普通目录，而不是一个包。`__init__.py`可以是空文件，也可以有Python代码，因为`__init__.py`本身就是一个模块，而它的模块名就是`mypy`。
而且，也可以使用多级目录，但是每个目录下面都要有`__init__.py`这个文件，比如：
```
mypy
|____ mymode
|	 |____ __init__.py
|	 |____ zhangdaxiang.py
|	 |____ bangbang.py
|____ __init__.py
|____ abc.py
```
这个时候，模块名称就是`mypy.mymode.zhangdaxiang`。
这种我们自己实现某些功能的模块就叫作第三方模块，于此对应的是`python`自带的功能模块叫作内置标准模块。
记录一些平时经常用到的内置标准模块和第三方模块：
### 内置标准模块
#### time模块
##### 时间表达形式
在`Python`中，通常有这三种方式来表示时间：时间戳、格式化的时间字符串、元组(struct_time)即结构化时间。
1.时间戳(`timestamp`) ：通常来说，时间戳表示的是从`1970年1月1日00:00:00`开始按秒计算的偏移量。我们运行`type(time.time())`，返回的是`float`类型。
2.格式化的时间字符串(`Format String`)： ‘1994-11-05’
3.元组(`struct_time`) ：`struct_time`元组共有9个元素共九个元素:(年，月，日，时，分，秒，一年中第几周，一年中第几天等）
```python
import time
#时间戳
>>> time.time()	#返回当前时间的时间戳
1424710567.7350585
#时间字符串
>>> time.strftime("%Y-%m-%d %X")	#%X为时分秒
'2017-04-26 11:48:36'
#时间元组
>>> time.localtime()	#本地结构化时间
time.struct_time(tm_year=2017, tm_mon=4, tm_mday=26, tm_hour=11, tm_min=50, tm_sec=16, tm_wday=3, tm_yday=116, tm_isdst=0)
>>> time.gmtime()		#格里尼治结构化时间
```
以上的函数，不加默认值，均为当前时间。总之，时间戳是计算机能够识别的时间，时间字符串是人能够看懂的时间，元组则是用来操作时间的。
##### 三种形式转换
转换规则可以看下二图
<img src = "/img/python之路(4)模块/time_img1.jpg" alt = "time_img1" width = 400 high = 400>
```python
#时间戳<－－－－>结构化时间：  localtime/gmtime   mktime
>>> time.localtime(time.time())
>>> time.gmtime(time.time())

>>> time.mktime(time.localtime()) 
>>> time.mktime(time.gmtime())

#字符串时间<－－－－>结构化时间： strftime／strptime

>>> time.strftime("%Y-%m-%d %X", time.localtime())
>>> time.strptime("2017-04-26","%Y-%m-%d")
```
<img src = "/img/python之路(4)模块/time_img2.jpg" alt = "time_img2" width = 400 high = 400>
```python
>>> time.asctime(time.localtime(time.time()))
'Wed Apr 26 17:26:00 2017'
>>> time.ctime(time.time())
'Wed Apr 26 17:26:04 2017'
```
```python
#其他方法
time.sleep()	#单位秒
```
#### random模块
```python
>>> import random
>>> random.random()			#大于0且小于1之间的小数
>>> random.randint(1,5)		#大于等于1且小于等于5之间的整数
>>> random.randrange(1,5)		#大于等于1且小于5之间的整数
>>> random.choice([1,'23',[4,5]])	#1或者23或者[4,5]
>>> random.sample([1,'23',[4,5]],2)	#列表元素任意2个组合
>>> random.uniform(1,3)		#大于1小于3的小数

# 打乱次序
>>> item=[1,3,5,7,9]
>>> random.shuffle([1,3,5,7,9])
>>> item
[5, 1, 3, 7, 9]
>>> random.shuffle(item)
>>> item
[5, 9, 7, 1, 3]
```
#### os模块
```python
>>> import os
>>> os.getcwd()	#当前python脚本工作的目录路径
>>> os.chdir()	#改变工作目录
>>> os.curdir()	#返回当前目录，即('.')
>>> os.pardir()	#返回上一级目录,即('..')
>>> os.makedirs('dirname1/dirname2')	#生成多层递归目录
>>> os.removedirs('dirname1')	#若目录为空，则删除，并递归到上一级目录，如若也为空，则删除，依此类推
>>> os.mkdir('dirname')	#生成单级目录
>>> os.rmdir('dirname')	#删除单级目录
>>> os.listdir()	#列出当前目录下的文件和子目录，包括隐藏的文件
>>> os.remove()	#删除一个文件
>>> os.rename("oldname","newname")	#重命名文件/目录
>>> os.sep	#输出操作系统特定的路径分隔符，win下为"\\",Linux下为"/"
>>> os.linesep	#输出当前平台使用的行终止符，win下为"\t\n",Linux下为"\n"
>>> os.pathsep		#输出用于分割文件路径的字符串 win下为;,Linux下为:
>>> os.name		#输出字符串指示当前使用平台。win->'nt'; Linux->'posix'
>>> os.system("bash command")		#运行shell命令，直接显示
>>> os.environ 	#获取系统环境变量
>>> os.stat('path/filename')		#获取文件/目录信息
```
```python
#os.stat('path/filename')的结构

st_mode:		inode 保护模式
st_ino: 		inode 节点号。
st_dev: 		inode 驻留的设备。
st_nlink: 		inode 的链接数。
st_uid: 		所有者的用户ID。
st_gid: 		所有者的组ID。
st_size: 		普通文件以字节为单位的大小；包含等待某些特殊文件的数据。
st_atime:		上次访问的时间。
st_mtime:	最后一次修改的时间。
st_ctime: 		由操作系统报告的"ctime"。在某些系统上（如Unix）是最新的元数据更改的时间，在其它系统上（如Windows）是创建时间（详细信息参见平台的文档）。
```
```python
>>> os.path.abspath(path) 	#返回path规范化的绝对路径
>>> os.path.split(path) 	#将path分割成目录和文件名二元组返回
>>> os.path.dirname(path) 	#返回path的目录。即os.path.split(path)的第一个元素
>>> os.path.basename(path) 	#返回path最后的文件名。若path以／或\结尾，那么就会返回空值。即os.path.split(path)的第二个元素
>>> os.path.exists(path) 	#如果path存在，返回True；如果path不存在，返回False
>>> os.path.isabs(path) 	#如果path是绝对路径，返回True
>>> os.path.isfile(path) 	#如果path是一个存在的文件，返回True。否则返回False
>>> os.path.isdir(path) 	#如果path是一个存在的目录，则返回True。否则返回False
>>> os.path.join(path1[, path2[, ...]]) 	#将多个路径组合后返回，第一个绝对路径之前的参数将被忽略
>>> os.path.getatime(path) 	#返回path所指向的文件或者目录的最后访问时间
>>> os.path.getmtime(path) 	#返回path所指向的文件或者目录的最后修改时间
>>> os.path.getsize(path) 	#返回path的大小
```
#### sys模块
```python
>>> import sys
>>> sys.argv 	#命令行参数的list，python 123.py a b c，第一个元素是程序本身路径,即返回[abspath//123.py,a,b,c]
>>> sys.exit(n) 	#退出程序，正常退出时exit(0)
>>> sys.maxint	#最大的int值
>>> sys.platform	#输出当前作系统平台名称
>>> sys.path	#输出python的模块搜索路径，即环境变量
>>> sys.version	#输出解释器版本
```
#### logging模块
`logging`模块分5种高级级别，默认是`WARNING`。默认的日志格式为日志级别：`Logger`名称：用户输出消息。

```python
>>> import logging  
logging.debug('debug message')  
logging.info('info message')  
logging.warning('warning message')  
logging.error('error message')  
logging.critical('critical message') 
```
配置日志格式，可以通过`logging.basicConfig`来配置。
```pythin
logging.basicConfig(level=logging.DEBUG,  
                    format='%(asctime)s %(filename)s[line:%(lineno)d] %(levelname)s %(message)s',  
                    datefmt='%a, %d %b %Y %H:%M:%S',  
                    filename='/tmp/test.log',  
                    filemode='w')  
  
logging.debug('debug message')  
logging.info('info message')  
logging.warning('warning message')  
logging.error('error message')  
logging.critical('critical message')
```
配置参数
```python
logging.basicConfig()函数中可通过具体参数来更改logging模块默认行为，可用参数有：

filename：用指定的文件名创建FiledHandler，这样日志会被存储在指定的文件中。
filemode：文件打开方式，在指定了filename时使用这个参数，默认值为“a”还可指定为“w”。
format：指定handler使用的日志显示格式。
datefmt：指定日期时间格式。
level：设置rootlogger的日志级别
stream：用指定的stream创建StreamHandler。可以指定输出到sys.stderr,sys.stdout或者文件(f=open(‘test.log’,’w’))，默认为sys.stderr。若同时列出了filename和stream两个参数，则stream参数会被忽略。

format参数中可能用到的格式化串：
%(name)s Logger的名字
%(levelno)s 数字形式的日志级别
%(levelname)s 文本形式的日志级别
%(pathname)s 调用日志输出函数的模块的完整路径名，可能没有
%(filename)s 调用日志输出函数的模块的文件名
%(module)s 调用日志输出函数的模块名
%(funcName)s 调用日志输出函数的函数名
%(lineno)d 调用日志输出函数的语句所在的代码行
%(created)f 当前时间，用UNIX标准的表示时间的浮 点数表示
%(relativeCreated)d 输出日志信息时的，自Logger创建以 来的毫秒数
%(asctime)s 字符串形式的当前时间。默认格式是 “2003-07-08 16:49:45,896”。逗号后面的是毫秒
%(thread)d 线程ID。可能没有
%(threadName)s 线程名。可能没有
%(process)d 进程ID。可能没有
%(message)s用户输出的消息
```
一般我们用的比较多的是创建`Logger`对象，然后来输出日志，如下
```python
import logging

logger = logging.getLogger()

#设定日志级别
logger.setLevel('DEBUG')

#创建一个写入文件的Handler
fileHandler = logging.FileHandler('test.log')

#创建一个输出到屏幕的Handler
streamHandler = logging.StreamHandler()

#格式化字符串
formatter = logging.Formatter('%(asctime)s - %(name)s - %(levelname)s - %(message)s')

fileHandler.setFormatter(formatter)
streamHandler.setFormatter(formatter)

#logger对象可以添加多个Handler
logger.addHandler(fileHandler)
logger.addHandler(streamHandler)

logger.debug('logger debug message')
logger.info('logger info message')
logger.warning('logger warning message')
logger.error('logger error message')
logger.critical('logger critical message')
```
日志等级分别有以下几种：
```python
CRITICAL : 'CRITICAL',
ERROR : 'ERROR',
WARNING : 'WARNING',
INFO : 'INFO',
DEBUG : 'DEBUG',
NOTSET : 'NOTSET'
```
#### hashlib模块
`hashlib`是个专门提供hash算法的库，现在里面包括`md5`,`sha1`,`sha224`,`sha256`,`sha384`,`sha512`。
```python
import hashlib

md51 = hashlib.md5()
md51.update('12345678'.encode('utf-8'))
print(md51.hexdigest())


#如果数据量过大，可以分多次update()，结果一样
md52 = hashlib.md5()
md52.update('1234'.encode('utf-8'))
md52.update('5678'.encode('utf-8'))
print(md52.hexdigest())

```
由于常用口令的MD5值很容易被计算出来，所以，要确保存储的用户口令不是那些已经被计算出来的常用口令的MD5，这一方法通过对原始口令加一个复杂字符串来实现，俗称“加盐”：
```python
hashlib.md5("salt".encode("utf8"))
```
### 第三方模块
