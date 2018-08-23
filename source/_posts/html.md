---
title: html
date: 2018-06-13 22:58:22
tags: [前端,html]
categories: 前端
---
### html结构
```html
<!--告诉浏览器使用什么样的html或者xhtml来解析html文档-->
<!DOCTYPE html>	
<!--文档的开始结束标签，说明这是一个html文档-->
<html>xxx</html> 
<!--出现在文档的开头部门。标签内的东西不会在浏览器的文档窗口显示-->
<head>xxx</head> 
<!--定义网页标题，在浏览器标题栏显示-->
<title>xxx<title/>
<!--网页主体部分-->
<body>xxx</body>	
```

### 常用标签
#### <!DOCTYPE>标签
此标签可告知浏览器文档使用哪种 HTML 或 XHTML 规范。
* BackCompat：怪异模式，浏览器使用自己的怪异模式解析渲染页面。
* CSS1Compat：标准模式，浏览器使用W3C的标准解析渲染页面。
#### <head>内常用标签
##### <meta>标签
```html
<!--<meta>标签共有两个属性，分别是name和http-equiv属性，两个属性具有不同的属性值-->
<!--name属性主要用于描述网页，对应的属性值是content,content中的值是便于搜索引擎机器人查找和分类信息用的-->
<meta name="kewwords" content="meta属性，meta参数值">
<meta name="description" content="description对应的值是搜索引擎描述用的">
<!--http-equiv标签,相当于http的文件头作用，它可以向浏览器传回一些有用的信息，以帮助正确地显示网页内容-->
<!--即2秒后跳转到百度-->
<meta http-equiv="Refresh" content="2;URL=https://www.baidu.com"> 
<!--编码格式-->
<meta http-equiv="content-Type" charset="utf-8">
```
##### 非<meta>标签
```html
<title>zhangdaxiang</title>
<!--即网页的icon小图标-->
<link rel="icon" href="http://xxxxx.ico">
<!--引入css和js-->
<link rel="stylesheet" href="css.css">
<scrip src="js.js"></scrip>
```
#### <body>内常用标签
##### 块级标签和内联标签
```html
<!--<div>只是一个块级元素，并无实际的意义。主要通过CSS样式为其赋予不同的表现-->
<div>xxx</div>
<!--<span>表示了内联行(行内元素),并无实际的意义,主要通过CSS样式为其赋予不同的表现-->
<span>xxx</span>
```
所谓块元素，是以另起一行开始渲染的元素，行内元素则不需另起一行。如果单独在网页中插入这两个元素，不会对页面产生任何的影响。
这两个元素是专门为定义CSS样式而生的。
##### 其他常用的基本标签
```html
<!--n的取值范围是1-6；从大到小，分别表示不同大小标题-->
<h3>xxx</h3>
<!--段落标签，包裹的内容被换行，并且与上下内容有一行空白-->
<p>xxx</p>
<!--两个都是加粗标签，<b>标签仿佛已经被弃用了-->
<b> <strong>
<!--为文字加上一条斜线-->
<strike>
<!--文字变成斜体-->
<em>
<!--上角标和下角标-->
<sup> <sub>
<!--换行-->
<br>
<!--水平线-->
<hr>
<!--特殊字符-->
&lt;&gt;&quot;&copy;&reg;
<!--图形标签<img>-->
<img src="要显示图片的路径" alt="图片没有加载成功时的提示" title="鼠标悬浮时的提示信息" width="图片的宽" height="图片的高 (宽高两个属性只用一个会自动等比缩放)">
<!--超链接标签(锚标签)-->
<a href="" target="_blank">click</a>
<!--href属性指定目标网页地址。该地址可以有几种类型：
1.绝对URL - 指向另一个站点（比如 href="http://www.baidu.com）
2.相对URL - 指当前站点中确切的路径（href="index.html"）
3.锚URL - 指向页面中的锚（href="#id"）-->
```
##### 列表标签
```html
<!--无序列表 [type属性：disc(实心圆点)(默认)、circle(空心圆圈)、square(实心方块)]-->
<ul>
	<li>xxx</li>
	<li>xxx</li>
</ul>
<!--有序标签-->
<ol>
	<li>xxx</li>
	<li>xxx</li>
</ol>
<!--自定义标签-->
<dl>
	<dt>列表标题</dt>
	<dl>列表项</dl>
</dl>
```
##### 表格标签
```html
<!--
<tr>: table row
<th>: table head cell
<td>: table data cell
属性：
border: 表格边框；
cellpadding: 内边距；
cellspacing: 外边距；
width: 像素，百分比(最好通过css来设置)
rowspan: 单元格横跨多少行
colspan: 单元格横跨多少列(即合并单元格)
-->
<table>
	<tr>
		<th>标题</th>
		<th>标题</th>
	</tr>
	<tr>
		<td>内容</td>
		<td>内容</td>
	</tr>
</table>
```
#### 表单标签<form>
表单用于向服务器传输数据，从而实现用户与Web服务器的交互。
表单属性：
1.action:表单提交到哪,一般指向服务器端一个程序，比如https://www.baidu.com/web
2.methon:表单提交方式,有post和get,默认就是get
表单元素是允许用户在表单中输入内容,比如：文本域(textarea)、下拉列表、单选框(radio-buttons)、复选框(checkboxes)等等。

```html
<form>
input 元素
<!--input标签的name和values是传输给服务器的键值对-->
</form>
```
##### 文本域
```html
<!--文本域通过<input type="text">标签来设定，在表单中输入字母数字等-->
<form action="https://www.baidu.com/web" method="get">
	FirstName:<input type="text" name="firstname">
	FamilyName:<input type="texxt" name="familyname">
</from>
<form>
	<textarea rows="10" cols="30">这是一个文本框</textarea>
</form>
```
##### 字段密码
```html
<!--密码字段通过标签<input type="password"> 来定义,密码字段不会明文显示，会以星号或者远点代替-->
<form>
	Password:<input type="password" name="password">
</form>
```
##### 单选按钮
```html
<!--<input type="radio"> 标签定义了表单单选框选项,单选按钮的两个name是一致的，values是不一致的-->
<form>
	<input type="radio" name="sex" value="male">Male<br>
	<input type="radio" name="sex" value="female">Female
</form>
```
##### 复选框
```html
<!--<input type="checkbox"> 定义了复选框. 需要从若干给定的选择中选取一个或若干选项-->
<form>
	<input type="checkbox" name="vehicle" value="bike">I have a bike<br>
	<input type="checkbox" name="vehicle" value="car">I have a car
</form>
```
##### 提交按钮
```html
<!--<input type="submit">定义了提交按钮-->
<!--当用户单击确认按钮时，表单的内容会被传送到另一个文件。表单的动作属性定义了目的文件的文件名。由动作属性定义的这个文件通常会对接收到的输入数据进行相关的处理-->
<form name="input" action="www.baidu.com" methon="get">
	username:<input type="text" name="username"><br>
	password:<input type="password" name="password">
	<input type="submit" value="Submit">
</form>
```
##### <select>下拉标签属性
```html
<form>
	<select name="cars">
		<option value="volvo">volvo</option>
		<option value="audi">audi</option>
		<option value="fiat">fiat</option>
 		<option value="saab">saab</option>
	</select>
</form>
```