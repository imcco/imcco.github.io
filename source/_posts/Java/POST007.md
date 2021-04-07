---
title: Java成神之路-数据库、PowerDesigner、JDBC（七）
tags: Java
category: Java
date: 2018-02-23 15:44:36
---
![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0132.jpg)

Java成神之路-数据库、PowerDesigner、JDBC
<!--more-->
# Mysql数据库

## 常用命令 


```SQL
net start mysql //　　启动MYSQL服务
net stop mysql  //　　停止MYSQL服务 
netstat –na | findstr 3306 //查看被监听的端口 , findstr用于查找后面的端口是否存在 
mysql –user=root –password=123456 db_name //在命令行中登陆MYSQL控制台 , 即使用 MYSQL COMMEND LINE TOOL 
mysql –u root –p123456 db_name //在命令行中登陆MYSQL控制台
// 进入MYSQL命令行工具后 , 使用status; 或\s 查看运行环境信息 

use new_dbname; //　切换连接数据库的语法
show databases; //　显示所有数据库
show tables;    //　显示数据库中的所有表
show create table table_name; 　//显示某个表创建时的全部信息　 
Describe table_name;   desc table_name; //查看表的具体属性信息及表中各字段的描述 
create database name; //创建数据库 
use databasename; //选择数据库 
drop database name //直接删除数据库，不提醒 
mysqladmin drop databasename //删除数据库前，有提示。 
show tables; //显示表 
describe tablename; //表的详细描述

select version(),current_date;  //显示当前mysql版本和当前日期   
SELECT DISTINCT `name` from users; //select 中加上distinct去除重复字段 
SELECT DISTINCT name,age FROM users; //select 中加上distinct去除重复字段 
SELECT count(DISTINCT name,age) FROM users; //select 中加上distinct去除重复字段
select * from table_name; //查询:查询所有数据
select 字段1 , 字段2 from table_name; //查询指定字段的数据
select count(*) from tablename; //Mysql命令查询一个表的记录总数
select count(*) as num from tablename; //Mysql命令查询一个表的记录总数
select count(*) as total from tablename; //Mysql命令查询一个表的记录总数

alter table t1 rename t2; //重新命名表
Create database db_name;  //数据库创建
Drop database db_name; //数据库删除 
drop database if exits db_name //删除时先判断是否存在

create table table_name (字段1 数据类型 , 字段2 数据类型); //建表 : 创建数据表的语法
create table mytable (id int , username char(20)); //建表示例
CREATE USER username1 identified BY ‘password’ , username2 IDENTIFIED BY ‘password’…. //创建数据库用户

drop table table_name;  //删表
Insert into 表名 [(字段1 , 字段2 , ….)] values (值1 , 值2 , …..); //添加数据 
insert into mytable (id,username) values (1,’zhangsan’); //　如果向表中的每个字段都插入一个值,那么前面 [ ] 括号内字段名可写也可不写
Update table_name set 字段名=’新值’ [, 字段2 =’新值’ , …..][where id=id_num] [order by 字段 顺序]

delete from table_name; //删除整个表中的信息
delete from table_name where 条件语句 ; 条件语句如 : id=3; //删除表中指定条件的语句 


Grant all ON db_name.table_name TO user_name [ indentified by ‘password’ ]; //库，表级的权限控制,将某库中的某表的控制权赋予某用户

alter table table_name add column (字段名 字段类型); //增加一个字段格式----此方法带括号 
alter table table_name add column 字段名 字段类型 after 某字段；//指定字段插入的位置 
alter table table_name drop字段名; //删除一个字段
alter table table_name change 旧字段名 新字段名 新字段的类型; //修改字段名称/类型
alter table table_name rename to new_table_name; //改表的名字
ALTER TABLE table_name ENGINE=MyISAM | InnoDB;  //修改数据库引擎 
truncate table table_name; //清空表中的所有数据 此方法也会使表中的取号器(ID)从1开始 
SHOW CREATE TABLE table_name; //查看数据库当前引擎

```

## 修改密码 

```SQL
shell>mysql -u root -p 
mysql> update user set password=password(”xueok654123″) where user=’root’; 
mysql> flush privileges //刷新数据库 
mysql>use dbname； //打开数据库： 
mysql>show databases; //显示所有数据库 
mysql>show tables; //显示数据库mysql中所有的表：先use mysql；然后 
mysql>describe user; //显示表mysql数据库中user表的列信息）；
```

## 授权 
创建一个从任何地方连接服务器的超级用户，使用一个口令something

```sql
mysql> grant all privileges on *.* to user@localhost identified by ’something’ with
```

增加新用户 
格式：grant select on 数据库.* to 用户名@登录主机 identified by “密码” 

```sql
GRANT ALL PRIVILEGES ON *.* TO monty@localhost IDENTIFIED BY ’something’ WITH GRANT OPTION; 
GRANT ALL PRIVILEGES ON *.* TO monty@”%” IDENTIFIED BY ’something’ WITH GRANT OPTION;
```

删除授权： 

```sql
mysql> revoke all privileges on *.* from root@”%”; 
mysql> delete from user where user=”root” and host=”%”; 
mysql> flush privileges;
```

创建一个用户custom在特定客户端it363.com登录，可访问特定数据库fangchandb 

```sql
mysql >grant select, insert, update, delete, create,drop on fangchandb.* to custom@ it363.com identified by ‘ passwd’
```


## 备份 
备份数据库 

```
shell> mysqldump -h host -u root -p dbname >dbname_backup.sql
```

恢复数据库 

```
shell> mysqladmin -h myhost -u root -p create dbname 
shell> mysqldump -h host -u root -p dbname < dbname_backup.sql
```

