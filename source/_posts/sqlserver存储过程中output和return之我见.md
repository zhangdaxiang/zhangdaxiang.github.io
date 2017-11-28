---
title: sqlserver存储过程中output和return之我见
date: 2017-03-28 11:13:04
tags: sqlserver
categories: sqlserver
---
今天老大把我叫去，和我说以后把数据库开发的一部分工作交给我。之前做的都是一些数据提取统计之类的活，虽然也写过几个存储过程，也大多数是提取类的。其中也用到了`output`这样的参数，但是之前没有好好地看过原理，只是根据之前已有的存储过程生搬硬套，总感觉对于`output`和`return`的理解不够。所以，今天就来总结一下这两个的用法。
### 四种参数
1.`input` 这个参数只是将信息从应用程序传输到存储过程。
2.`inputoutput`这个参数可以将信息从应用程序传输到存储过程，并可以将信息传输回应用程序。
3.`output`这个参数可以只可以将信息从存储过程传输回应用程序。
4.`returnvalue` 这个参数表示存储过程的返回值。存储过程参数列表中不显示该参数。它只和存储过程的 `return` 语句中的值相关联。
### 参数实例
#### 不带输入参数
```sql
IF OBJECT_ID('user_info') IS NOT NULL 
	DROP PROC user_info 
--GO起到了分割sql脚本的作用，上下语句可以视为两个脚本
GO
CREATE PROC user_info
AS
SET NOCOUNT ON
DECLARE @name VARCHAR(10)
BEGIN 
	SELECT @name = name FROM userinfo
END 
PRINT @name
SET NOCOUNT OFF
--执行存储过程
EXEC user_info 
```
#### 带输入参数
```sql
IF OBJECT_ID('user_info') IS NOT NULL 
	DROP PROC user_info 
--GO起到了分割sql脚本的作用，上下语句可以视为两个脚本
GO
CREATE PROC user_info
(
	@id INT
)
AS
SET NOCOUNT ON
DECLARE @name VARCHAR(10)
BEGIN 
	SELECT @name = name FROM userinfo WHERE id = @id
END 
PRINT @name
SET NOCOUNT OFF
--执行存储过程
EXEC user_info @id = 1
```
#### 带return参数
这里涉及到一个存储过程返回值的概念，和用`select`输出一个结果集不同的是，`return`返回的是一个数字。如果存储过程中有`return`，则返回`return`出来的值。如果没有`return`的话，则返回0。
这样子是返回作用的行数，即全局变量`@@ROWCOUNT`：
```sql
IF OBJECT_ID('user_info') IS NOT NULL 
	DROP PROC user_info 
--GO起到了分割sql脚本的作用，上下语句可以视为两个脚本
GO
CREATE PROC user_info
AS
SET NOCOUNT ON
DECLARE @name VARCHAR(10)
BEGIN 
	SELECT @name = name FROM userinfo
END 
RETURN @@ROWCOUNT
SET NOCOUNT OFF
--执行存储过程
DECLARE @result INT
EXEC @result = user_info 
SELECT @result
```
这样的话就是没有`return`关键字，其实就是`return 0`：
```sql
IF OBJECT_ID('user_info') IS NOT NULL 
	DROP PROC user_info 
--GO起到了分割sql脚本的作用，上下语句可以视为两个脚本
GO
CREATE PROC user_info
AS
SET NOCOUNT ON
DECLARE @name VARCHAR(10)
BEGIN 
	SELECT @name = name FROM userinfo
END 
--RETURN @@ROWCOUNT
SET NOCOUNT OFF
--执行存储过程
DECLARE @result INT
EXEC @result = user_info 
SELECT @result
```
注意的是，这里的`@@ROWCOUNT`虽然现实影响的行数是3，但是`@name`只是最近的一个。
#### 带有输出参数
```sql
IF OBJECT_ID('user_info') IS NOT NULL 
	DROP PROC user_info 
--GO起到了分割sql脚本的作用，上下语句可以视为两个脚本
GO
CREATE PROC user_info
(
	@id INT
	,@name VARCHAR(10)='' OUTPUT
)
AS
SET NOCOUNT ON
BEGIN 
	SELECT @name = name FROM userinfo WHERE id = @id
END 
SET NOCOUNT OFF
--执行存储过程
DECLARE @name VARCHAR(10)
--要注意的是，这里要重新declare一次OUTPUT参数@name,而且如果使用了@id=3这样的调用写法，OUTPUT参数也要用@name = value这样的写法。
--第一种写法
EXEC user_info @id = 3,@name = @name OUTPUT
--第二种写法
EXEC user_info 3,@name OUTPUT
SELECT @name
```
#### 同时带return参数和output参数
```sql
IF OBJECT_ID('user_info') IS NOT NULL 
	DROP PROC user_info 
--GO起到了分割sql脚本的作用，上下语句可以视为两个脚本
GO
CREATE PROC user_info
(
	@id INT
	,@name VARCHAR(10) OUTPUT 
)
AS
SET NOCOUNT ON
DECLARE @age INT 
BEGIN 
	SELECT @name = name,@age = age FROM userinfo WHERE id = @id
	RETURN @age
END 
SET NOCOUNT OFF
--执行存储过程
DECLARE @name VARCHAR(10)
DECLARE @age INT 
EXEC @age = user_info @id = 1,@name = @name OUTPUT 
--当然这也写也是可以的，就像之前提到的
EXEC @age = user_info 1,@name OUTPUT 
SELECT @name,@age
```
