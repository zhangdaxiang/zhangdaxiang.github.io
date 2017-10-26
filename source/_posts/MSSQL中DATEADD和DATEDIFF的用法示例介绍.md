---
title: sqlserver中DATEADD和DATEDIFF的用法示例介绍
date: 2016-12-21 14:18:23
tags: sqlserver
categories: sqlserver
---

DATEDIFF函数是用于计算两个日期之间的小时、天、周、月、年等时间的间隔。DATEADD函数通过给定时间间隔对一个日期进行加减来获得一个新的日期。
一个月的第一天
``` sql
Select DATEADD(mm, DATEDIFF(mm,0,getdate()), 0)
```
本周的星期一
``` sql
Select DATEADD(wk, DATEDIFF(wk,0,getdate()), 0)
```
一年的第一天
``` sql
Select DATEADD(yy, DATEDIFF(yy,0,getdate()), 0)
```
季度的第一天
``` sql
Select DATEADD(qq, DATEDIFF(qq,0,getdate()), 0)
```
当天的半夜
``` sql
Select DATEADD(dd, DATEDIFF(dd,0,getdate()), 0)
```
上个月的最后一天
``` sql
Select dateadd(ms,-3,DATEADD(mm, DATEDIFF(mm,0,getdate()), 0))
```
去年的最后一天
``` sql
Select dateadd(ms,-3,DATEADD(yy, DATEDIFF(yy,0,getdate()), 0))
```
本月的最后一天
``` sql
Select dateadd(ms,-3,DATEADD(mm, DATEDIFF(m,0,getdate())+1, 0))
```
本年的最后一天
``` sql
Select dateadd(ms,-3,DATEADD(yy, DATEDIFF(yy,0,getdate())+1, 0))
```
本月的第一个星期一
``` sql
select DATEADD(wk,DATEDIFF(wk,0,dateadd(dd,6datepart(day,getdate()),getdate())), 0)
```