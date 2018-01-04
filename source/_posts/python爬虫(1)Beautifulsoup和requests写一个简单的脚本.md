---
title: python爬虫(1)Beautifulsoup和requests写一个简单的脚本
date: 2016-12-11 11:26:31
tags: [python,爬虫]
categories: python
---
主要是用`bs4`下的`Beautifulsoup`和`requests`库来编写一个简单的爬虫，爬取的是59同城网的一个详情页面`http://bj.58.com/pingbandiannao/31804057080125x.shtml`。
```python
from bs4 import BeautifulSoup
import requests
url = 'http://bj.58.com/pingbandiannao/31804057080125x.shtml'
#可设置代理
#proxies = { 
#	"http": "http://"+ip,  
#	"https": "http://"+ip,  
#}
#这里可以添加`User-Agent`和`cookie`和`Referer`等请求参数。
headers = {
    'User-Agent':'Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.86 Safari/537.36'
	#这个表明网页调用接口的来源，不添加的话，之后调用浏览数的api不会返回具体浏览数，会返回0。
	,'Referer':'http://bj.58.com/pingbandiannao/31804057080125x.shtml'
}
#requests以get()方法打开网页，然后使用text方法取出内容
data = requests.get(url,headers = headers,proxies = proxies)
#BeautifulSoup使用'lxml'来简析这个网页
soup = BeautifulSoup(data.text,'lxml')
#select方法是通过cssSelector选择器来筛选数据的
type = soup.select('.crb_i')[0].get_text()
title = soup.select('.col_sub.mainTitle > h1')[0].get_text()
time = soup.select('li.time')[0].get_text()
#stripped_strings提取一个父标签下，被多个子标签分割的文本
place = ' '.join(list(soup.select('span.c_25d')[0].stripped_strings))
#浏览量是通过js异步加载的，我们需要调用返回浏览量的api
#获取标签id：31804057080125
apid = url.split('/')[-1].split('x.')[0]
#拼接api地址
api = 'http://jst1.58.com/counter?infoid={}'.format(apid)
print(api)
api_data = requests.get(api,headers=headers)
#获取浏览量
view = api_data.text.split('=')[-1]
data = {
	'type':type
	,'title':title
	,'time':time
	,'place':place
	,'view':view
}
print(data)
```
这里`Beautifulsoup`的`select`方法是使用`cssSelector`选择器来筛选的。
<a href = '/html/css.html'>查看css选择器</a>
这样，一个详情页就爬取完毕了，如果想批量爬取多个详情页，观察各个详情页的`url`，找到规律，然后就可以爬取了。为了避免反爬，最好使用`time.sleep(10)`来限制爬取频率。