如果只想卸出建表指令，则命令如下： 

```sql
shell> mysqladmin -u root -p -d databasename > a.sql
```

如果只想卸出插入数据的sql命令，而不需要建表命令，则命令如下： 

```sql
shell> mysqladmin -u root -p -t databasename > a.sql
```

## 基础
```sql
1、说明：创建数据库
CREATE DATABASE database-name
2、说明：删除数据库
drop database dbname
3、说明：备份sql server
--- 创建 备份数据的 device
USE master
EXEC sp_addumpdevice 'disk', 'testBack', 'c:\mssql7backup\MyNwind_1.dat'
--- 开始 备份
BACKUP DATABASE pubs TO testBack
4、说明：创建新表
create table tabname(col1 type1 [not null] [primary key],col2 type2 [not null],..)
根据已有的表创建新表：
A：create table tab_new like tab_old (使用旧表创建新表)
B：create table tab_new as select col1,col2… from tab_old definition only
5、说明：删除新表
drop table tabname
6、说明：增加一个列
Alter table tabname add column col type
注：列增加后将不能删除。DB2中列加上后数据类型也不能改变，唯一能改变的是增加varchar类型的长度。
7、说明：添加主键： Alter table tabname add primary key(col)
说明：删除主键： Alter table tabname drop primary key(col)
8、说明：创建索引：create [unique] index idxname on tabname(col….)
删除索引：drop index idxname
注：索引是不可更改的，想更改必须删除重新建。
9、说明：创建视图：create view viewname as select statement
删除视图：drop view viewname
10、说明：几个简单的基本的sql语句
选择：select * from table1 where 范围
插入：insert into table1(field1,field2) values(value1,value2)
删除：delete from table1 where 范围
更新：update table1 set field1=value1 where 范围
查找：select * from table1 where field1 like ’%value1%’ ---like的语法很精妙，查资料!
排序：select * from table1 order by field1,field2 [desc]
总数：select count as totalcount from table1
求和：select sum(field1) as sumvalue from table1
平均：select avg(field1) as avgvalue from table1
最大：select max(field1) as maxvalue from table1
最小：select min(field1) as minvalue from table1
11、说明：几个高级查询运算词
A： UNION 运算符
UNION 运算符通过组合其他两个结果表（例如 TABLE1 和 TABLE2）并消去表中任何重复行而派生出一个结果表。当 ALL 随 UNION 一起使用时（即 UNION ALL），不消除重复行。
两种情况下，派生表的每一行不是来自 TABLE1 就是来自 TABLE2。
B： EXCEPT 运算符
EXCEPT 运算符通过包括所有在 TABLE1 中但不在 TABLE2 中的行并消除所有重复行而派生出一个结果表。当 ALL 随 EXCEPT 一起使用时 (EXCEPT ALL)，不消除重复行。
C： INTERSECT 运算符
INTERSECT 运算符通过只包括 TABLE1 和 TABLE2 中都有的行并消除所有重复行而派生出一个结果表。当 ALL 随 INTERSECT 一起使用时 (INTERSECT ALL)，不消除重复行。
注：使用运算词的几个查询结果行必须是一致的。
12、说明：使用外连接
A、left （outer） join：
左外连接（左连接）：结果集几包括连接表的匹配行，也包括左连接表的所有行。
SQL: select a.a, a.b, a.c, b.c, b.d, b.f from a LEFT OUT JOIN b ON a.a = b.c
B：right （outer） join:
右外连接(右连接)：结果集既包括连接表的匹配连接行，也包括右连接表的所有行。
C：full/cross （outer） join：
全外连接：不仅包括符号连接表的匹配行，还包括两个连接表中的所有记录。
12、分组:Group by:
  一张表，一旦分组完成后，查询后只能得到组相关的信息。
 组相关的信息：（统计信息） count,sum,max,min,avg  分组的标准)
    在SQLServer中分组时：不能以text,ntext,image类型的字段作为分组依据
 在selecte统计函数中的字段，不能和普通的字段放在一起；
13、对数据库进行操作：
 分离数据库： sp_detach_db; 附加数据库：sp_attach_db 后接表明，附加需要完整的路径名
14.如何修改数据库的名称:
sp_renamedb 'old_name', 'new_name' 

```

