---
title: python爬虫同时添加用户代理和ip代理
date: 2017-10-21
tags: [python]
categories: python
---

### 为何要设置User Agent


>通过一段时间的爬虫学习，也成功实现了一些小的爬取项目，有的时候会出现代码正确但是无法爬取的情况。通过科学上网，了解到了有一些网站对于爬虫访问会有一定的措施，如果一段时间内同一ip访问次数过多，就会当做爬虫程序予以封杀。所以，我们需要伪装自己的爬虫程序，了解到伪装大致需要添加用户代理以及ip代理。下面是设置UA代理和ip代理的过程。


### 常见的User Agent


#### Android

```python
Mozilla/5.0 (Linux; Android 4.1.1; Nexus 7 Build/JRO03D) AppleWebKit/535.19 (KHTML, like Gecko) Chrome/18.0.1025.166 Safari/535.19
Mozilla/5.0 (Linux; U; Android 4.0.4; en-gb; GT-I9300 Build/IMM76D) AppleWebKit/534.30 (KHTML, like Gecko) Version/4.0 Mobile Safari/534.30
Mozilla/5.0 (Linux; U; Android 2.2; en-gb; GT-P1000 Build/FROYO) AppleWebKit/533.1 (KHTML, like Gecko) Version/4.0 Mobile Safari/533.1
```

#### Firefox

```python
Mozilla/5.0 (Windows NT 6.2; WOW64; rv:21.0) Gecko/20100101 Firefox/21.0
Mozilla/5.0 (Android; Mobile; rv:14.0) Gecko/14.0 Firefox/14.0
```

#### Google Chrome

```python
Mozilla/5.0 (Windows NT 6.2; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/27.0.1453.94 Safari/537.36
Mozilla/5.0 (Linux; Android 4.0.4; Galaxy Nexus Build/IMM76B) AppleWebKit/535.19 (KHTML, like Gecko) Chrome/18.0.1025.133 Mobile Safari/535.19
```

#### iOS

```python
Mozilla/5.0 (iPad; CPU OS 5_0 like Mac OS X) AppleWebKit/534.46 (KHTML, like Gecko) Version/5.1 Mobile/9A334 Safari/7534.48.3
Mozilla/5.0 (iPod; U; CPU like Mac OS X; en) AppleWebKit/420.1 (KHTML, like Gecko) Version/3.0 Mobile/3A101a Safari/419.3
```

### 添加方法

#### 添加UA

```python
import urllib.request
headers = ("User-Agent","Mozilla/5.0 (iPod; U; CPU like Mac OS X; en) AppleWebKit/420.1 (KHTML, like Gecko) Version/3.0 Mobile/3A101a Safari/419.3")
opener = urllib.request.build_opener()
opener.addheaders = [headers]
#将opener安装为全局，让urlopen()访问时也添加对应报头
urllib.requeset.install_opener(opener)
data1 = opener.open(url).open()
data2 = urllib.request.urlopen(url).open()
```

#### 添加ip代理

```python
import urllib.request
ip = "192.168.1.1"
proxy urllib.request.ProxyHandler({"http":ip})
opener = urllib.request.build_opener(proxy,urllib.request.HTTPHandler)
#将opener安装为全局
urllib.request.install_opener(opener)
```

### 完整代码(两者同时添加)
```python
import urllib
```

```python
#同时使用ip代理以及用户代理
import urllib.request
import random
#用户代理池和ip代理池
uapools = [
"Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/49.0.2623.221 Safari/537.36 SE 2.X MetaSr 1.0",
"Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 5.1; Trident/4.0; Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1; SV1) ; Maxthon/3.0)",
"Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 5.1; Trident/4.0; Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1; SV1) ;  QIHU 360EE)"]
ippools = ["61.135.217.7","118.114.77.47","111.224.104.161"]
url = 'http://www.baidu.com'
#添加用户代理以及ip代理
def ua(uapools,ippools):
    req = urllib.request.Request(url)
    req.add_header("User-Agent",random.choice(uapools))
    proxy = urllib.request.ProxyHandler({"https":random.choice(ippools)})
    opener = urllib.request.build_opener(proxy,urllib.request.HTTPHandler)
    #将opener安装为全局
    urllib.request.install_opener(opener)
    return req
if __name__ == '__main__':
    for i in range(20):
        req = ua(uapools,ippools)
        try:
            data = urllib.request.urlopen(req)
            print(len(data.read()))
            print(data.getcode())
        except Exception as err:
            print(err)
```
