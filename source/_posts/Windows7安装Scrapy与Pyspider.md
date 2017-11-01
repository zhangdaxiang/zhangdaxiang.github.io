---
title: Windows7安装Scrapy与Pyspider
date: 2017-10-26 16:32:18
tags: python
categories: python
---

说的python上面的爬虫框架，最有名的当然是Scrapy了，但是感觉他对windows操作系统不太友好，安装的时候各种报错。这个也加深了我好好学习linux的决心。经过多次尝试，总结一下windows上安装Scrapy和Pyspider的步骤。

Scrapy要求Twisted.whl，Pyspider要求lxml.whl

### 更新pip（可选）
``` bash
python -m pip install -U pip
```
### 安装wheel
进入python目录下的scripts目录
``` bash
cd D:\\Python35\Scripts
```
安装wheel
``` bash
pip install wheel
```

### 下载whl
找到并下载lxml与Twisted两个包。

### 安装whl
安装.whl
``` bash
pip install lxml-3.6.4-cp35-cp35m-win_amd64.whl
pip install Twisted-16.6.0-cp35-cp35m-win_amd64.whl
```

### 安装Scrapy、PySpider
``` bash
pip install scrapy
pip install pyspider
```