## 提升
```sql

1、说明：复制表(只复制结构,源表名：a 新表名：b) (Access可用)
法一：select * into b from a where 1<>1（仅用于SQlServer）
法二：select top 0 * into b from a
2、说明：拷贝表(拷贝数据,源表名：a 目标表名：b) (Access可用)
insert into b(a, b, c) select d,e,f from b;
3、说明：跨数据库之间表的拷贝(具体数据使用绝对路径) (Access可用)
insert into b(a, b, c) select d,e,f from b in ‘具体数据库’ where 条件
例子：..from b in '"&Server.MapPath(".")&"\data.mdb" &"' where..
4、说明：子查询(表名1：a 表名2：b)
select a,b,c from a where a IN (select d from b ) 或者: select a,b,c from a where a IN (1,2,3)
5、说明：显示文章、提交人和最后回复时间
select a.title,a.username,b.adddate from table a,(select max(adddate) adddate from table where table.title=a.title) b
6、说明：外连接查询(表名1：a 表名2：b)
select a.a, a.b, a.c, b.c, b.d, b.f from a LEFT OUT JOIN b ON a.a = b.c
7、说明：在线视图查询(表名1：a )
select * from (SELECT a,b,c FROM a) T where t.a > 1;
8、说明：between的用法,between限制查询数据范围时包括了边界值,not between不包括
select * from table1 where time between time1 and time2
select a,b,c, from table1 where a not between 数值1 and 数值2
9、说明：in 的使用方法
select * from table1 where a [not] in (‘值1’,’值2’,’值4’,’值6’)
10、说明：两张关联表，删除主表中已经在副表中没有的信息
delete from table1 where not exists ( select * from table2 where table1.field1=table2.field1 )
11、说明：四表联查问题：
select * from a left inner join b on a.a=b.b right inner join c on a.a=c.c inner join d on a.a=d.d where .....
12、说明：日程安排提前五分钟提醒
SQL: select * from 日程安排 where datediff('minute',f开始时间,getdate())>5
13、说明：一条sql 语句搞定数据库分页
select top 10 b.* from (select top 20 主键字段,排序字段 from 表名 order by 排序字段 desc) a,表名 b where b.主键字段 = a.主键字段 order by a.排序字段
具体实现：
关于数据库分页：
  declare @start int,@end int
  @sql  nvarchar(600)
  set @sql=’select top’+str(@end-@start+1)+’+from T where rid not in(select top’+str(@str-1)+’Rid from T where Rid>-1)’
  exec sp_executesql @sql
 
注意：在top后不能直接跟一个变量，所以在实际应用中只有这样的进行特殊的处理。Rid为一个标识列，如果top后还有具体的字段，这样做是非常有好处的。因为这样可以避免 
top的字段如果是逻辑索引的，查询的结果后实际表中的不一致（逻辑索引中的数据有可能和数据表中的不一致，而查询时如果处在索引则首先查询索引）
14、说明：前10条记录
select top 10 * form table1 where 范围
15、说明：选择在每一组b值相同的数据中对应的a最大的记录的所有信息(类似这样的用法可以用于论坛每月排行榜,每月热销产品分析,按科目成绩排名,等等.)
select a,b,c from tablename ta where a=(select max(a) from tablename tb where tb.b=ta.b)
16、说明：包括所有在 TableA 中但不在 TableB和TableC 中的行并消除所有重复行而派生出一个结果表
(select a from tableA ) except (select a from tableB) except (select a from tableC)
17、说明：随机取出10条数据
select top 10 * from tablename order by newid()
18、说明：随机选择记录
select newid()
19、说明：删除重复记录
1),delete from tablename where id not in (select max(id) from tablename group by col1,col2,...)
2),select distinct * into temp from tablename
  delete from tablename
  insert into tablename select * from temp
评价： 这种操作牵连大量的数据的移动，这种做法不适合大容量但数据操作
3),例如：在一个外部表中导入数据，由于某些原因第一次只导入了一部分，但很难判断具体位置，这样只有在下一次全部导入，这样也就产生好多重复的字段，怎样删除重复字段
alter table tablename
--添加一个自增列
add  column_b int identity(1,1)
 delete from tablename where column_b not in(
select max(column_b)  from tablename group by column1,column2,...)
alter table tablename drop column column_b
20、说明：列出数据库里所有的表名
select name from sysobjects where type='U' // U代表用户
21、说明：列出表里的所有的列名
select name from syscolumns where id=object_id('TableName')
22、说明：列示type、vender、pcs字段，以type字段排列，case可以方便地实现多重选择，类似select 中的case。
select type,sum(case vender when 'A' then pcs else 0 end),sum(case vender when 'C' then pcs else 0 end),sum(case vender when 'B' 
then pcs else 0 end) FROM tablename group by type
显示结果：
type vender pcs
电脑 A 1
电脑 A 1
光盘 B 2
光盘 A 2
手机 B 3
手机 C 3
23、说明：初始化表table1
TRUNCATE TABLE table1
24、说明：选择从10到15的记录
select top 5 * from (select top 15 * from table order by id asc) table_别名 order by id desc


```

## 技巧

