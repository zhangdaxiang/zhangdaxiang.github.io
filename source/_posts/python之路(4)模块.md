---
title: python之路(4):模块
date: 2017-11-15 10:37:48
tags: python
categories: python
---
在`python`中，一个`.py`文件就是一个模块，我们可以将模块导入，直接使用里面已经写好的功能，所以`python`的拓展性是非常好的。但是如果我们的`.py`文件和`python`原有的模块名称重复了，就会出现问题。要解决这种情况，有一种方法是引入包。比如说我们的`zhangdaxiang.py`模块以及`bangbang.py`模块与系统原有模块重名了。我们可以选择一个顶层包名`mypy`,文件结构就成了这样：
```
mypy 
|____ __init__.py
|____ zhangdaxiang.py
|____ bangbang.py
```
这个时候，模块名称就是`mypy.mymode.zhangdaxiang`。

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
这种我们自己实现某些功能的模块就叫作第三方模块，于此对应的是`python`自带的功能模块叫作内置标准模块。
记录一些平时经常用到的内置标准模块和第三方模块：
### 内置标准模块
### 第三方模块
