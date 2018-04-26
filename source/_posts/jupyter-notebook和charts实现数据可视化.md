---
title: jupyter notebook和charts实现数据可视化
date: 2017-12-14 11:52:56
tags: [charts,python]
categories: python
---
### 安装jupyter
Jupyter Notebook是一个交互式笔记本，是一个功能非常强大的交互式工具。一般来说，我们安装的话，只要使用`pip install jupyter`就可以了。但是如果安装失败，我们也可以使用`anaconda`这个包管理工具来安装，这个还是比较方便的。
这里有一个jupyter notebook的教程，比较简单，但是也还不错。<a href ='https://www.cnblogs.com/nxld/p/6566380.html'>教程地址</a>

### chart模块
#### 安装
首先`pip`安装`charts`模块，`pip install charts`。这个时候试试`import`是否成功，如果成功，那么OK。如果失败的话，找到`site-packages`文件路径，打开`charts`文件夹，将`charts`文件下的7个文件替换，替换地址为<a href='https://github.com/zhangdaxiang/charts-replace'>文件地址</a>。
#### 柱状图
接下来我们把之前爬取到的赶集网的数据用图形表示出来,这样数据比较起来更加直观。
```python
import pymongo
import charts
#连接之前爬取的数据，在Mongodb里面
client = pymongo.MongoClient('localhost',27017)
ganji = client['ganji']
ganji3 = ganji['ganji3']
cate_list = []
#取之前爬取到的cates属性的第二个标签来分类
cate_list = [i['cates'][2] for i in ganji3.find()]
#set来进行去重，取出不重复的标签
cate_index = list(set(cate_list))
#取出每个标签对应的数量
cate_list = [i['cates'][2] for i in ganji3.find()]
num_list = [cate_list.count(i) for i in cate_index]
#使用zip函数来一一对应
def cate_num():
	for k,v in zip(cate_index,num_list):
		data = {
		'name':k
		,'data':[v]
		,'type':'column'
		}
		yield data
#使用charts的plot方法生成柱状图
charts.plot([i for i in cate_num()],show='inline',options={'title':{'text':'ganji_cate'}})
```
<img src='/img/jupyter-notebook和charts实现数据可视化/ganji_cates.jpg' alt='ganji_cates' width='800' height='800' >

#### 折线图
```python
import pymongo
from datetime import date,timedelta
import charts
#连接Mongodb
client = pymongo.MongoClient('localhost',27017)
ganji3 = client['ganji']
ganjisheet = ganji3['ganji3']
#统一日期格式
for i in ganjisheet.find():
    if len(i['pub_date'].split('.'))>1:
        ganjisheet.update({'_id':i['_id']},{'$set':{'pub_date':'-'.join(i['pub_date'].split('.'))}})
    else:
        pass

#返回一段时间，timedelte()和data()是处理日期的函数
def date_num(date1,date2):
	#data()函数输入三个自然数，输出一个字符串
    start_date = date(int(date1.split('-')[0]),int(date1.split('-')[1]),int(date1.split('-')[2]))
    end_date = date(int(date2.split('-')[0]),int(date2.split('-')[1]),int(date2.split('-')[2]))
    while start_date <= end_date:
        yield start_date
        #timedelta(1)就表示1天
        start_date += timedelta(1)

#返回data生成器
def ares_num(areas,date1,date2):
    for area in areas:
        area_num = []
        for date in date_num(date1,date2):
            num = ganjisheet.find({'pub_date':date.strftime('%Y-%m-%d'),'area':area}).count()
            area_num.append(num)
        data = {'name':area
               ,'data':area_num
               ,'type':'line'}
        yield data

#表格参数
options = {
    'title': {'text': '我是标题'}
    ,'subtitle': {'text': '我是副标题'}
    ,'xAxis': {'categories': [i.strftime('%Y-%m-%d') for i in date_num('2016-01-01','2016-01-10')]
              ,'title':{'text':'x轴标题'}}
    ,'yAxis':{'title':{'text':'y轴标题'}}}


series = [i for i in ares_num(['朝阳','海淀','通州'],'2016-01-01','2016-01-10')]
charts.plot(series,show = 'inline',options = options)
```
<img src='/img/jupyter-notebook和charts实现数据可视化/ganji_cates2.jpg' alt='ganji_cates2' width='800' height='800' >