```sql

1、1=1，1=2的使用，在SQL语句组合时用的较多
“where 1=1” 是表示选择全部    “where 1=2”全部不选，
如：
if @strWhere !=''
begin
set @strSQL = 'select count(*) as Total from [' + @tblName + '] where ' + @strWhere
end
else
begin
set @strSQL = 'select count(*) as Total from [' + @tblName + ']'
end
我们可以直接写成
错误！未找到目录项。
set @strSQL = 'select count(*) as Total from [' + @tblName + '] where 1=1 安定 '+ @strWhere 2、收缩数据库
--重建索引
DBCC REINDEX
DBCC INDEXDEFRAG
--收缩数据和日志
DBCC SHRINKDB
DBCC SHRINKFILE
3、压缩数据库
dbcc shrinkdatabase(dbname)
4、转移数据库给新用户以已存在用户权限
exec sp_change_users_login 'update_one','newname','oldname'
go
5、检查备份集
RESTORE VERIFYONLY from disk='E:\dvbbs.bak'
6、修复数据库
ALTER DATABASE [dvbbs] SET SINGLE_USER
GO
DBCC CHECKDB('dvbbs',repair_allow_data_loss) WITH TABLOCK
GO
ALTER DATABASE [dvbbs] SET MULTI_USER
GO
7、日志清除
SET NOCOUNT ON
DECLARE @LogicalFileName sysname,
 @MaxMinutes INT,
 @NewSize INT
 
USE tablename -- 要操作的数据库名
SELECT  @LogicalFileName = 'tablename_log', -- 日志文件名
@MaxMinutes = 10, -- Limit on time allowed to wrap log.
 @NewSize = 1  -- 你想设定的日志文件的大小(M)
Setup / initialize
DECLARE @OriginalSize int
SELECT @OriginalSize = size
 FROM sysfiles
 WHERE name = @LogicalFileName
SELECT 'Original Size of ' + db_name() + ' LOG is ' +
 CONVERT(VARCHAR(30),@OriginalSize) + ' 8K pages or ' +
 CONVERT(VARCHAR(30),(@OriginalSize*8/1024)) + 'MB'
 FROM sysfiles
 WHERE name = @LogicalFileName
CREATE TABLE DummyTrans
 (DummyColumn char (8000) not null)
 
DECLARE @Counter    INT,
 @StartTime DATETIME,
 @TruncLog   VARCHAR(255)
SELECT @StartTime = GETDATE(),
 @TruncLog = 'BACKUP LOG ' + db_name() + ' WITH TRUNCATE_ONLY'
DBCC SHRINKFILE (@LogicalFileName, @NewSize)
EXEC (@TruncLog)
-- Wrap the log if necessary.
WHILE @MaxMinutes > DATEDIFF (mi, @StartTime, GETDATE()) -- time has not expired
 AND @OriginalSize = (SELECT size FROM sysfiles WHERE name = @LogicalFileName) 
 AND (@OriginalSize * 8 /1024) > @NewSize 
 BEGIN -- Outer loop.
SELECT @Counter = 0
 WHILE   ((@Counter < @OriginalSize / 16) AND (@Counter < 50000))
 BEGIN -- update
 INSERT DummyTrans VALUES ('Fill Log') DELETE DummyTrans
 SELECT @Counter = @Counter + 1
 END
 EXEC (@TruncLog) 
 END
SELECT 'Final Size of ' + db_name() + ' LOG is ' +
 CONVERT(VARCHAR(30),size) + ' 8K pages or ' +
 CONVERT(VARCHAR(30),(size*8/1024)) + 'MB'
 FROM sysfiles
 WHERE name = @LogicalFileName
DROP TABLE DummyTrans
SET NOCOUNT OFF
8、说明：更改某个表
exec sp_changeobjectowner 'tablename','dbo'
9、存储更改全部表
CREATE PROCEDURE dbo.User_ChangeObjectOwnerBatch
@OldOwner as NVARCHAR(128),
@NewOwner as NVARCHAR(128)
AS
DECLARE @Name    as NVARCHAR(128)
DECLARE @Owner   as NVARCHAR(128)
DECLARE @OwnerName   as NVARCHAR(128)
DECLARE curObject CURSOR FOR
select 'Name'    = name,
   'Owner'    = user_name(uid)
from sysobjects
where user_name(uid)=@OldOwner
order by name
OPEN   curObject
FETCH NEXT FROM curObject INTO @Name, @Owner
WHILE(@@FETCH_STATUS=0)
BEGIN   
if @Owner=@OldOwner
begin
   set @OwnerName = @OldOwner + '.' + rtrim(@Name)
   exec sp_changeobjectowner @OwnerName, @NewOwner
end
-- select @name,@NewOwner,@OldOwner
FETCH NEXT FROM curObject INTO @Name, @Owner
END
close curObject
deallocate curObject
GO
 
10、SQL SERVER中直接循环写入数据
declare @i int
set @i=1
while @i<30
begin
    insert into test (userid) values(@i)
    set @i=@i+1
end
案例：
有如下表，要求就裱中所有沒有及格的成績，在每次增長0.1的基礎上，使他們剛好及格:
 Name     score
 Zhangshan 80
 Lishi       59
 Wangwu      50
 Songquan 69
while((select min(score) from tb_table)<60)
begin
update tb_table set score =score*1.01
where score<60
if  (select min(score) from tb_table)>60
  break
 else
    continue
end
 

```
## 数据开发

