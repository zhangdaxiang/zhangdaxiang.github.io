---
title: openvpn 连接提示CreateFile failed on TAP device
date: 2016-12-14 12:38:54
tags: PC
categories: PC
---

今天一大早有同事反映VPN连接不上，老大就让我去检查一下，我一个小虾米刚用VPN还不到1个月，着实有些为难啊，只能硬着头皮上了。

打开log一看，发现报错`CreateFile failed on TAP device`，遂百度，成功得到解决方法。

记录一下

1.关闭OpenVPN客户端
2.进入OpenVPN安装目录\bin
3.运行deltapall.bat，并按提示执行完成
4.运行addtap.bat，并按提示执行完成
5.重新运行OpenVPN客户端，完成连接即可