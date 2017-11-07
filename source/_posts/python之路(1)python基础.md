---
title: 'python之路(1):python基础'
date: 2016-11-06 11:57:03
tags: python
categories: python
---
### 数据类型和变量
python的数据类型可以分为以下几种：
#### 整数
`python`中的整数和数学中的整数定义并没有什么不同，但是由于计算机使用的是二进制，所以用`0x`为前缀的十六进制来表示更为方便，比如`0xa5b4c4d2`等
#### 浮点数
浮点数就是我们通常意义上的小数,对于很大的浮点数,我们需要用科学计数法来表示,把其中的10用`e`来代替。比如说`1.6x10^9`就表示为`1.6e9`。
#### 字符串
字符串就是用`'`和`"`双引号括起来的任意文本，比如`'abc'`和`"abc"`就是同一字符串。如果字符串中同时包含`'`和`"`的话，可以使用转义符`\`来转义：
```python
'I\'m \"daxiang\"'
```
就是
```python
I'm "daxiang"
```
转义符`\`还可以转义很多东西，比如`\n`就是换行，`\t`就是制表符，而`\\`表示的就是`\`。为了避免转义符带来的困扰，`python`还引入了`r''`原始字符串的概念，`r''`中间的字符串默认不转义。
#### 布尔值
布尔值就是`True`和`False`,拥有运算`and`,`or`,`not`。
#### 空值
空值在`python`中用`None`表示，`None`不等同于`0`，`0`在`python`是有实际意义的一个数字，而`None`是一个特殊的空值。
### 字符串和编码

#### 字符编码
说到字符编码，首先要介绍一下他的产生历史。一开始呢，只有`127`个字符被编码到了计算机里面，就是一些大小写字母和一些特俗符合，这个叫做`ASCII`码。这个对于外国人来说，已经够用了。但是如果我们想处理中文的话，显然这套编码是不够用的。所以中国人就制定了`GB2312`编码提供给自己使用。同样的，每种语言都给自己制定了一套编码。很快，编码世界就非常混乱，多语言的文本经常出现乱码问题。所以，经过讨论，制定出了一种适合所有语言的编码，叫做`Unicode`。这套编码把所有语言都包含进去了，所以也不存在乱码的问题。
但是，存在的问题也很明显。`ASCII`编码占用的是一个字节，而`Unicode`编码通常占用两个字节。。虽然乱码的问题解决了，但是存储和传输所消耗的资源却比之前多很多。对于之前适用`ASCII`编码的那些字符，套用`Unicode`编码就显得非常不合适。
本着节约的精神，"可变长编码"`utf-8`编码也就应运而生。`utf`把将`Unicode`编码的字符根据数字大小编码成1-6个字节。常见的英文字母被编码成1个字节，而中文一般被编码成3个字节，只有一些生僻的字符才会被编码成4-6个字节。同时，还可以将`ASCII`编码视为`utf-8`编码的一部分，这样也可以和之前使用`ASCII`编码的程序兼容，解决历史遗留问题。
然后，我们概括一下计算机中编码的工作方式：
在计算机内存里，统一使用`Unicode`编码，在需要保存和传输的时候，转换成`utf-8`编码。
比如说我们在编辑一个`txt`文件，我们先将从文件中读取的`utf-8`字符转换成`Unicode`字符存放在内存里。在编辑完以后，再转换成`utf-8`字符存储到`txt`文件。
再比如说我们在浏览一个网页，服务器先将动态生成的`Unicode`转换成`utf-8`字符，然后传输给我们。我们接收到`utf-8`字符再转换成`Unicode`字符展现出来。
#### python的字符串
字符串最大的特点,即**不可变**，不论使用什么函数，字符串的本身是**不会变化**的。
在`python3`中，已经适配了`Unicode`编码，所以说`python3`可以直接输出中文，而不会像`python2`那样乱码了。同时，`python3`提供了`ord()`来将字符转换成编码的整数形式，提供了`chr()`将整数形式的编码转换成字符。
```python
>>> ord('A')
65
>>> chr(66)
'B'
```
也可以用十六进制来表示整数:
```python
>>> '\u5f20\u5927\u8c61'
'张大象'
```
这两张写法是完全等价的。
在`python3`中，字符串类型是`str`，在内存中以`Unicode`表示，一个字符对应几个字节。如果需要传输或者保存的话，就需要通过`encode()`方法转换成以字节为单位的字符串类型`bytes`。在`python`中对于`bytes`类型的数据，用带b前缀的单引号或者双引号表示。
```python
>>> b'ABC'
b'ABC'
```
这里的`b'ABC'`和`'ABC'`是不一样的，一个是`bytes`，一个是`str`。其中`b'ABC'`是以字节为单位的。
如果`b''`中间的字符不是`ASCII`中的，则会报错：
```python
>>> b'张大象'
SyntaxError: bytes can only contain ASCII literal characters.
```
同时，对于无法用`ASCII`编码表示的字符，`python`会用`/x###`来表示：
```python
>>> 'ABC'.encode('utf-8')
b'ABC'
>>> '张大象'.encode('utf-8')
b'\xe5\xbc\xa0\xe5\xa4\xa7\xe8\xb1\xa1'
```
反过来，如果从磁盘中或者浏览器得到`bytes`的字节流，我们希望转换成`Unicode`的字符，可以使用`decode()`：
```python
>>> b'ABC'.decode('utf-8')
'ABC'
>>> b'\xe5\xbc\xa0\xe5\xa4\xa7\xe8\xb1\xa1'.decode('utf-8')
'张大象'
```
可以使用`len()`来计算长度，`str`计算的是字符数，`bytes`计算的是字节数：
```python
>>> len('张大象')
3
>>> len(b'\xe5\xbc\xa0\xe5\xa4\xa7\xe8\xb1\xa1')
9
```
由此可见，中文在`utf-8`中一般编码成3个字节。
记录一下字符串的常见操作：
1.去除空格:
```python
>>> name=' zhangdaxiang '
>>> name.strip() #删除字符串两边的指定字符，括号的写入指定字符，默认为空格
'zhangdaxiang'
>>> name.lstrip() #删除字符串左边的指定字符，括号的写入指定字符，默认为空格
'zhangdaxiang '
>>> name.rstrip() #删除字符串右边指定字符，默认为空格
' zhangdaxiang'
```
2.连接字符串:
```python
#万恶的加号
>>> name='zhangdaxiang '
>>> profession=' student'
>>> str=name+'is a'+profession
>>> str
'zhangdaxiangis astudent'
#使用一次加号会开辟一个name+profession大小的存储单元，如果使用N次加号，就会开辟N次，这是非常消耗资源的，也是"万恶"的来源。
>>> num='12345'
>>> str='+'
>>> result=str.join(num)
>>> result
'1+2+3+4+5'
```
3.查找字符串：
```python
#str.index()和str.find()函数的区别在于，str.find()查找失败会返回-1，不影响程序运行，而str.index()会报错，所以我们一般使用str.find()!=-1或str.find()>-1作为判断条件。
>>> name='zhangdaxiang'
>>> name.index('a')
2
>>> name.find('a')
2
>>> name.find('j')
-1
>>> name.index('j')
Traceback (most recent call last):
  File "<pyshell#31>", line 1, in <module>
    name.index('j')
ValueError: substring not found
```
4.是否包含指定字符串：
使用`in`和`not in`，返回的是`True`和`False`。
5.字符串中字母大小写转换
```python
>>> name='zHangdaXiAng'
>>> name.lower() #全部转换为小写
'zhangdaxiang'
>>> name.upper() #全部转换成大写
'ZHANGDAXIANG'
>>> name.swapcase() #大小写互换
'ZhANGDAxIaNG'
>>> name.capitalize() #首字母大写
'Zhangdaxiang'
```
6.将字符串放入中心位置可指定长度以及位置两边字符：
```python
>>> name='zhangdaxiang'
>>> name.center(40,'*')
'**************zhangdaxiang**************'
```
7.字符串统计:
```python
>>> name='zhangdaxiang'
>>> name.count('a')
3
```
8.字符串的判断函数:
```python
S.startswith(prefix[,start[,end]]) #是否以prefix开头 
S.endswith(suffix[,start[,end]]) #以suffix结尾 
S.isalnum()    #是否全是字母和数字，并至少有一个字符 
S.isalpha()    #是否全是字母，并至少有一个字符 
S.isdigit()    #是否全是数字，并至少有一个字符 
S.isspace()    #是否全是空白字符，并至少有一个字符 
S.islower()    #S中的字母是否全是小写 
S.isupper()    #S中的字母是否便是大写 
S.istitle()    #S是否是首字母大写的
```
### 列表和元组
#### 列表
列表(List)是`python`中的一种有序集合。我们可以很方便地存储数据，并添加删除。
定义列表：
```python
names=['zhangdaxiang','张大象','bangbangtang','棒棒糖']
```
通过下标访问元素：
```python
>>> names[2]
'bangbangtang'
>>> names[-1]
'棒棒糖'
```
如果希望同时访问多个数据，可以使用切片：
```python
>>> names[1:3]  #取下标1至下标4之间的数字，包括1，不包括3
['张大象', 'bangbangtang']
>>> names[1:-1] #取下标1至-1的值，不包括-1
['张大象', 'bangbangtang']
>>> names[0:3] 
['zhangdaxiang', '张大象', 'bangbangtang']
>>> names[:3] #如果是从头开始取，0可以忽略，跟上句效果一样
['zhangdaxiang', '张大象', 'bangbangtang']
>>> names[2:] #如果想取最后一个，必须不能写-1，只能这么写
['bangbangtang', '棒棒糖']
>>> names[2:-1] #这样-1就不会被包含了
['bangbangtang']
>>> names[0::2] #后面的2是代表，每隔一个元素，就取一个
['zhangdaxiang', 'bangbangtang']
>>> names[::2] #和上句效果一样
['zhangdaxiang', 'bangbangtang']
```
如果想在列表最后添加一个元素可以使用`append()`:
```python
>>> names.append('biaobiao')
>>> names
['zhangdaxiang', '张大象', 'bangbangtang', '棒棒糖', 'biaobiao']
```
使用`insert()`可以在指定位置插入元素：
```python
>>> names.insert(1,'飙飙')
>>> names
['zhangdaxiang', '飙飙', '张大象', 'bangbangtang', '棒棒糖', 'biaobiao']
```
要删除末尾元素可以使用pop(),删除指定位置元素使用pop(i):
```python
>>> names.pop()
'biaobiao'
>>> names
['zhangdaxiang', '飙飙', '张大象', 'bangbangtang', '棒棒糖']
>>> names.pop(1)
'飙飙'
>>> names
['zhangdaxiang', '张大象', 'bangbangtang', '棒棒糖']
```
这样又回到了初始状态。
也可以使用`del`和`remove()`来删除：
```python 
>>> del names[2]
>>> names
['zhangdaxiang', '张大象', '棒棒糖']
>>> names.remove('棒棒糖') #删除指定元素
>>> names
['zhangdaxiang', '张大象'] 
```
也可以同时添加多个元素,使用`extend()`:
```python
>>> names.extend(['bangbangtang','棒棒糖'])
>>> names
['zhangdaxiang', '张大象', 'bangbangtang', '棒棒糖']
```
使用`index()`可以查看元素所在下标：
```python
>>> names.index('张大象')
1
```
使用`count()`可以实现计数功能：
```python
>>> names.append('张大象')
>>> names
['zhangdaxiang', '张大象', 'bangbangtang', '棒棒糖', '张大象']
>>> names.count('张大象')
2
```
翻转和排序：
```python
>>> names=['zhangdaxiang', '张大象', 'bangbangtang', '棒棒糖', 1, 2, 3]
>>> names.sort() #排序
Traceback (most recent call last):
  File "<pyshell#37>", line 1, in <module>
    names.sort()
TypeError: unorderable types: int() < str()  #3.0里不同数据类型不能放在一起排序了
>>> names=['zhangdaxiang', '张大象', 'bangbangtang', '棒棒糖', '1', '2', '3']
>>> names.sort()
>>> names
['1', '2', '3', 'bangbangtang', 'zhangdaxiang', '张大象', '棒棒糖']
>>> names.reverse()#翻转
>>> names
['棒棒糖', '张大象', 'zhangdaxiang', 'bangbangtang', '3', '2', '1']
```
#### 元组
元组(tuple)和`list`是非常相似的，也是一个有序的集合。但是元组有一个特性，那就是不可更改。这样，`tuple`具有更高的安全性，所以我们如果在可以用`tuple`代替`list`的地方，就劲量使用`tuple`。
定义一个`tuple`:
```python
>>> names = ('zhangdaxiang','张大象')
>>> names
('zhangdaxiang', '张大象')
```
定义一个空的`tuple`:
```python
>>> names=()
>>> names
()
```
定义一个单一元素的`tuple`:
```python
>>> names=('zhangdaxiang',)
>>> names
('zhangdaxiang',)
```
如果使用`names=('zhangdaxiang')`,虽然不会报错，但是实际上`python`所做的操作是将`'zhangdaxiang'`这个字符串赋值给了`names`变量，并不是`tuple`。
### 字典和集合
#### 字典
`python`内置了字典(dict)，就是一种键值(key-value)对应的存储模式。这个和列表相比是**无序**的，但是和字典一样，非常便于查找，有很快的查找速度。
定义字典：
```python
>>> names={'name':'zhangdaxiang','age':'25','city':'hangzhou'}
>>> names
{'name': 'zhangdaxiang', 'city': 'hangzhou', 'age': '25'}
```
其中要注意的两点是：无序，`key`值是唯一的。
我们要额外增加一个元素的话很简单：
```python
>>> names['friend']='bangbangtang'
>>> names
{'name': 'zhangdaxiang', 'city': 'hangzhou', 'friend': 'bangbangtang', 'age': '25'}
```
修改元素：
```python
>>> names['friend'] = '飙飙'
>>> names
{'name': 'zhangdaxiang', 'city': 'hangzhou', 'friend': '飙飙', 'age': '25'}
```
删除操作的话和`list`也非常类似：
```python
>>> names.pop('age')#最常用的删除方式
'25'
>>> names
{'name': 'zhangdaxiang', 'city': 'hangzhou', 'friend': '飙飙'}
>>> del names['friend']
>>> names
{'name': 'zhangdaxiang', 'city': 'hangzhou'}
>>> names.popitem()#随机删除
('name', 'zhangdaxiang')
>>> names
{'city': 'hangzhou}
```
查找元素：
```python
>>> names={'name': 'zhangdaxiang', 'city': 'hangzhou', 'friend': '飙飙', 'age': '25'}
>>> 'name' in names #标准用法
True
>>> names.get('city') #获取
'hangzhou'
#如果key不存在，可以返回None，或者自己指定的value
>>> name.get('age1',-1)
-1
>>> names['age'] #同上，但是如果key不存在会报错，如下
'25'
>>> name['age1'] #key不存在
Traceback (most recent call last):
  File "<pyshell#18>", line 1, in <module>
    name['age1']
NameError: name 'name' is not defined
```
多级字典嵌套已经操作：
```python
av_catalog = {
    "欧美":{
        "www.youporn.com": ["很多免费的,世界最大的","质量一般"],
        "www.pornhub.com": ["很多免费的,也很大","质量比yourporn高点"],
        "letmedothistoyou.com": ["多是自拍,高质量图片很多","资源不多,更新慢"],
        "x-art.com":["质量很高,真的很高","全部收费,屌比请绕过"]
    },
    "日韩":{
        "tokyo-hot":["质量怎样不清楚,个人已经不喜欢日韩范了","听说是收费的"]
    },
    "大陆":{
        "1024":["全部免费,真好,好人一生平安","服务器在国外,慢"]
    }
}

av_catalog["大陆"]["1024"][1] += ",可以用爬虫爬下来"
print(av_catalog["大陆"]["1024"])
#ouput 
['全部免费,真好,好人一生平安', '服务器在国外,慢,可以用爬虫爬下来']
```
我们还需要了解的是`dict`的其他操作：
```python

>>> names={'name': 'zhangdaxiang', 'city': 'hangzhou', 'friend': '飙飙', 'age': '25'}
#values
>>> names.values()
dict_values(['zhangdaxiang', 'hangzhou', '飙飙', '25'])
#keys
>>> names.keys()
dict_keys(['name', 'city', 'friend', 'age'])
#item
>>> names.items()
dict_items([('name', 'zhangdaxiang'), ('city', 'hangzhou'), ('friend', '飙飙'), ('age', '25')])
#setdefault
>>> names.setdefault('school','JL')
'JL'
>>> names
{'name': 'zhangdaxiang', 'city': 'hangzhou', 'friend': '飙飙', 'age': '25', 'school': 'JL'}
>>> names.setdefault('friend','bangbangtang')
'飙飙'
>>> names
{'name': 'zhangdaxiang', 'city': 'hangzhou', 'friend': '飙飙', 'age': '25', 'school': 'JL'}
#update
>>> names={'name': 'zhangdaxiang', 'city': 'hangzhou', 'friend': '飙飙'}
>>> updates={'name':'张大象','age':'25'}
>>> names.update(updates)
>>> names
{'name': '张大象', 'city': 'hangzhou', 'friend': '飙飙', 'age': '25'}
```
了解这么多`dict`的基本操作方法，其实我们平时用到`dict`的时候，很多都是循环的，这是两种常用的循环方法：
```python
#方法1
for key in names:
	print(key,names[key])
#方法2
for key,value in names.items():
	print(key,values)
```
#### 元组
元组(set)和`list`非常相似，也是一个存储`key`的集合。但是`set`不存储`value`,同时，在`set`中，`key`值也是不可重复的。
定义`set`：
```python
>>> names=set(['张大象','飙飙','棒棒糖'])
>>> names
{'张大象', '飙飙', '棒棒糖'}
```
要注意的是，这里的三个元素和`dict`里是一样的，是没有顺序的。如果是重复元素的话，在`set`中会被直接过滤：
```python
>>> names=set(['张大象','飙飙','棒棒糖','张大象'])
>>> names
{'张大象', '飙飙', '棒棒糖'}
```
我们可以使用`add(key)`来为`set`添加元素，但是如果添加的是重复项的话，虽然不会报错，但是也不会有任何效果：
```python
>>> names.add('biaobiao')
>>> names
{'biaobiao', '张大象', '飙飙', '棒棒糖'}
>>> names.add('张大象')
>>> names
{'biaobiao', '张大象', '飙飙', '棒棒糖'}
```
然后，使用`remove(key)`可以删除：
```python
>>> names.remove('biaobiao')
>>> names
{'张大象', '飙飙', '棒棒糖'}
```
`set`和`dict`是非常像的，他们的区别就是有没有放入`value`。同样值得注意的是，`set`和`dict`都不可以放入**可变对象**，因为不能判定两个**可变对象**是否相等，这样就不能保证内部的唯一性。
### 不可变对象
上面我们提到了`list`，`dict`之类的是可变对象，`str`字符串是不可变对象。
对于可变对象，比如说`list`进行操作，对象是会改变的：
```python
>>> names=['张大象','飙飙','棒棒糖']
>>> names.sort()
>>> names
['张大象', '棒棒糖', '飙飙']
```
而对于不可变对象,`str`不论进行什么操作，对象都不会改变：
```python
>>> name='zhangdaxiang'
>>> name.replace('a','A')
'zhAngdAxiAng'
>>> name
'zhangdaxiang'
```
这样是不会改变的，想要改变的话，要把新生成的值赋给另外一个变量：
```python
>>> cname=name.replace('a','A')
>>> name
'zhangdaxiang'
>>> cname
'zhAngdAxiAng'
```