```sql
 
1.按姓氏笔画排序:
Select * From TableName Order By CustomerName Collate Chinese_PRC_Stroke_ci_as //从少到多
2.数据库加密:
select encrypt('原始密码')
select pwdencrypt('原始密码')
select pwdcompare('原始密码','加密后密码') = 1--相同；否则不相同 encrypt('原始密码')
select pwdencrypt('原始密码')
select pwdcompare('原始密码','加密后密码') = 1--相同；否则不相同
3.取回表中字段:
declare @list varchar(1000),
@sql nvarchar(1000)
select @list=@list+','+b.name from sysobjects a,syscolumns b where a.id=b.id and a.name='表A'
set @sql='select '+right(@list,len(@list)-1)+' from 表A'
exec (@sql)
4.查看硬盘分区:
EXEC master..xp_fixeddrives
5.比较A,B表是否相等:
if (select checksum_agg(binary_checksum(*)) from A)
     =
    (select checksum_agg(binary_checksum(*)) from B)
print '相等'
else
print '不相等'
6.杀掉所有的事件探察器进程:
DECLARE hcforeach CURSOR GLOBAL FOR SELECT 'kill '+RTRIM(spid) FROM master.dbo.sysprocesses
WHERE program_name IN('SQL profiler',N'SQL 事件探查器')
EXEC sp_msforeach_worker '?'
7.记录搜索:
开头到N条记录
Select Top N * From 表
-------------------------------
N到M条记录(要有主索引ID)
Select Top M-N * From 表 Where ID in (Select Top M ID From 表) Order by ID   Desc
----------------------------------
N到结尾记录
Select Top N * From 表 Order by ID Desc
案例
例如1：一张表有一万多条记录，表的第一个字段 RecID 是自增长字段， 写一个SQL语句，找出表的第31到第40个记录。
 select top 10 recid from A where recid not  in(select top 30 recid from A)
分析：如果这样写会产生某些问题，如果recid在表中存在逻辑索引。
 select top 10 recid from A where……是从索引中查找，而后面的select top 30 recid from A 则在数据表中查找，这样由于索引中的顺序有可能和数据表中的不一致，
这样就导致查询到的不是本来的欲得到的数据。

解决方案
1， 用order by select top 30 recid from A order by ricid 如果该字段不是自增长，就会出现问题
2， 在那个子查询中也加条件：select top 30 recid from A where recid>-1
例2：查询表中的最后以条记录，并不知道这个表共有多少数据,以及表结构。
set @s = 'select top 1 * from T   where pid not in (select top ' + str(@count-1) + ' pid  from  T)'
print @s      exec  sp_executesql  @s
9：获取当前数据库中的所有用户表
select Name from sysobjects where xtype='u' and status>=0
10：获取某一个表的所有字段
select name from syscolumns where id=object_id('表名')
select name from syscolumns where id in (select id from sysobjects where type = 'u' and name = '表名')
两种方式的效果相同
11：查看与某一个表相关的视图、存储过程、函数
select a.* from sysobjects a, syscomments b where a.id = b.id and b.text like '%表名%'
12：查看当前数据库中所有存储过程
select name as 存储过程名称 from sysobjects where xtype='P'
13：查询用户创建的所有数据库
select * from master..sysdatabases D where sid not in(select sid from master..syslogins where name='sa')
或者
select dbid, name AS DB_NAME from master..sysdatabases where sid <> 0x01
14：查询某一个表的字段和数据类型
select column_name,data_type from information_schema.columns
where table_name = '表名'
15：不同服务器数据库之间的数据操作
--创建链接服务器
exec sp_addlinkedserver   'ITSV ', ' ', 'SQLOLEDB ', '远程服务器名或ip地址 '
exec sp_addlinkedsrvlogin  'ITSV ', 'false ',null, '用户名 ', '密码 '
--查询示例
select * from ITSV.数据库名.dbo.表名
--导入示例
select * into 表 from ITSV.数据库名.dbo.表名
--以后不再使用时删除链接服务器
exec sp_dropserver  'ITSV ', 'droplogins '
 
--连接远程/局域网数据(openrowset/openquery/opendatasource)
--1、openrowset
--查询示例
select * from openrowset( 'SQLOLEDB ', 'sql服务器名 '; '用户名 '; '密码 ',数据库名.dbo.表名)
--生成本地表
select * into 表 from openrowset( 'SQLOLEDB ', 'sql服务器名 '; '用户名 '; '密码 ',数据库名.dbo.表名)
 
--把本地表导入远程表
insert openrowset( 'SQLOLEDB ', 'sql服务器名 '; '用户名 '; '密码 ',数据库名.dbo.表名)
select *from 本地表
--更新本地表
update b
set b.列A=a.列A
 from openrowset( 'SQLOLEDB ', 'sql服务器名 '; '用户名 '; '密码 ',数据库名.dbo.表名)as a inner join 本地表 b
on a.column1=b.column1
--openquery用法需要创建一个连接
--首先创建一个连接创建链接服务器
exec sp_addlinkedserver   'ITSV ', ' ', 'SQLOLEDB ', '远程服务器名或ip地址 '
--查询
select *
FROM openquery(ITSV,  'SELECT *  FROM 数据库.dbo.表名 ')
--把本地表导入远程表
insert openquery(ITSV,  'SELECT *  FROM 数据库.dbo.表名 ')
select * from 本地表
--更新本地表
update b
set b.列B=a.列B
FROM openquery(ITSV,  'SELECT * FROM 数据库.dbo.表名 ') as a 
inner join 本地表 b on a.列A=b.列A
 
--3、opendatasource/openrowset
SELECT   *
FROM   opendatasource( 'SQLOLEDB ',  'Data Source=ip/ServerName;User ID=登陆名;Password=密码 ' ).test.dbo.roy_ta
--把本地表导入远程表
insert opendatasource( 'SQLOLEDB ',  'Data Source=ip/ServerName;User ID=登陆名;Password=密码 ').数据库.dbo.表名
select * from 本地表
```
# PowerDesigner设计建造MySQL数据库

## 制作建库脚本

1. 设计CDM(Conceptual Data Model)
2. 选择 Tools -> Generate Physical Data Model ，选择对应的DBMS为MySQL，生成PDM
3. 选择 Database -> Generate Database ，在弹出的 Database Generation 对话框中选择脚本存取路径及脚本文件名称
4. 点击确定后生成数据库建库脚本(*.sql)

## 执行sql脚本 

### 第一种方法: 

在命令行下(未连接数据库),输入 mysql -h localhost -u root -p123456 < F:/mytest/testdb.sql (注意路径不用加引号的!!) 回车即可. 

### 第二种方法: 

在命令行下(已连接数据库,此时的提示符为 mysql> ),输入 source F:/mytest/testdb.sql (注意路径不用加引号的) 回车即可

# JDBC

## JDBC概念

JDBC（Java DataBase Connectivity,java数据库连接）是一种用于执行SQL语句的Java API，可以为多种关系数据库提供统一访问，它由一组用Java语言编写的类和接口组成。JDBC提供了一种基准，据此可以构建更高级的工具和接口，使数据库开发人员能够编写数据库应用程序。

## 一、JDBC常用接口、类介绍

JDBC提供对独立于数据库统一的API，用以执行SQL命令。API常用的类、接口如下：

### **DriverManager**

管理JDBC驱动的服务类，主要通过它获取Connection数据库链接，常用方法如下：


```java
public static synchronized Connection getConnection(String url, String user, String password) throws Exception;
```

该方法获得url对应的数据库的连接。

### **Connection**常用数据库操作方法：


```java
Statement createStatement throws SQLException: //该方法返回一个Statement对象。

PreparedStatement prepareStatement(String sql) throws SQLException;//该方法返回预编译的Statement对象， 即将SQL语句提交到数据库进行预编译。

CallableStatement prepareCall(String sql) throws SQLException：//该方法返回CallableStatement对象，该对象用于存储过程的调用。
```

