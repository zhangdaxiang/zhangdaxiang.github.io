---
title: pymongo的基本操作
date: 2016-12-14 13:56:18
tags: [python,pymongo,爬虫]
categories: python
---
前几天写了个简单爬虫爬取了58的数据，今天想要把他和`Mongodb`数据库连接起来。
### Windows平台安装Mongodb
#### Mongodb下载
首先去`Mongodb`官网下载一个MongoDB 预编译二进制包，<a href="https://www.mongodb.com/download-center#community">下载地址</a>。
#### 创建数据目录
`Mongodb`将数据存储在`db`目录下，但是这个数据目录不会自己创建，我们需要在安装完成后创建它。(注意，数据目录要放在根目录下。比如`C:\`和`D:\`)
我们创建一个`db\data\`目录。
```shell
cd /d D:\
mkdir data\db\
```
#### 配置Mongodb服务
先在`data`目录下再创建一个`long`文件夹：
```shell
mkdir data\log\
```
然后在`Mongodb`目录文件下再创建一个配置文件`C:\mongodb\mongod.cfg`,其中指定 `systemLog.path`和`storage.dbPath`。具体配置如下：
```shell
systemLog:
    destination: file #注意是四个空格不是Tab
    path: D:\data\log\mongod.log
storage:
    dbPath: D:\data\db
```
#### 安装Mongodb服务
通过执行`mongod.exe`，使用`--install`选项来安装服务，使用`--config`选项来指定之前创建的配置文件。这样我们可以开机自动启动`Mongodb`服务了。
```shell
"D:\mongodb\bin\mongod.exe" --config "D:\mongodb\mongod.cfg" --install
```
然后启动`Mongodb`服务：
```shell
net start MongoDB
```
完成，这时候，运行`Mongodb`目录下的`mongo.exe`，就可以进入交互模式了。
### pymongo基本操作
首先导入`pymongo`
```python
import pymongo
```
#### 创建连接
指定`host`及`port`参数：
```python
client = pymongo.MongoClient('localhost',27017)
```
或者这样
```python
client = pymongo.MongoClient('mongodb://localhost:27017/')
```
#### 连接数据库
```python
tongcheng = client.tongcheng
```
或者
```python
tongcheng = client['tongcheng']
```
#### 连接聚集
相当于关系型数据库中的表
```python
tongchengsheet = tongcheng.tongchengsheet
```
或者
```python
tongchengsheet = tongcheng['tongchengsheet']
```
查看数据库下面所有的聚集
```python
tongcheng.collection_names()
```
#### 插入记录
```python
data = {
  'name':name
  ,'age':age
  ,'city':city
}
tongchengsheet.insert(data)
```
注意，这里插入的一定是一个字典。
#### 按条件删除记录
```python
tongchengsheet.remove({'name':name})
```
即为删除`name`值为“name”的记录。
#### 更新数据
```python
tongchengsheet.update({'name':name},{'$set':{'age':age,'city':city}})
```
即为把`name`条件为“name”的行，`age`和`city`字段改成设定的值。这样只会修改发现的第一条数据，如果想修改所有的文档，需要把`multi`参数改成`true`。
```python
tongchengsheet.update({'name':name},{'set':{'age':age,'city':city}},{'multi':true})
```
#### 查找数据
```python
#find_one()查询一条数据，不带参数则返回第一条，带参数则按条件查找
tongchengsheet.find_one()
tongchengsheet.find_one({'name':name})
#find_one()查询所有数据，不带参数则返回所有数据，带参数则按条件查找
tongchengsheet.find()
tongchengsheet.find({'name':name})
```
可以用`count()`方法查看聚集数据数量,`limit()`方法限制条数
```python
tongchengsheet.find().count()
#查看前n条
tongchengsheet.find().limit(n)
```
类似于关系型数据库的`ORDER BY`,我们同样可以使用`sort()`函数来排序
```python
#默认升序
tongchengsheet.find().sort('name')
#ASCENDING为升序
tongchengsheet.find().sort('name',pymongo.ASCENDING)
#DESCENDING为降序
tongchengsheet.find().sort('name',pymongo.DESCENDING)
#多字段排序
tongchengsheet.find().sort([('name',pymongo,ASCENDING),('age',pymongo.DESCENDING)])
```
#### 结语
其他操作以后用到再补充。