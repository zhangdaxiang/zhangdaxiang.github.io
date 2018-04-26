---
title: sqlserver分区表
date: 2018-01-15 14:36:24
tags: sqlserver
categorise: sqlserver
---
### 分区表的定义
一般情况下，我们建立一个数据库的表，表里面的数据都会放在同一个文件里面。但是如果是分区表的话，表数据会根据我们自定义的规则给存放到不同的文件中。这样其实就相当于将一个大的数据文件拆分为多个小文件，这样文件的大小随着拆分而减小，可以得到硬件系统方面的假期。而且可以将这些小文件放在不同磁盘下的多个CPU来进行处理，大大提升数据的性能。
概括一下，分区表的优势主要体现在：<ul><li>提供性能：这个是大多人数分区的目的，把一个表分部到不同的硬盘或其他存储介质中，会大大提升查询速度。</li><li>提高稳定性：当一个分区出了问题，不会影响其他分区，仅仅是当前坏的分区不可用。</li><li>便于管理：把一个大表分成若干个小表，则备份和恢复的时候不再需要备份整个表，可以单独备份分区。</li><li>存档：将一些不太常用的数据，单独存放。如：将1年前的数据记录分到一个专门的存档服务器存放。</li></ul>但是这样仅限于大数据量的数据表，对于小数据量的表。这样还会产生不必要的开销，得不偿失。
### 创建文件组
分区是要把一个表数据拆分为若干子集合，也就是把把一个数据文件拆分到多个数据文件中，然而这些文件的存放可以依托一个文件组或者多个文件组，由于多个文件组可以提高数据库的访问并发量，还可以把不同的分区配置到不同的磁盘中提高效率，所以创建时建议分区跟文件组个数相同。
#### 创建文件组
创建文件组的语法如下：
```sql
ALTER DATABASE <数据库名> ADD filegroup <文件组名>
```
```sql
---创建数据库文件组
alter database testDB add filegroup ByBirGroup1
alter database testDB add filegroup ByBirGroup2
alter database testDB add filegroup ByBirGroup3
```
#### 创建数据文件到文件组里面
创建数据文件到文件组的语法如下：
```sql
ALTER DATABASE <数据库名称> ADD FILE <数据标识> TO FILEGROUP <文件组名称>
---<数据标识> （name:文件名，fliename:物理路径文件名，size:文件初始大小kb/mb/gb/tb，filegrowth:文件自动增量kb/mb/gb/tb/%,maxsize:文件可以增加到的最大大小kb/mb/gb/tb/unlimited）
```
```sql
alter database testDB add file 
(name=N'ByBirGroup1',filename=N'D:\Program Files\Microsoft SQL Server\MSSQL11.MSSQLSERVER\MSSQL\DATA\testDB1.mdf',size=5Mb,filegrowth=1mb)
to filegroup ByBirGroup1
alter database testDB add file 
(name=N'ByBirGroup2',filename=N'D:\Program Files\Microsoft SQL Server\MSSQL11.MSSQLSERVER\MSSQL\DATA\testDB2.mdf',size=5Mb,filegrowth=1mb)
to filegroup ByBirGroup2
alter database testDB add file 
(name=N'ByBirGroup3',filename=N'D:\Program Files\Microsoft SQL Server\MSSQL11.MSSQLSERVER\MSSQL\DATA\testDB3.mdf',size=5Mb,filegrowth=1mb)
to filegroup ByBirGroup3
```
查询当前的所有文件组
```sql
SELECT name,type_desc,physical_name,state_desc,size,growth
FROM sys.database_files
```
可以看到，成了。
<img src="/img/sqlserver_分区表/sqlserver_fenqu6.jpg" alt="fenqu6" width=800 height=800>
### 分区表的操作步骤
分区表分为三部分：分区函数，分区框架和分区表。他们之间的关系是：<strong>分区表依赖于分区构架，分区构架又依赖分区函数。</strong>所以定义分区表的顺序是定义分区函数->定义分区构架->定义分区表。我们实际操作的时候，需要有一张需要分区的表：
<img src="/img/sqlserver_分区表/sqlserver_fenqu1.jpg" alt="fenqu1"  width = 400 hight =400>
#### 定义分区函数
分区函数的作用是判断一行数据属于哪一个分区，通过在分区函数中设定边界值来使得根据行中特殊列的值来确定分区，比如上面的分区表：
假如选择的是`id`列，那就是`int`类型，那么分区可以指定为1-100w是一个分区，100-200w是一个分区。
假如选择的是`birthday`列，那就是`datetime`类型可以指定为`2014-01-01`至`2017-01-01`一个分区，`2017-01-01`以后一个分区。放一张`CareySon`大佬的图，方便理解。
<img src="/img/sqlserver_分区表/sqlserver_fenqu2.jpg" alt=""fenqu2  width = 400 hight =600>
定义分区函数的语法如下：
```sql
CREATE PARTITION FUNCTION partition_function_name ( input_parameter_type )
AS RANGE [ LEFT | RIGHT ] 
FOR VALUES ( [ boundary_value [ ,...n ] ] ) 
[ ; ]
```
可以看到，定义函数的语法中，并没有涉及到具体的表，这是因为分区函数并不和具体的表相关联。其中的`Range left`和`right`决定这个边界值应该归属于左边还是右边。
<img src="/img/sqlserver_分区表/sqlserver_fenqu3.jpg" alt="fenqu3" width=400 height=600>
创建分区函数:
```sql
---创建分区函数
CREATE PARTITION FUNCTION Fun_test_zyz_birthday(DATETIME)
AS RANGE LEFT 
FOR VALUES('2004-01-01','2007-01-01')
--查看分区函数是否创建成功
SELECT * FROM sys.partition_functions
```
查询结果如图所示：
<img src="/img/sqlserver_分区表/sqlserver_fenqu4.jpg" alt="fenqu4" width=1000 height=1000>
#### 定义分区框架
分区函数决定了数据该分到哪个分区，而分配每个分区属于哪个文件组，则需要依赖分区框架。
定义分区框架的语法如下：
```sql
CREATE PARTITION SCHEME partition_scheme_name
AS PARTITION partition_function_name
[ ALL ] TO ( { file_group_name | [ PRIMARY ] } [ ,...n ] )
[ ; ]
```
<img src="/img/sqlserver_分区表/sqlserver_fenqu5.jpg" alt="fenqu5" width=400 height=600>
根据之前的分区函数和文件组，可以得到：
```sql
--基于之前的分区函数创建分区构架schema
CREATE PARTITION SCHEME SchemaForParirion
AS PARTITION Fun_test_zyz_birthday    --这个是之前创建的分区函数
TO(ByBirGroup1,ByBirGroup2,ByBirGroup3)   --FileGroup1是自己添加的文件组，因为有两个分界值，3个分区，所以要指定3个文件组，也可以使用ALL所谓的分区指向一个文件组
--查看已创建的分区构架
SELECT * FROM sys.partition_schemes
```
### 定义分区表
需要知道一点的是，一个表在创建的时候就要决定是否是分区表了，虽然大多数情况下，我们是因为一个表的数据过大才决定分区的。
所以，在创建表的时候确立为分区表
```sql
CREATE TABLE [dbo].[test_zyz](
	[id] [INT]  NOT NULL,
	[name] [VARCHAR](50) NULL,
	[age] [INT] NULL,
	[birthday] [DATETIME]  PRIMARY KEY NOT NULL)
ON SchemaForParirion([birthday]) --SchemaForPartition是刚刚定义的分区架构，括号内为指定的分区列
```
插入测试数据
<img src="/img/sqlserver_分区表/sqlserver_fenqu7.jpg" alt="fenqu7" width=400 height=400>
可以通过下列语句来查看分区状况：
```sql
select convert(varchar(50), ps.name) as partition_scheme,
p.partition_number, 
convert(varchar(20), ds2.name) as filegroup, 
convert(varchar(20), isnull(v.value, ''), 120) as range_boundary, 
str(p.rows, 9) as rows
from sys.indexes i 
INNER JOIN sys.partition_schemes ps on i.data_space_id = ps.data_space_id 
INNER JOIN sys.destination_data_spaces dds
on ps.data_space_id = dds.partition_scheme_id 
INNER JOIN sys.data_spaces ds2 on dds.data_space_id = ds2.data_space_id 
INNER JOIN sys.partitions p on dds.destination_id = p.partition_number
and p.object_id = i.object_id and p.index_id = i.index_id 
INNER JOIN sys.partition_functions pf on ps.function_id = pf.function_id 
LEFT JOIN sys.Partition_Range_values v on pf.function_id = v.function_id
and v.boundary_id = p.partition_number - pf.boundary_value_on_right 
WHERE i.object_id = object_id('test_zyz')    --此处是表名
and i.index_id in (0, 1) 
order by p.partition_number
```
可以看到各分区的具体数量
<img src="/img/sqlserver_分区表/sqlserver_fenqu8.jpg" alt="fenqu8" width=600 height=600>
查询某个分区的语法是：
```sql
SELECT * FROM test_zyz
WHERE $partition.Fun_test_zyz_birthday(birthday)=2 ---$partition.分区函数(分区列)
```
### 分区表的分割与合并
### 分区表的分割
分区表的分割就相当于新建一个分区，将原有的分区内需要被分割的内容插入到新建的分区中，然后删除这部分内容。
新加入一个分割点：`2009-01-01`。如图所示：
<img src="/img/sqlserver_分区表/sqlserver_fenqu9.jpg" alt="fenqu9" width=400 height=600>
对于上图的操作，分割时候，被分割的分区3内的内容复制到分区4，然后删除分区3中对于的部分。<strong>但是这种操作非常消耗IO，并且会在分割的过程中锁定分区3的内容，而且这个操作所产生的日志是被转移数据的4倍。</strong>所以最后在建表的时候就考虑到以后的分割点。
分区分割分为两个步骤：<ul><li>首先要告诉SQL Server新建立的分区放到哪个文件组。</li><li>建立新的分割点。</li></ul>现在增加一条测试数据：
```sql
INSERT INTO test_zyz(name,age,birthday)
VALUES('xiaoming5',20,'2011-01-01 00:00:00.000')
```
执行长查询，得到：
<img src="/img/sqlserver_分区表/sqlserver_fenqu10.jpg" alt="fenqu10" >
接下来可以执行分割操作：
```sql
--分割出来的分区数据存在在哪个文件组
ALTER PARTITION SCHEME SchemaForParirion NEXT USED 'ByBirGroup2'
--添加分割点
ALTER PARTITION FUNCTION Fun_test_zyz_birthday()
SPLIT RANGE('2009-01-01')
```
再次查询可以得到：
<img src="/img/sqlserver_分区表/sqlserver_fenqu11.jpg" alt="fenqu11" >
#### 分区表的合并
分区表的合并可以看做是分割的逆操作，合并时必须提供分割点，且分割点存在，不然会报错：
```sql
--提供分割点，合并分区
ALTER PARTITION FUNCTION Fun_test_zyz_birthday()
MERGE RANGE('2009-01-01')
```
### 分区表数据迁移
#### 分区表一个分区的数据迁移到普通表
从分区到普通表，需要满足以下条件：<ul><li>普通表必须建立在分区表切换出的分区所在的文件组上。</li><li>普通表的表结构和分区表一致。</li><li>普通表上的索引要和分区表一致，包括聚集索引和非聚集索引。</li><li>普通表必须是空表。</li></ul>
首先，创建和`test_zyz`结构相同的普通表：
```sql
CREATE TABLE [dbo].[test_zyz2](
	[id] [INT] IDENTITY(1,1) NOT NULL,
	[name] [VARCHAR](50) NULL,
	[age] [INT] NULL,
	[birthday] [DATETIME] NOT NULL,
 CONSTRAINT [PK_test_zyz2] PRIMARY KEY NONCLUSTERED 
(
	[birthday] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [ByBirGroup2]
) ON [ByBirGroup2] ---表格建在分区2上，这样我们就能迁移分区2的数据
```
然后从分区表中复制数据到普通表：
```sql
--将test_zyz分区表中的第2分区数据复制到普通表中
alter table testdb..test_zyz switch partition 2 to testdb..test_zyz2
```
可以看到，分区2的数据已经没有了,迁移到了`test_zyz2`表中
<img src = "/img/sqlserver_分区表/sqlserver_fenqu12.jpg" alt = "fenqu12">
但是要注意的是，从普通表`test_zyz2`中还原数据到`test_zyz`这个分区表分区2的时候，还要新增另外一个条件：普通表必须加上和分区数据范围一致的 check 约束条件。
```sql
ALTER TABLE testdb..test_zyz2 ADD CONSTRAINT CK_SalesOrders_Birthday
CHECK(birthday>'2004-01-01' AND birthday<='2007-01-01') ---之前分区函数是left，所以应该是<='2007-01-01'
```
成功建立`check`约束条件以后，就可以还原数据了：
```sql
--将普通表中的数据复制到test_zyz分区表中的第2分区
alter table testdb..test_zyz2 switch to testdb..test_zyz partition 2
```
### 结语
分区表分区切换并没有真正去移动数据,而是`SQL Server`在系统底层改变了表的元数据。因此分区表分区切换是高效、快速、灵活的。利用分区表的分区切换功能，我们可以快速加载数据到分区表、卸载分区数据到普通表，然后`TRUNCATE`普通表，以实现快速删除分区表数据，也可以快速迁移分区到历史表。
本文学习自<a href="http://www.cnblogs.com/CareySon/archive/2011/12/30/2307766.html">理解SQL SERVER中的分区表</a>，感谢宋沄剑大大。