上面的三个方法都是返回执行SQL语句的Statement对象，PreparedStatement、CallableStatement的对象是Statement的子类，只有获得Statement之后才可以执行SQL语句。

### Connection控制事务


```java
Savepoint setSavepoint(): //创建一个保存点

Savepoint setSavepoint(String name)：//创建一个带有名称的保存点

void setTransactionIsolation(int level)://设置事务隔离级别

void rollback()：//回滚事务

void rollback(Savepoint savepoint)：//回滚到指定保存点

void setAutoCommit(boolean autoCommit): //关闭自动提交，打开事务

void commit()：提交事务
```


### **Statement**


用于执行SQL语句的API接口，该对象可以执行DDL、DCL语句，也可以执行DML语句，还可以执行SQL查询语句，当执行查询语句是返回结果集，常用方法如下：


```java
ResultSet executeQuery(String sql) throws SQLException：//该方法用于执行查询语句，并返回查询结果对应的ResultSet对象，该方法只用于查询语句。

int executeUpdate(String sql) throws SQLException：//该方法用于执行DML语句，并返回受影响的行数；该方法也可以执行DDL，执行DDL返回0；

boolean execute(String sql) throws SQLException：//该方法可以执行任何SQL语句，如果执行后第一个结果是ResultSet对象，则返回true；如果执行后第一个结果为受影响的行数或没有任何结果，则返回false；
```

### **PreparedStatement**


预编译的statement对象，PreparedStatement是Statement的子接口，它允许数据库预编译SQL（通常指带参数SQL）语句，以后每次只改变SQL命令参数，避免数据库每次都编译SQL语句，这样性能就比较好。而相对于Statement而言，使用PreparedStatement执行SQL语句时，无需重新传入SQL语句，因为它已经预编译了SQL语句。

但是PreparedStatement需要为编译的SQL语句传入参数值，所以它比了如下方法：

```
void setXxx(int index, value)//根据该方法传入的参数值的类型不同，需要使用不同的方法。
```

传入的值的类型根据传入的SQL语句参数而定。


### **ResultSet**


```java
void close() throws SQLException：//释放、关闭ResultSet对象

boolean absolute(int row)://将结果集移动到第几行，如果row是负数，则移动到倒数第几行。如果移动到的记录指针指向一条有效记录，则该方法返回true；

void beforeFisrt(): //将ResultSet的记录指针定位到首行之前，这是ResultSet结果集记录指针的初始状态：记录指针的起始位置位于第一行之前。

boolean first()：//将ResultSet的记录指针定位到首行。如果移动后的记录指针指向一条有效记录，则该方法返回true。

boolean previous()：//将ResultSet的记录指针定位到上一行，如果移动后的记录指针指向一条有效记录，则该方法返回true。

boolean next()：//将ResultSet的记录指针定位到下一行。如果移动后的记录指针指向一条有效记录，则返回true。

boolean last()：//将ResultSet的记录指针定位到最后一行。如果移动后的记录指针指向一条有效记录，则返回true。

void afterLast()：//将ResultSet的记录指针定位到最后一行之后。
```


注意：在JDK1.4以前只支持next移动，且每次移动一个位置。到JDK1.5就可以随意定位。


## 二、JDBC编程步骤

进行jdbc编程步骤大致如下：

1. 加载数据库驱动

Class.forName(driverClass)
上面的dirverClass就是数据库驱动类所对应的类路径字符串，根据不同数据库厂商提供的驱动也不同。

2. 通过DriverManager获取数据库的链接

DriverManager.getConnection(String url, Stirng user, String pass)
当使用DriverManager来获取链接，需要传入三个参数：分别是数据量的url、用户名、密码。

3. 通过Connection对象创建Statement对象，Connection创建Statement的方法如下三个：

createStatement()创建基本的Statement对象。
prepareStatement(String sql)：根据传入的sql语句创建预编译的Statement对象。
prepareCall(String sql)：根据传入的sql语句创建CallableStatement对象

4. Statement执行SQL语句，Statement有三大方法来执行SQL语句：

execute：可以执行任何SQL语句，单比较麻烦
executeUpdate：可以执行DML、DDL语句。执行DML返回受影响的SQL语句行数，执行DDL返回0；
executeQuery：只能执行查询语句，执行后返回代表查询结果的ResultSet对象。

5. 操作结果集，针对ResultSet

主要移动指针和获得值

next、previous、first、last、beforeFrist、afterLast、absolute等移动指针的方法。

getXxx获得移动指针指向行，特定列、索引的值。使用列名作为获取值的参数可读性好、使用索引作为获取参数性能好。


## 三、JDBC执行SQL语句

1. executeUpdate执行DDL、DML语句

Statement提供了execute、executeUpdate、executeQuery三种方法执行，下面用executeUpdate来执行DDL、DML语句，executeUpdate执行DDL返回值是0，执行了DML是返回影响后的记录条数。


2. execute执行SQL语句

当我们知道SQL语句是完成修改语句时，我们就知道使用executeUpdate语句来完成操作；如果SQL语句是完成查询操作的时候，我们就使用executeQuery来完成。

如果我们不知道SQL语句完成什么操作的时候，就可以使用execute方法来完成。

当我们使用Statement对象的execute方法执行SQL语句后返回的是boolean值，这就说明该语句能否返回ResultSet对象。

那么，如何判断是否是ResultSet对象？方法如下：

getResultSet()：获取该Statement执行查询语句返回的ResultSet对象

getUpdateCount()：获取该Statement执行修改语句影响的行数

