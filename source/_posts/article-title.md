title: python爬虫同时添加用户代理和ip代理
---
<pre><code>
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
</code></pre>
