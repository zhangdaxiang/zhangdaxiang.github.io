---
title: sqlserver float 转 varchar 防止科学计数法
date: 2016-12-20 12:44:34
tags: sqlserver
categories: sqlserver
---
今天给同事处理数据需求的时候，发现了一个问题，直接将Float转化成Varchar，生成的字符串是科学计数法。研究了一下发现，用Cast转换两次即可。
Float--->>Decimal--->>Varchar
``` sql
select cast(cast(字段 as decimal(18,0)) as varchar)
```
哇咔咔