3. PrepareStatement执行SQL语句


对于我们操作数据库的时候，执行某一条SQL语句的时候。只有它的参数不同，而SQL语句相同。

我们可以使用占位符来设置我们的参数信息，PrepareStatement中的占位符是？，用？代替参数的位置。


```sql
insert into table values(?, ‘abc’, ?);
```

占位符仅仅支持PrepareStatement，而Statement不支持占位符。PrepareStatement是预编译SQL语句的，然后将占位符替换成参数。而Statement就不能做到。


PrepareStatement对象也有execute、executeUpdate、executeQuery这三个方法，但这三个方法都无需传递参数。只需用PrepareStatement来设置占位符的参数，通过用setXxxx(index, value)来完成设置参数信息即可。PrepareStatement的效率要比Statement的效率高。PrepareStatement设置参数可以不拼接字符串，而Statement设置参数信息的时候需要手动拼接字符串。拼接字符串容易操作程序错误、可读性降低、维护性升高、程序性能下降。而PrepareStatement直接设置参数信息就降低了编程的复杂度。并且它可以放在SQL注入。因为它是通过setXxx方法进行设置参数信息，而Statement是通过拼接字符串，很容易就造成SQL注入。


综上所述，PrepareStatement比Statement有以下优点：

- 预编译SQL语句，性能更好
- 无需拼接SQL语句，编程更简单
- 可以防止SQL语句注入，安全性更好


4. CallableStatement调用存储过程


存储过程的调用可以通过CallableStatement，通过Connection对象的prepareCall方法来创建CallableStatement对象。

然后传入存储过程的SQL语句，即可调用存储过程，格式如下：


```sql
{call proc_name(?, ?, ?)}
```

上面的?是占位符，表示传递的参数。

存储过程有传入参数、传出参数。传入参数是程程序必须传入的参数，可以 通过setXxx方法进行设置参数值。

而传出参数则需要通过程序进行设置，可以用CallableStatement对象的registerOutParameter方法来注册输出参数，cs.registerOutParameter(3, Types.STRING);

设置完毕后，当调用存储过程后要获取输出参数值，可以通过getXxx方法来完成。


## 四、操作结果集(ResultSet)

JDBC是通过ResultSet来管理结果集，操作ResultSet可以通过移动其指针来指向不同的行记录，然后取出当前记录即可。并且ResultSet可以完成更新记录，还提供了ResultSetMetaData来获得对象相关信息。

1. 可移动、可更新的ResultSet

前面介绍过ResultSet的相关方法，可以通过一系列的方法来移动记录指针，如：absolute、previous、next、first、last、beforeFirst、afterLast等方法。

ResultSet默认是不支持更新的，如果希望ResultSet完成更新操作，必须在创建Statement或PrepareStatement时传入一些参数。

Connection对象在创建Statement或PrepareStatement时可以传入两个参数：



### resultSetType：控制ResultSet的类型，该参数有以下三个值：

- ResultSet.TYPE_FORWARD_ONLY该常量控制记录指针只能向前移动。Jdk1.4的默认值
- ResultSet.TYPE_SCROLL_INSENSITIVE：该常量控制记录指针自由移动(可滚动结果集)，但底层的数据改变不影响结果集ResultSet的内容
- ResultSet.TYPE_SCROLL_SENSITIVE：该常量控制记录指针自由移动，但底层数据的影响会改变结果集ResultSet的内容


### resultSetConcurrency：控制ResultSet的并发类型，该参数可以接收如下两个值：

- ResultSet.CONCUR_READ_ONLY：该常量表示ResultSet是只读并发模式
- ResultSet.CONCUR_UPDATABLE：该常量表示ResultSet是更新并发模式

通过PrepareStatement、Statement的创建时进行参数设置来创建可滚动、可更新的ResultSet，然后通过rs的updateXxx方法来完成某列的更新值设置，通过updateRow来提交修改。

2. ResultSet中的二进制Blob数据处理


Blob类型通常用来存储文件，如：图片、音频、视频文件。将文件转换成二进制保存在数据库中，取出来的时候可以二进制数据恢复成文件。

如果要插入图片到数据库，显然不能直接设置SQL参数拼接字符串进行插入。因为二进制常量无法表示。

但是将Blob类型数据插入到数据可以用PrepareStatement，通过PrepareStatement对象的setBinaryStatement方法将参数传入到二进制输入流；也可以用Blob对象的getBytes方法直接取出数据。

3. 利用ResultSetMetaData操作ResultSet结果集


在我们查询数据返回的结果集中，我们不清楚结果集存放的数据类型、数据列数。

那样我们就可以用ResultSetMetaData来读取ResultSet的信息。

通过ResultSet的getMetaData()的方法可以获取ResultSetMetaData对象。

然后可以用ResultSetMetaData对象的方法来操作ResultSet，常用方法如下：

- int getColumnCount()：返回ResultSet的列名数量
- int getColumnType(int column)：返回指定索引的类型
- String getColumnName(int column)：返回指定索引的列名

## 五、JDBC事务

1. 事务介绍


事务是一步或多步组成操作序列组成的逻辑执行单元，这个序列要么全部执行，要么则全部放弃执行。

事务的四个特性：原子性（Atomicity）、一致性（Consistency）、隔离性（IsoIation）和持续性（Durability）

- 原子性（****Atomicity****）：事务应用最小的执行单元，不可再分。是事务中不可再分的最小逻辑执行体。

- 一致性（****Consistency****）：事务的执行结果，必须使数据库的从一个一致性的状态变到另一个一致性的状态。

