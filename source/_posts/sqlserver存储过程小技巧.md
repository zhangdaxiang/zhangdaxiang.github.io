---
title: sqlserver存储过程小技巧
date: 2017-12-07 16:15:09
tags: sqlserver
categories: sqlserver
---

### 数据写入表
今天碰到一个问题，执行一个存储过程时
```sql
EXEC proc_orderday
```
返回了一系列的数据，但是并不能对这些数据进行处理,我们想要对这些数据进行操作的话，就要写把他们写入一个表。
需要注意的是，一定要先建表，才能写入，直接`select * into`是不行的。
```SQL
#先建一个临时表
CREATE TABLE #tmp
(
	...
)
#写入存储过程数据
INSERT INTO #tmp
EXEC proc_orderday
```