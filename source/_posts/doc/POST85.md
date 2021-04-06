---
title: 数据库设计规范
tags: Document
category: Document
date: 2018-02-06 15:44:36
---
![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0113.jpg)

数据库设计规范
<!--more-->

## 1     基本原则

Ø  产品根据业务功能的不同，划分为多个相对独立的业务子系统。对应于一个业务子系统，数据库中都应建立一个用户，该业务子系统中的数据、业务规则都在此用户下的对象中描述。如：数据库中建立MES用户，来对应制造执行系统，制造执行系统中的数据、业务逻辑都由MES用户下的对象（如表、包及存储过程等等）来描述，系统管理由APPSYS用户来管理；

Ø  除两个公共用户APPS，APPVIEW外，其他用户下的所有对象，都需要在APPS中建立同义词，并对APPS授权；

Ø  除两个公共用户APPS，APPVIEW外，其他用户下的所有的视图， 都需要在APPVIEW中建立对应的表； 

Ø  系统管理部分的表，需用设置多语言；

## 2     表及字段规范

## 2.1  表命名约定

数据库中以描述表中内容的英文为表名，前面以该表所属的用户+下划线作为开头，具体遵循以下规则：

用户 + ‘_’+ 英文，如MDS_Route表示MDS用户下的工艺路线表，

英文如果是两个单词及以上，单词之间尽量用下划线分割。如果英文有公共熟知的缩写，可以使用缩写。如MES_Control_Dept表示MES用户下的受控部门表

## 2.2  表字段约定

1.  每个表中必须包含7个字段：

| 字段名称及作用   | 字段代码             | 类型           |
| --------- | ---------------- | ------------ |
| 表ID，作为表主键 | ID               | Number(16,0) |
| 创建人       | CREATED_BY       | Number(16,0) |
| 创建时间      | CREATION_DATE    | Date         |
| 最后修改人     | LAST_UPDATED_BY  | Number(16,0) |
| 最后修改时间    | LAST_UPDATE_DATE | Date         |
| 最后更新IP    | LAST_UPDATE_IP   | VARCHAR2(20) |
| 版本        | VERSION          | Number(16,0) |

 

2.  主键字段：每个表必须以ID作为主键，通过建立UniqueKey来实现实际的主键组合。如部门表中，将部门代码建立为Unique Key

3.  外键字段：系统不显式建立外键，表中的字段如果需要引用其他表的主键，尽量以其他表的表名+ ‘_ID’作为此字段名，并在注释中显式说明，如：员工表中引用部门表的主键的字段，命名为HR_Dept_ID；如果表中有两个及以上的字段需要引用同一个表的主键，需要在此规则的基础上，前面添加用途，如两个字段都引用部门表主键，制造部门命名为：Manufact_HR_Dept_ID，使用部门命名为：User_HR_Dept_ID。

4.  字段注释：每个字段必须有注释，说明该字段的含义，值的来源。对于标识，状态之类的字段，必须注明其每个可能的取值，及每个取值的含义。如对于标识字段，注释需要标明：Y-是，N-否，说明此字段的取值为Y和N，含义分别为是和否。

5.  字段数据类型：对于标识，状态字段，一般取值为一个字符（Varchar2(1)），如果状态较多，可以设置为2个字符（Varchar2(2)）；对于数量等字段，设置为数值（Number(20，7)）；对于代码、名称、说明、描述的字段，一般为Varchar2，字符的多少可根据需要设置。 

6.  字段取值：对于涉及到是和否的标识字段，取值必须为Y/N，但 “是否显示”字段除外，其取值为0/1；对于状态字段，一般取值为0，1，2……,对应不同的状态。

## 3     序列规范

每个表都必须有一个主键序列，作为主键ID的值.

1.       命名：表名 + ‘_S’， 如：MDS_Route_S;

2.       取值：初始值，最小值，步长均为1，最大值为999999999

   

在后来的数据库设计中，不再需要为每个表创建一个序列，用以产生主键ID。表主键的数据类型变为Varchar2(32)，用Oracle函数SYS_GUID()产生，具体方法如下：

Select SYS_GUID() into v_tableId from dual;

## 4     包及存储过程规范

对于业务系统的逻辑处理，需要建立包及存储过程来实现。如果多个存储过程实现同一业务，尽量封装在包中。

