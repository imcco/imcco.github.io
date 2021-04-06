---
title: 项目运维中常用SQL
tags: SQL
category: SQL
abbrlink: 54584
date: 2017-12-18 22:34:36
---
![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0033.jpg)

结构化查询语言(Structured Query Language)简称SQL，是一种特殊目的的编程语言，是一种数据库查询和程序设计语言，用于存取数据以及查询、更新和管理关系数据库系统；同时也是数据库脚本文件的扩展名。
<!--more-->

### 删除表空间
 
```sql
drop tablespace pt6 INCLUDING CONTENTS;drop tablespace mpm INCLUDING CONTENTS;
 drop tablespace sf INCLUDING CONTENTS;drop tablespace bt INCLUDING CONTENTS;
```

### 删除用户

```sql
drop user pt6 CASCADE;
drop user mpm CASCADE;
drop user sf CASCADE;
drop user bt CASCADE;
```

### 创建表空间
当首次导入时创建表空间 （运行脚本时会提示输入 &1 输入数据文件希望存放的物理地址 例如 D:\OracleData）

```sql
create tablespace pt6 datafile '&1\pt6.dbf'size 100m autoextend on next 10m;create tablespace mpm datafile '&1\mpm.dbf'size 100m autoextend on next 10m;
```

### 创建用户

```sql
create user pt6 identified by cape default tablespace pt6;create user mpm identified by cape default tablespace mpm;
```

### 授权

```sql
grant dba to pt6;grant dba to mpm;
grant all on dual to pt6 with grant option;
grant all on dual to mpm with grant option;
```

### 数据库的备份：
#### 方法一：
##### 数据泵方式    
- 导出：

```sql
expdp pt6/cape@localhost:1521/bt dumpfile=ORCL_2014-08-18.EXPDP logfile=ORCL_2014-08-18.log schemas=(pt6,mpm,sf,bt)             //如果是用数据泵倒入，把文件必须放在例：D:\app\rongda\admin\bt\dpdump(oracle的安装目录)
```

- 导入：

```sql
impdp pt6/cape@localhost:1521/bt dumpfile=NEWBT_2015-03-16.EXPDP logfile =NEWBT_2015-03-16.log schemas=(pt6,mpm,sf,bt)
```
#### 方法二：
- 导出：

```sql
exp pt6/cape@localhost:1521/TEST file=c:\test_2014-04-02.dmp log=c:\test_2014-04-02.log owner=(pt6,mpm)
```
- 导入:

```sql
imp pt6/cape@localhost:1521/orcl file=D:\app\rongda\oradata\orcl\20140704sf.dmp full=y log=c:\test_2014-04-02.log
```
          
```sql
imp pt6/cape@localhost:1521/orcl file=D:\app\rongda\oradata\orcl\20140704sf.dmp fromuser=pt6 touser=pt6 fromuser=mpm touser=mpm log=c:\test_2014-04-02.log
```
> 说明：如果高版本的数据库导向低版本的数据库脚步，那么在导出的时候加上低版本号 version=11.1.0.7.0 版本号即可。导入不需要加入。10.1.0.2.0

例如：

```sql
expdp pt6/cape@localhost:1521/TEST dumpfile=TEST_2014-04-02.EXPDP logfile=EXP_TEST_2014-04-02.log schemas=(pt6,mpm) version=11.1.0.7.0
```
             
```sql
expdp pt6/cape@localhost:1521/TEST dumpfile=TEST_2014-04-02.EXPDP logfile=EXP_TEST_2014-04-02.log schemas=(pt6,mpm) version=10.1.0.2.0
```
            
```sql
impdp pt6/cape@localhost:1521/TEST dumpfile=TEST_2014-04-02.EXPDP logfile=IMP_TEST_2014-04-02.log schemas=(pt6,mpm)
```

### 同义词创建：

```sql
create or replace synonym pt6.application_anplan for bt.application_anplan
```

### 表的恢复与表数据恢复

对误删的表，只要没有使用 purge 永久删除选项，那么基本上是能从 flashback table 区恢复回来的。
数据表和其中的数据都是可以恢复回来的，记得 flashback table 是从 Oralce 10g 提供的，一般步骤有：
1. 从 flashback table 里查询被删除的数据表

```sql
select*from recyclebin orderby droptime desc
```

2. 执行表的恢复

```sql
flashback table'需要恢复的表名'to before drop
```

### 删除表中数据有三种方法：

1. delete（删除一条记录）
2. drop或truncate删除表格中数据

#### delete误删除的解决方法原理：

利用oracle提供的闪回方法:

如果在删除数据后还没做大量的操作（只要保证被删除数据的块没被覆写），就可以利用闪回方式直接找回删除的数据
###### 具体步骤为：
1. 确定删除数据的时间（在删除数据之前的时间就行，不过最好是删除数据的时间点)
2. 用以下语句找出删除的数据：

```sql
select * from 表名 as of timestamp to_timestamp('删除时间点','yyyy-mm-dd hh24:mi:ss');
```

3. 把删除的数据重新插入原表：

```sql
insert into 表名 (select * from 表名 as of timestamp to_timestamp('删除时间点','yyyy-mm-dd hh24:mi:ss'));
```
- 注意要保证主键不重复。如果表结构没有发生改变，还可以直接使用闪回整个表的方式来恢复数据
具体步骤为：表闪回要求用户必须要有flash any table权限

```sql
alter table 表名 enable row movement ·flashback table 表名 to timestamp to_timestamp(删除时间点','yyyy-mm-dd hh24:mi:ss')
```
- drop误删除的解决方法原理：

由于oracle在删除表时，没有直接清空表所占的块,oracle把这些已删除的表的信息放到了一个虚拟容器“回收站”中，而只是对该表的数据块做了可以被覆写的标志，所以在块未被重新使用前还可以恢复
###### 具体步骤：
查询这个“回收站”或者查询user_table视图来查找已被删除的表: 

```sql
select table_name,dropped from user_tables · select object_name,original_name,type,droptime from user_recyclebin
```
在以上信息中，表名都是被重命名过的，字段table_name或者object_name就是删除后在回收站中的存放表名*如果还能记住表名，则可以用下面语句直接恢复：  

```sql
flashback table 原表名 to before drop
```

如果记不住了，也可以直接使用回收站的表名进行恢复，然后再重命名，参照以下语句：  


```sql
flashback table "回收站中的表名(如：Bin$DSbdfd4rdfdfdfegdfsf==$0)" to before drop rename to 新表名
```
oracle的闪回功能除了以上基本功能外，还可以闪回整个数据库：使用数据库闪回功能，可以使数据库回到过去某一状态, 语法如下：

```sql
alter database flashback onSQL>flashback database to scn SCNNO;SQL>flashback database to timestamp to_timestamp('2007-2-12 12:00:00','yyyy-mm-dd hh24:mi:ss');
```
#### 总结：
oracle提供以上机制保证了安全操作，但同时也代来了另外一个问题，就是空间占用，由于以上机制的运行，使用drop一个表或者delete数据后，空间不会自动回收，对于一些确定不使用的表，删除时要同时回收空间，可以有以下2种方式：

1. 采用truncate方式进行截断。（但不能进行数据回恢复了） 
2. 在drop时加上purge选项：drop table 表名 purge   
3. 
   该选项还有以下用途：  

- 通过删除recyclebin区域来永久性删除表 ,原始删除表

```sql
drop table emp cascade constraints   purge table emp;
```
- 删除当前用户的回收站:    purge recyclebin;   
- 删除全体用户在回收站的数据:   purge dba_recyclebin 