- 隔离线（****IsoIation****）：各个事务的执行互不干扰，任意一个事务的内部操作对其他并发的事务，都是隔离的。也就是：并发执行的事务之间不能看到对方的中间状态，并发执行的事务之间不能互相影响。

- 持续性（****Durability****）：持续性也称为持久性（Persistence），指事务一旦提交，对数据所做的任何改变，都要记录到永久存储器中，通常就是保存在物理数据库中。


通常数据库的事务涉及到的语句有：

一组DML（Data Munipulation Language，数据操作语言）语句，这组DML语句修改后数据将保持较好的一致性；

操作表的语句，如插入、修改、删除等；

一个DDL（Data Definition Language，数据定义语言）语句，操作数据对象的语言，有create、alter、drop。

一个DCL（Data Control Language，数据控制语言）语句，主要有grant、revoke语句。

DDL和DCL语句最多只能有一个，因为它们都会导致事务的立即提交。

当事务所包含的全部数据库操作都成功执行后，应该提交事务，使这些修改永久生效。

事务提交有两种方式：显示提交和自动提交。

显示提交：使用commit提交

自动提交：执行DLL或DCL，或者程序正常退出

当事务包含的任意一个数据库操作执行失败后，应该回滚（rollback）事务，使该事务中所作的修改全部失效。

事务的回滚方式有两种：显示回滚和自动回滚。

显示回滚：使用rollback

自动回滚：系统错误或强行退出

2. JDBC的事物的支持


JDBC的Connection也支持事物，Connection默认打开自动提交，即关闭事物。

也就是说，每条SQL语句执行就会立即提交到数据库，永久生效，无法对其进行操作。

关闭Connection的自动提交，开启事物。Connection的setAutoCommit方法即可：connection.setAutoCommit(false);

通过connection.getAutoCommit()来获取事物的模式。

当我们开启事物后，在当前Connection中完成的数据库操作，都不会立即提交到数据库，需要调用Connection的commit方法才行。

如果有语句执行失败，可以调用rollback来回滚。

注意：如果Connection遇到未处理的SQLException异常时，系统将非正常退出，系统会自动回滚该事务。

如果程序捕捉了该异常，则需要在异常处理中显示回滚事务。


Connection提供了设置事务中间保存点的方法：setSavepoint，有2个方法可以设置中间点：

Savepoint setSavepoint()：在当前事务中创建一个未命名的中间点，并返回该中间点的Savepoint对象。

Savepoint setSavepoint(String name)：当前事务中创建一个具有指定名称的中间点，并返回该中间点的Savepoint对象

通常setSavepoint(String name)设置中间点的名称，事务回滚并不是通过中间点的名称进行回滚的，而是根据中间点对象进行回滚的。

设置名称只是更好的区分中间点对象，用Connection的rollback(Savepoint savepoint)方法即可完成回滚到指定中间点。

3. JDBC的批量更新

批量更新就是可以同时进行多条SQL语句，将会被作为一批操作被同时执行、同时提交。

批量更新需要得到数据底层的支持，可以通过调研DataBaseMetaData的supportsBatchUpdates方法来查看底层数据库是否支持批量更新。

批量更新也需要创建一个Statement对象，然后通过该对象的addBatch方法将多条SQL语句同时收集在一起，

然后通过Statement对象的executeBatch同时执行这些SQL语句，如下代码：


```java
Statement sm = conn.createStatement();
sm.addBatch(sql);
sm.addBatch(sql2);
sm.addBatch(sql3);
…
//同时执行多条SQL语句
sm.executeBatch();
```

执行executeBatch将返回一个int[]的数组，因为使用Statement执行DDL、DML都将返回一个int的值，而执行多条DDL、DML也将返回一个int数组。批量更新中不允许出现select查询语句，一旦出现程序将出现异常。

如果要批量更新正确、批量完成，需要用单个事务，如果批量更新过程中有失败，则需要用事务回滚到原始状态。

如果要达到这样的效果，需要关闭事务的自动提交，当批量更新完成再提交事务，如果出现异常将回滚事务。

然后将连接恢复成自动提交模式。

```java
public int[] executeBatch(String[] sql) throws SQLException {
        int[] result = null;
        conn = DBHelper.getConnection();
        try {
        //获得当前Connection的提交模式
        boolean autoCommit = conn.getAutoCommit();
        //关闭自动提交模式
        conn.setAutoCommit(false);
        sm = conn.createStatement();
        for (String s : sql) {
                        sm.addBatch(s);
            }
        //执行批量更新
        result = sm.executeBatch();
        //提交事务
        conn.commit();
        //还原提交模式
        conn.setAutoCommit(autoCommit);
                } catch (Exception e) {
                e.printStackTrace();
                conn.rollback();
                } finally {
                if (sm != null) {
                sm.close();
                }
                DBHelper.close();
                }
                 return result;
}
```


## 六、分析数据库数据

1. 使用DatabaseMetaData分析数据库数据

JDBC提供了DatabaseMetaData来封装数据库连接对应的数据库信息，通过Connection的getMetaData方法来获取该对象。

DatabaseMetaData接口通常数据库驱动提供商完成实现，其作用是让用户了解数据库的底层信息。

使用该接口可以了解数据库底层的实现，便于完成多个数据库的相互切换。

如：可以利用supportsCorrelatedSubquenes方法来查看数据库底层是否可以利用关联子查询，或是调用supportsBatchUpdates方法查看是否支持批量更新。

大部分的DatabaseMetaData都是以ResultSet对象返回的，可以通过ResultSet对象的getString、getInt来获取相关信息。
DatabaseMetaData方法都需要传递一个xxxPattern的模式字符串，这个字符串是过滤条件，一般传递是SQL中的%、_等内容。
如果传递一个null表示不作任何过滤。