1.  包的命名：与表的命名规则类似，但需要以’ _P’结尾，如：MES_Route_P

2.  存储过程命名规范：命名尽量表示此存储过程功能；

3.  存储过程参数规范：最后两个参数必须为返回值，并遵循以下约定：

倒数第二个参数为Number，表示执行的结果，成功/失败等等 ，其取值为Codecollection里的Success/Fail/Warn

倒数第一个参数为Varchar2, 表示执行中出现的错误或警告信息

传入的参数中如果需要有日期或时间，需要设置为字符串

4.  存储过程中必须包含的处理：

如果传入的参数中如日期或时间字符串，必须进行转换的异常处理，转换时，使用Codecollection里的Date_Format_Date/Date_Format_Datetime来进行时间格式设置

插入数据时，7个必须的字段必须赋值，其中主键使用相应的序列取值，Version设为1，涉及到人员的，使用Get_Session_Info里的Get_User_Id，涉及到时间的，使用当前系统时间，IP使用Get_Session_Info里的Get_Logon_Ip

更新数据时，最后修改人，最后修改时间，最后更新IP，版本 必须更新，取值同样来源于Get_Session_Info，Version 增1

异常处理：如果执行成功，返回值中Return_Int 设为Codecollection里的 Success

如果捕捉到异常，需要返回Codecollection里的Fail/Warn，并设置Return_String的内容

需要读取配置文件中的设置时，使用Sys_Profile_Option_p的Get_Profile_Option_Value方法，来取得值。如：末工序的最大号

5.  存储过程的参数、变量命名规范

**传入参数**：命名以 i_开头，譬如i_Sys_Function_Tl_Id，具体参照appsys用户下sys_form_p包中的存储过程Delete_Form_Tl_Id的定义，

PROCEDURE
Delete_Form_Tl_Id(i_Sys_Form_Tl_Id VARCHAR2,
o_Return_Int     OUT INT,
o_Return_String  OUT VARCHAR2);

**返回值**：命名以o_开头，目前只支持两个返回值，o_Return_Int和o_Return_String，分别表示执行的结果及消息；

**变量**：命名以v_开头，如果变量是用来存储数据库表中某列的数值，其数据类型一般会采取tableName.columnName%TYPE的定义方式，如v_Sys_Function_IdSys_Function.Id%TYPE;

6.  存储过程及函数的注释

对存储过程和函数的注释主要包括功能描述、编写人、编写日期信息，一般写在包体中存储过程或函数的名称前面。如appsys用户下sys_form_p包中的存储过程Delete_Form_Tl_Id：

/***************************************************************************

  * 功能描述:级联删除form_with_tl信息

  * 编写人：刘岩

  * 编写日期: 2008-04-22

 ***************************************************************************/

  PROCEDURE Delete_Form_Tl_Id(i_Sys_Form_Tl_IdVARCHAR2,

​                              o_Return_Int     OUT INT,

​                             o_Return_String  OUT VARCHAR2) IS

​    v_Sys_Form_Id Sys_Form.Id%TYPE;

  BEGIN

​    SELECT Sys_Form_Id

​      INTO v_Sys_Form_Id

​      FROM Sys_Form_Tl

​     WHERE Id = i_Sys_Form_Tl_Id;

​    Delete_Form(v_Sys_Form_Id, o_Return_Int,o_Return_String);

  EXCEPTION

​    WHEN OTHERS THEN

​      o_Return_Int    := Codecollection.Fail;

​      o_Return_String := '删除表单失败：' || SQLERRM;

  END Delete_Form_Tl_Id;

## 5     视图规范

每个表必须建立视图，命名规范为：

如果没有多语言，则视图名为：表名 + ‘_V’

如果有多语言，则视图名为：表名 + ‘_VL’

如果在视图的基础上再建立视图，命名规范为：视图名 +‘V’

如果视图带数据权限，则视图名为：表名 + ‘_VS’

## 6     常用包及用途

APPSYS.Codecollection:常量设置

APPSYS.Sys_Profile_Option_p:读取配置文件

APPSYS.Get_Session_Info:获取当前用户信息

APPS.Gen_Appview：根据视图在APPVIEW中建立表

APPS.Sys_Auto_Code_p：管理编码

APPS.Sys_Util_p：将字符串表示的计算式格式化，计算值
