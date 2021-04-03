---
title: Java成神之路-JavaSE 面向对象和Java常用类（五）
tags: Java
category: Java
date: 2018-02-23 13:44:36
---
![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0130.jpg)

Java成神之路-面向对象和Java常用类
<!--more-->
# java简介

## 1.java是什么？

java是一种高级的面向对象的程序设计语言 。

## 2.JVM

Java Virtual Machine，是java程序跨平台的关键，不同的平台有不同的JVM，而java字节码不包含任何与平台相关的信息，不直接与平台交互，而是通过JVM间接与平台交互。应用程序在执行时，JVM加载字节码，将字节码解释成特定平台的机器码，让平台执行。

任何一个应用程序都必须转化为机器码，才能与计算机进行交互，如果机器码的来源依赖于具体的平台，那么这个应用程序就不能跨平台。而java应用程序运行时机器码由java体系的一部分JVM提供，不受平台的限制，所以实现了跨平台。

## 3.java程序运行过程

程序员编写的源码经编译器编译转化为字节码，字节码被加载到JVM，由JVM解释成机器码在计算机上运行。

## 4.java版本

针对不同的用途，java分为3个版本：

1. Java SE：java的标准版，是其他版本的基础，主要用于开发桌面应用程序。
2. Java EE：java的企业版，主要用于开发企业级分布式网络程序。
3. Java ME：主要用于嵌入式系统开发。

## 5.JDK

Java Develop Kits，使用java语言开发应用程序必备的工具包，主要包含包括了编译器、JVM、Java基础API等。

## 6.JRE

Java Run Environment，java运行所依赖的环境，包括JVM以及java基础API。

## 7.API

Application Programming Interface，应用程序编程接口，是使用java语言编写应用程序的入口，包含源码、字节码帮助文档三部分。应用程序由一系列方法构成，方法有哪些要求？什么样的方法是编程语言接受的？API提供了一些基础的方法，程序员要实现某项功能必须遵循java语言规范，调用这些方法编写更高级的方法。

## 8.java特性

1. 简单：java语言是从C++发展起来的，取消了C++中复杂难以掌握的部分，如指针。
2. 面向对象：java语言的基础。java将一切问题都看做对象与对象之间的交互，将对象抽象成方法与属性的集合。
3. 分布性：包含操作分布性与数据分布性两个方面。操作分布性是指由多个主机共同完成一项功能，数据分布性是分布在多台主机上的数据当做一个完成的整体处理。
4. 跨平台：java语言编写的应用程序，不受平台限制，可以由一种平台迁移到另一种平台。
5. 解释型：使用java语言编写的源码被转化为字节码，字节码只有被JVM解释成机器码才能被计算机执行。
6. 安全性：java语言的底层设计可以有效避免非法操作。
7. 健壮性：java提供了许多机制防止运行时出现严重错误，如编译时类型检查、异常处理。
8. 多线程：java支持多线程，允许进程内部多个线程同时工作。

# window系统安装java

## 下载JDK

首先我们需要下载java开发工具包JDK，下载地址：<http://www.oracle.com/technetwork/java/javase/downloads/index.html>，

下载后JDK的安装根据提示进行，还有安装JDK的时候也会安装JRE，一并安装就可以了。

安装JDK，安装过程中可以自定义安装目录等信息，例如我们选择安装目录为 **C:\Program Files (x86)\Java\jdk1.8.0_91**。

## 配置环境变量

1.安装完成后，右击"我的电脑"，点击"属性"，选择"高级系统设置"；

![img](http://www.runoob.com/wp-content/uploads/2013/12/win-java1.png)

2.选择"高级"选项卡，点击"环境变量"；

![img](http://www.runoob.com/wp-content/uploads/2013/12/java-win2.png)

然后就会出现如下图所示的画面：

在"系统变量"中设置3项属性，JAVA_HOME,PATH,CLASSPATH(大小写无所谓),若已存在则点击"编辑"，不存在则点击"新建"。

变量设置参数如下：

- 变量名：**JAVA_HOME**
- 变量值：**C:\Program Files (x86)\Java\jdk1.8.0_91**        // 要根据自己的实际路径配置


- 变量名：**CLASSPATH**
- 变量值：**.;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar;**         //记得前面有个"."


- 变量名：**Path**
- 变量值：**%JAVA_HOME%\bin;%JAVA_HOME%\jre\bin;**

> **注意：**在 Windows10 中，因为系统的限制，path 变量只可以使用 JDK 的绝对路径。%JAVA_HOME% 会无法识别，导致配置失败。如下所示：
>
> ```
> C:\Program Files (x86)\Java\jdk1.8.0_91\bin;C:\Program Files (x86)\Java\jdk1.8.0_91\jre\bin;
> ```

### JAVA_HOME 设置

![img](http://www.runoob.com/wp-content/uploads/2013/12/java-win4.png)

![img](http://www.runoob.com/wp-content/uploads/2013/12/java-win5.png)

### PATH设置

![img](http://www.runoob.com/wp-content/uploads/2013/12/java-win6.png)

![img](http://www.runoob.com/wp-content/uploads/2013/12/java-win7.png)

### CLASSPATH 设置

![img](http://www.runoob.com/wp-content/uploads/2013/12/java-win8.png)

这是 Java 的环境配置，配置完成后，你可以启动 Eclipse 来编写代码，它会自动完成java环境的配置。

> **注意：**如果使用1.5以上版本的JDK，不用设置CLASSPATH环境变量，也可以正常编译和运行Java程序。

### 测试JDK是否安装成功

1、"开始"->"运行"，键入"cmd"；

2、键入命令: **java -version**、**java**、**javac** 几个命令，出现以下信息，说明环境变量配置成功；

![img](http://www.runoob.com/wp-content/uploads/2013/12/java-win9.png)

------

## Linux，UNIX，Solaris，FreeBSD环境变量设置

环境变量PATH应该设定为指向Java二进制文件安装的位置。如果设置遇到困难，请参考shell文档。

例如，假设你使用bash作为shell，你可以把下面的内容添加到你的 .bashrc文件结尾: export PATH=/path/to/java:$PATH

------

## 流行JAVA开发工具

正所谓工欲善其事必先利其器，我们在开发java语言过程中同样需要依款不错的开发工具，目前市场上的IDE很多，本文为大家推荐以下下几款java开发工具：

- **Eclipse（推荐）:**另一个免费开源的java IDE，下载地址： <http://www.eclipse.org/>

  选择 **Eclipse IDE for Java Developers**：
  ![img](http://www.runoob.com/wp-content/uploads/2013/12/5A92DEAE-EFB9-493D-AC4D-808E529B533C.jpg)

- **Notepad++ : **Notepad++ 是在微软视窗环境之下的一个免费的代码编辑器，下载地址：[ http://notepad-plus-plus.org/](http://notepad-plus-plus.org/)

- **Netbeans:**开源免费的java IDE，下载地址： <http://www.netbeans.org/index.html>

------

## 使用 Eclipse 运行第一个 Java 程序

演示如下所示：

HelloWorld.java 文件代码：

```java
public class HelloWorld {    
	public static void main(String []args) {        
	System.out.println("Hello World");    
}}
```

# Java基本语言元素

## 规则

1. 命名规则 

- 类名称：首字母大写，尽可能采取驼峰式命名:MaLin HelloWorld 
- 命名标识符规范：数字、字母、下划线、$组成，但是不能以数字开头，不能是关键字或者保留字。
- 变量名称 ：首字母必须小写          常量：必须全大些 PI MAX 
- 方法名称：首字母必须小写 

2. 严格区分大小写 
3. 以分号 ‘;’ 结束

## 数据类型

### 基本数据类型（四类八种） 

1. **简单类型** 

* 第一类：整型 byte short int long
* 第二类：浮点型 float double
* 第三类：逻辑型 boolean(它只有两个值可取true false)
* 第四类：字符型 char

2. **封装类型**（数据类型转换简单类型） 

`Byte Short Integer Long Float Double Character Boolean` 

- 基本类型只能按值传递，而每个基本类型对应的封装类是按引用传递的。 
- 从性能上说java中的基本类型是在堆栈上创建的，而所有的对象类型都是在堆上创建的，（对象的引用在堆栈上创建） 

3. 引用类型

数组、 类（对象）、接口

说明： 

​       导包快捷键 ctrl+shift+o

## 变量、常量

**1. 语法**： 
数据类型 变量名称 [= 值];

```
char sex; //声明变量
        sex = '男';//初始化、赋值 
        int age = 10; 
        float score = 93.2F; 
        int x,y,z=20;
```

final 数据类型 变量名称 [= 值]; final int X=1; 
先声明 、初始化 、使用

**2. 类型之间的转换：** 
2.1小——>大：自动类型转换

```
short  y = 1;
System.out.println(y);
double x =  y;
```

System.out.println(x);—>小： 强制类型转换

```
at score = 13;//13(int)-->小，到
         System.out.println(score);
         int rs = (int)score; // 0.3截取丢失
         System.out.println(rs);
```



## 字符串类型

String在java.lang.String 
**1. 必须掌握： = = 、 equals() 字符串比较差别**：

**2. 熟练进行 简单类型字符串类型** 
String.valueOf(参数) 任意类型+”” 
字符串简单类型（封装类型：parseXxx()）

```
public class Test2 {
    public static void main(String[] args) {
        float num = 3.1f;
        String str = String.valueOf(num);
        System.out.println(str);//3.1(String)
        num =  Float.parseFloat(str) ;
    }
}
```

**3. 掌握：String类中常用功能** 


| SN(序号) | 方法描述                                                     |
| -------- | ------------------------------------------------------------ |
| 1        | [char charAt(int index)](http://www.runoob.com/java/java-string-charat.html)返回指定索引处的 char 值。 |
| 2        | [int compareTo(Object o)](http://www.runoob.com/java/java-string-compareto.html)把这个字符串和另一个对象比较。 |
| 3        | [int compareTo(String anotherString)](http://www.runoob.com/java/java-string-compareto.html)按字典顺序比较两个字符串。 |
| 4        | [int compareToIgnoreCase(String str)](http://www.runoob.com/java/java-string-comparetoignorecase.html)按字典顺序比较两个字符串，不考虑大小写。 |
| 5        | [String concat(String str)](http://www.runoob.com/java/java-string-concat.html)将指定字符串连接到此字符串的结尾。 |
| 6        | [boolean contentEquals(StringBuffer sb)](http://www.runoob.com/java/java-string-contentequals.html)当且仅当字符串与指定的StringButter有相同顺序的字符时候返回真。 |
| 7        | [static String copyValueOf(char[\] data)](http://www.runoob.com/java/java-string-copyvalueof.html)返回指定数组中表示该字符序列的 String。 |
| 8        | [static String copyValueOf(char[\] data, int offset, int count)](http://www.runoob.com/java/java-string-copyvalueof.html)返回指定数组中表示该字符序列的 String。 |
| 9        | [boolean endsWith(String suffix)](http://www.runoob.com/java/java-string-endswith.html)测试此字符串是否以指定的后缀结束。 |
| 10       | [boolean equals(Object anObject)](http://www.runoob.com/java/java-string-equals.html)将此字符串与指定的对象比较。 |
| 11       | [boolean equalsIgnoreCase(String anotherString)](http://www.runoob.com/java/java-string-equalsignorecase.html)将此 String 与另一个 String 比较，不考虑大小写。 |
| 12       | [byte[\] getBytes()](http://www.runoob.com/java/java-string-getbytes.html) 使用平台的默认字符集将此 String 编码为 byte 序列，并将结果存储到一个新的 byte 数组中。 |
| 13       | [byte[\] getBytes(String charsetName)](http://www.runoob.com/java/java-string-getbytes.html)使用指定的字符集将此 String 编码为 byte 序列，并将结果存储到一个新的 byte 数组中。 |
| 14       | [void getChars(int srcBegin, int srcEnd, char[\] dst, int dstBegin)](http://www.runoob.com/java/java-string-getchars.html)将字符从此字符串复制到目标字符数组。 |
| 15       | [int hashCode()](http://www.runoob.com/java/java-string-hashcode.html)返回此字符串的哈希码。 |
| 16       | [int indexOf(int ch)](http://www.runoob.com/java/java-string-indexof.html)返回指定字符在此字符串中第一次出现处的索引。 |
| 17       | [int indexOf(int ch, int fromIndex)](http://www.runoob.com/java/java-string-indexof.html)返回在此字符串中第一次出现指定字符处的索引，从指定的索引开始搜索。 |
| 18       | [int indexOf(String str)](http://www.runoob.com/java/java-string-indexof.html) 返回指定子字符串在此字符串中第一次出现处的索引。 |
| 19       | [int indexOf(String str, int fromIndex)](http://www.runoob.com/java/java-string-indexof.html)返回指定子字符串在此字符串中第一次出现处的索引，从指定的索引开始。 |
| 20       | [String intern()](http://www.runoob.com/java/java-string-intern.html) 返回字符串对象的规范化表示形式。 |
| 21       | [int lastIndexOf(int ch)](http://www.runoob.com/java/java-string-lastindexof.html) 返回指定字符在此字符串中最后一次出现处的索引。 |
| 22       | [int lastIndexOf(int ch, int fromIndex)](http://www.runoob.com/java/java-string-lastindexof.html)返回指定字符在此字符串中最后一次出现处的索引，从指定的索引处开始进行反向搜索。 |
| 23       | [int lastIndexOf(String str)](http://www.runoob.com/java/java-string-lastindexof.html)返回指定子字符串在此字符串中最右边出现处的索引。 |
| 24       | [int lastIndexOf(String str, int fromIndex)](http://www.runoob.com/java/java-string-lastindexof.html) 返回指定子字符串在此字符串中最后一次出现处的索引，从指定的索引开始反向搜索。 |
| 25       | [int length()](http://www.runoob.com/java/java-string-length.html)返回此字符串的长度。 |
| 26       | [boolean matches(String regex)](http://www.runoob.com/java/java-string-matches.html)告知此字符串是否匹配给定的正则表达式。 |
| 27       | [boolean regionMatches(boolean ignoreCase, int toffset, String other, int ooffset, int len)](http://www.runoob.com/java/java-string-regionmatches.html)测试两个字符串区域是否相等。 |
| 28       | [boolean regionMatches(int toffset, String other, int ooffset, int len)](http://www.runoob.com/java/java-string-regionmatches.html)测试两个字符串区域是否相等。 |
| 29       | [String replace(char oldChar, char newChar)](http://www.runoob.com/java/java-string-replace.html)返回一个新的字符串，它是通过用 newChar 替换此字符串中出现的所有 oldChar 得到的。 |
| 30       | [String replaceAll(String regex, String replacement](http://www.runoob.com/java/java-string-replaceall.html)使用给定的 replacement 替换此字符串所有匹配给定的正则表达式的子字符串。 |
| 31       | [String replaceFirst(String regex, String replacement)](http://www.runoob.com/java/java-string-replacefirst.html) 使用给定的 replacement 替换此字符串匹配给定的正则表达式的第一个子字符串。 |
| 32       | [String[\] split(String regex)](http://www.runoob.com/java/java-string-split.html)根据给定正则表达式的匹配拆分此字符串。 |
| 33       | [String[\] split(String regex, int limit)](http://www.runoob.com/java/java-string-split.html)根据匹配给定的正则表达式来拆分此字符串。 |
| 34       | [boolean startsWith(String prefix)](http://www.runoob.com/java/java-string-startswith.html)测试此字符串是否以指定的前缀开始。 |
| 35       | [boolean startsWith(String prefix, int toffset)](http://www.runoob.com/java/java-string-startswith.html)测试此字符串从指定索引开始的子字符串是否以指定前缀开始。 |
| 36       | [CharSequence subSequence(int beginIndex, int endIndex)](http://www.runoob.com/java/java-string-subsequence.html) 返回一个新的字符序列，它是此序列的一个子序列。 |
| 37       | [String substring(int beginIndex)](http://www.runoob.com/java/java-string-substring.html)返回一个新的字符串，它是此字符串的一个子字符串。 |
| 38       | [String substring(int beginIndex, int endIndex)](http://www.runoob.com/java/java-string-substring.html)返回一个新字符串，它是此字符串的一个子字符串。 |
| 39       | [char[\] toCharArray()](http://www.runoob.com/java/java-string-tochararray.html)将此字符串转换为一个新的字符数组。 |
| 40       | [String toLowerCase()](http://www.runoob.com/java/java-string-tolowercase.html)使用默认语言环境的规则将此 String 中的所有字符都转换为小写。 |
| 41       | [String toLowerCase(Locale locale)](http://www.runoob.com/java/java-string-tolowercase.html) 使用给定 Locale 的规则将此 String 中的所有字符都转换为小写。 |
| 42       | [String toString()](http://www.runoob.com/java/java-string-tostring.html) 返回此对象本身（它已经是一个字符串！）。 |
| 43       | [String toUpperCase()](http://www.runoob.com/java/java-string-touppercase.html)使用默认语言环境的规则将此 String 中的所有字符都转换为大写。 |
| 44       | [String toUpperCase(Locale locale)](http://www.runoob.com/java/java-string-touppercase.html)使用给定 Locale 的规则将此 String 中的所有字符都转换为大写。 |
| 45       | [String trim()](http://www.runoob.com/java/java-string-trim.html)返回字符串的副本，忽略前导空白和尾部空白。 |
| 46       | [static String valueOf(primitive data type x)](http://www.runoob.com/java/java-string-valueof.html)返回给定data type类型x参数的字符串表示形式。 					|

频繁的操作字符串：不建议使用String类，而是使用StringBuilder & StringBuffer

## 运算符

**1. 算术运算符** 
双目： * / %(取余、求模) + - 
单目： ++ – 
a 前缀（num++;）

```
int num = 4;
        ++num;
        int rs = ++num*2;
        System.out.println(rs);//?
```

b 后缀

```
int num = 4;
        int x = 3;
        num++;//5
        int rs = num++ +x; //rs = 8  num=6
        System.out.println(rs++);//?
        System.out.println(rs);
```

**2. 关系运算符**：true/false

> < >= <= ==(恒等于) != 
> 逻辑运算符：true/false 
> ！（取反） &&（短路且） ||（短路或）

```
int x=3,y=4,z=10;
boolean rs = x>y&&++z>10;// true / false
System.out.println(z);
```

算符 = 
**3. 赋值运算符** 
赋值运算符： = 
复合赋值运算符： += -= *= /= %=

```
int x = 5;
//      x = x + 10;
        x+=10;
        System.out.println(x);
```

**4. 三目运算符**： 
exp1 ? exp2 : exp3; 
exp1:true exp2; 
exp1:false exp3; 
**5. 位运算符**： 
~ 非 |或 &与 ^异或 
按位~运算符：10100101=01011010 
按位&运算符：1101&1010=1000 
按位|运算符： 1101 | 1010= 1111 
按位异或运算符：1101 ^ 1010 = 0111 
**6. 位移运算符** 
<< 3<<3=24 >> 
在数字没有溢出的前提下，对于正数和负数，左移一位都相当于乘以2的1次方，左移n位就相当于乘以2的n次方 
**7. 转义字符**： 

```
\n 换行 
\r 回车 
\t 水平制表 
\v 垂直 制表 
\” “ 
\’ ‘ 
\ \
```

# Java流程控制

## 介绍

Java流程控制包括**顺序控制、条件控制和循环控制**。

* 顺序控制，就是从头到尾依次执行每条语句操作。
* 条件控制，基于条件选择执行语句，比方说，如果条件成立，则执行操作A，或者如果条件成立，则执行操作A，反之则执行操作B。
* 循环控制，又称为回路控制，根据循环初始条件和终结要求，执行循环体内的操作。
* 分支结构:  顺序结构只能顺序执行，不能进行判断和选择，因此需要分支结构。

Java有两种分支结构：

- if语句

- switch语句

Java中有三种主要的循环结构：

- while循环

- do…while循环

- for循环

## 笔记

```java
package com.hgd.study2;

import java.util.Scanner;

/**
 * java的流程控制：顺序结构 分之机构 循环结构
 * 
 * @author HuTiger 顺序结构：通过debug模式可以看出java程序的运行时顺序结构的
 * 
 *         分支结构：if语句
 *
 */
public class ProcessControl {

    public static void main(String[] args) {
        // IfStudy();
        // SwitchCaseStudy();
        // WhileStudy();
    }

    /*
     * IF语句
     */
    private static void IfStudy() {

        /*
         * 根据条件表达的世界(true||false)来判断是否进入语句块 if(条件表达式){ 语句块 } 继续执行后面的语句
         */
        int i = 100;
        if (i > 60) {
            System.out.println(i);
        }
        System.out.println("后面需要执行的语句");

        /*
         * if else 语句
         */

        // system.in就是标准输入，可以获取从键盘输入的值
        // 通过scanner(jdk提供给我们的工具)来处理获取到的数据
        System.out.println("请输入分数!");
        Scanner sc = new Scanner(System.in);

        int j = sc.nextInt();// 把用户输入的数赋值给j
        System.out.println("控制台获取到的值是：" + j);

        if (j > 60) {
            System.out.println("通过");
        } else {
            System.out.println("没通过");
        }
        System.out.println("当if else 执行后需要执行的内容");

        /*
         * if else if else if ...else
         */
        // 场景：将一个5(score)分制分为 :5分的评价等级A 4==B 3==C 其他是D
        Scanner sca = new Scanner(System.in);
        int score = sca.nextInt();
        if (score >= 0 && score <= 5) {
            if (score == 5) {
                System.out.println("A");
            } else if (score == 4) {
                System.out.println("B");
            } else if (score == 3) {
                System.out.println("C");
            } else {
                System.out.println("D");
            }
        } else {
            System.out.println("输入不合法");
        }

        /*
         * 练习：百分制系统 90-100 优秀 75-89 良好 60-74 合格 其他 不合格
         */
        Scanner scan = new Scanner(System.in);
        int score1 = scan.nextInt();
        if (score1 >= 0 && score1 <= 100) {
            if (score1 >= 90 && score1 <= 100) {
                System.out.println("优秀");
            } else if (score1 >= 75) {
                System.out.println("良好");
            } else if (score1 >= 60) {
                System.out.println("合格");
            } else {
                System.out.println("不合格");
            }
        } else {
            System.out.println("输入不合法");
        }

    }

    /*
     * switch case
     */
    private static void SwitchCaseStudy() {

        /*
         * 分支语句：switch case key：需要比较的表达式 value：与表达式进行比较的值
         * 执行流程：如果key和value比较的结果为true，那么将执行case部分的代码，case部分可以有多个 类似于else if 部分
         * 区别是key和value之间的比较只能是== default部分是不满足以上任何去执行的代码 ，类似于else
         * 
         * switch 后面括号中的表达式的值必须是符合byte，char，short，int类型的常量表达式 jdk1.7以后可以使用string
         * ，而不能用浮点型或long类型
         * 
         * switch(key) { case value: break; default: break; }
         */

        // 场景：将一个5(score)分制分为 :5分的评价等级A 4==B 3==C 其他是D
        Scanner sc = new Scanner(System.in);
        int score = sc.nextInt();
        switch (score) {
        case 5:
            System.out.println("A");
            break;// 标准写法 break不能少 跳出当前语句块，如果没有break会直接进入下一个case语句
        case 4:
            System.out.println("B");
            break;
        case 3:
            System.out.println("C");
            break;
        default:
            System.out.println("D");
            break;
        }
    }

    /*
     * while 循环和do while
     */
    private static void WhileStudy() {

        /*
         * 执行流程： 当条件表达式为true的时候进入代码块，执行需要执行的代码 当条件表达式为false的时候执行后面的语句
         * 
         * while(条件表达式){ 需要执行的代码部分 } 后面的语句
         */

        /*
         * 场景：输入1-100的整数分析：给输出的值一个变量I，当i在1-100之间的时候输出
         */
        int i = 1;// 循环的起点
        while (i <= 100) {
            System.out.println(i);
            // 给出循环的步长
            i++;
        }
        System.out.println("打印结束");
        
        
        /*
         * do while
         * 
         * do{
         * }while(条件表达式)
         */
        
        int a=1;
        do{//语句块中的内容无论条件是否满足都会先执行一次
            System.out.println(a);
            a++;
        }
        while(a<=0);
        
    }

    /*
     * for循环
     */
    private static void ForStudy() {

        /*
         * 场景：打印1-100 int i=1是循环的起点 i++ 循环的补偿 i<=100 循环的条件
         */
        for (int i = 1; i <= 100; i++) {
            System.out.println(i);
        }
        System.out.println("打印结束");
    }
}
```

# Java 数组

数组对于每一门编程语言来说都是重要的数据结构之一，当然不同语言对数组的实现及处理也不尽相同。

Java 语言中提供的数组是用来存储固定大小的同类型元素。

你可以声明一个数组变量，如 numbers[100] 来代替直接声明 100 个独立变量 number0，number1，....，number99。

本教程将为大家介绍 Java 数组的声明、创建和初始化，并给出其对应的代码。

------

## 声明数组变量

首先必须声明数组变量，才能在程序中使用数组。下面是声明数组变量的语法：

dataType[] arrayRefVar;   // 首选的方法 或 dataType arrayRefVar[];  // 效果相同，但不是首选方法

**注意:** 建议使用 **dataType[] arrayRefVar** 的声明风格声明数组变量。 dataType arrayRefVar[] 风格是来自 C/C++ 语言 ，在Java中采用是为了让 C/C++ 程序员能够快速理解java语言。

实例

下面是这两种语法的代码示例：

double[] myList;         // 首选的方法 或 double myList[];         //  效果相同，但不是首选方法

------

## 创建数组

Java语言使用new操作符来创建数组，语法如下：

```
arrayRefVar = new dataType[arraySize];
```

上面的语法语句做了两件事：

- 一、使用 dataType[arraySize] 创建了一个数组。
- 二、把新创建的数组的引用赋值给变量 arrayRefVar。

数组变量的声明，和创建数组可以用一条语句完成，如下所示：

```
dataType[] arrayRefVar = new dataType[arraySize];
```

另外，你还可以使用如下的方式创建数组。

```
dataType[] arrayRefVar = {value0, value1, ..., valuek};
```

数组的元素是通过索引访问的。数组索引从 0 开始，所以索引值从 0 到 arrayRefVar.length-1。

实例

下面的语句首先声明了一个数组变量 myList，接着创建了一个包含 10 个 double 类型元素的数组，并且把它的引用赋值给 myList 变量。

TestArray.java 文件代码：

```java
public class TestArray {
   public static void main(String[] args) {
      // 数组大小
      int size = 10;
      // 定义数组
      double[] myList = new double[size];
      myList[0] = 5.6;
      myList[1] = 4.5;
      myList[2] = 3.3;
      myList[3] = 13.2;
      myList[4] = 4.0;
      myList[5] = 34.33;
      myList[6] = 34.0;
      myList[7] = 45.45;
      myList[8] = 99.993;
      myList[9] = 11123;
      // 计算所有元素的总和
      double total = 0;
      for (int i = 0; i < size; i++) {
         total += myList[i];
      }
      System.out.println("总和为： " + total);
   }
}
```

以上实例输出结果为：

```
总和为： 11367.373
```

下面的图片描绘了数组 myList。这里 myList 数组里有 10 个 double 元素，它的下标从 0 到 9。

![java数组结构说明](https://www.runoob.com/wp-content/uploads/2013/12/12-130Q0221Q5602.jpg)

------

## 处理数组

数组的元素类型和数组的大小都是确定的，所以当处理数组元素时候，我们通常使用基本循环或者 foreach 循环。

示例

该实例完整地展示了如何创建、初始化和操纵数组：

TestArray.java 文件代码：

```java
public class TestArray {
   public static void main(String[] args) {
      double[] myList = {1.9, 2.9, 3.4, 3.5};
 
      // 打印所有数组元素
      for (int i = 0; i < myList.length; i++) {
         System.out.println(myList[i] + " ");
      }
      // 计算所有元素的总和
      double total = 0;
      for (int i = 0; i < myList.length; i++) {
         total += myList[i];
      }
      System.out.println("Total is " + total);
      // 查找最大元素
      double max = myList[0];
      for (int i = 1; i < myList.length; i++) {
         if (myList[i] > max) max = myList[i];
      }
      System.out.println("Max is " + max);
   }
}
```

以上实例编译运行结果如下：

```
1.9
2.9
3.4
3.5
Total is 11.7
Max is 3.5
```

------

## foreach 循环

JDK 1.5 引进了一种新的循环类型，被称为 foreach 循环或者加强型循环，它能在不使用下标的情况下遍历数组。

示例

该实例用来显示数组myList中的所有元素：

TestArray.java 文件代码：

```java
public class TestArray {
   public static void main(String[] args) {
      double[] myList = {1.9, 2.9, 3.4, 3.5};
 
      // 打印所有数组元素
      for (double element: myList) {
         System.out.println(element);
      }
   }
}
```

以上实例编译运行结果如下：

```
1.9
2.9
3.4
3.5
```

------

## 数组作为函数的参数

数组可以作为参数传递给方法。

例如，下面的例子就是一个打印 int 数组中元素的方法:

```java
public static void printArray(int[] array) {
  for (int i = 0; i < array.length; i++) {
    System.out.print(array[i] + " ");
  }
}
```

下面例子调用 printArray 方法打印出 3，1，2，6，4 和 2：

printArray(new int[]{3, 1, 2, 6, 4, 2});

------

## 数组作为函数的返回值

```java
public static int[] reverse(int[] list) {
  int[] result = new int[list.length];
 
  for (int i = 0, j = result.length - 1; i < list.length; i++, j--) {
    result[j] = list[i];
  }
  return result;
}
```

以上实例中 result 数组作为函数的返回值。

------

## 多维数组

多维数组可以看成是数组的数组，比如二维数组就是一个特殊的一维数组，其每一个元素都是一个一维数组，例如：

String str[][] = new String[3][4];

### 多维数组的动态初始化（以二维数组为例）

1. 直接为每一维分配空间，格式如下：

type arrayName = new type[arraylenght1][arraylenght2];

type 可以为基本数据类型和复合数据类型，arraylenght1 和 arraylenght2 必须为正整数，arraylenght1 为行数，arraylenght2 为列数。

例如：

int a[][] = new int[2][3];

解析：

二维数组 a 可以看成一个两行三列的数组。

2. 从最高维开始，分别为每一维分配空间，例如：

```java
String s[][] = new String[2][];
s[0] = new String[2];
s[1] = new String[3];
s[0][0] = new String("Good");
s[0][1] = new String("Luck");
s[1][0] = new String("to");
s[1][1] = new String("you");
s[1][2] = new String("!");
```

解析：

**s[0]=new String[2]** 和 **s[1]=new String[3]** 是为最高维分配引用空间，也就是为最高维限制其能保存数据的最长的长度，然后再为其每个数组元素单独分配空间 **s0=new String("Good")** 等操作。

### 多维数组的引用（以二维数组为例）

对二维数组中的每个元素，引用方式为 **arrayName[index1][index2]**，例如：

num[1][0];

------

## Arrays 类

java.util.Arrays 类能方便地操作数组，它提供的所有方法都是静态的。

具有以下功能：

- 给数组赋值：通过 fill 方法。
- 对数组排序：通过 sort 方法,按升序。
- 比较数组：通过 equals 方法比较数组中元素值是否相等。
- 查找数组元素：通过 binarySearch 方法能对排序好的数组进行二分查找法操作。

具体说明请查看下表：

| 序号 | 方法和说明                                                   |
| ---- | ------------------------------------------------------------ |
| 1    | **public static int binarySearch(Object[] a, Object key)**用二分查找算法在给定数组中搜索给定值的对象(Byte,Int,double等)。数组在调用前必须排序好的。如果查找值包含在数组中，则返回搜索键的索引；否则返回 (-(*插入点*) - 1)。 |
| 2    | **public static boolean equals(long[] a, long[] a2)**如果两个指定的 long 型数组彼此*相等*，则返回 true。如果两个数组包含相同数量的元素，并且两个数组中的所有相应元素对都是相等的，则认为这两个数组是相等的。换句话说，如果两个数组以相同顺序包含相同的元素，则两个数组是相等的。同样的方法适用于所有的其他基本数据类型（Byte，short，Int等）。 |
| 3    | **public static void fill(int[] a, int val)**将指定的 int 值分配给指定 int 型数组指定范围中的每个元素。同样的方法适用于所有的其他基本数据类型（Byte，short，Int等）。 |
| 4    | **public static void sort(Object[] a)**对指定对象数组根据其元素的自然顺序进行升序排列。同样的方法适用于所有的其他基本数据类型（Byte，short，Int等）。 |

## 笔记列表

1. 数组倒序实例：

   ```
   public class Test2 {
       public static void main(String[] args){
           int[] test= {1,2,4,5,7};
           for (int i : test) {
               System.out.print(i+" ");
           }
           System.out.println("\n");
           test = Test2.reverse(test);
           for (int i : test) {
               System.out.print(i+" ");
           }
       }

       public static int[] reverse(int[] arr){
           int[] result = new int[arr.length];
           for (int i = 0,j=result.length-1; i < arr.length; i++,j--) {
               result[j] = arr[i];
           }
           return result;
       }
   }
   ```

2. ​

   ```
   // 声明二维数组：有两行，列数待定，数组结构 = { { }, { } }

   String s[][] = new String[2][]; 

   // 确定每行的元素个数，第一行有2个元素，第二行有3个元素，

   // 数组结构 = {{"E1", "E2"}, {"E1", "E2", "E3"}}

   s[0] = new String[2];

   s[1] = new String[3];
   ```

3. 实现数组和字符串的转换处理

   ```
   public class Test {
       public static void main(String args[]) {
           String str = "helloworld";
           char[] data = str.toCharArray();// 将字符串转为数组
           for (int x = 0; x < data.length; x++) {
               System.out.print(data[x] + "  ");
               data[x] -= 32;
               System.out.print(data[x] + "  ");
           }
           System.out.println(new String(data));
       }
   }
   ```

4. 冒泡排序

   ```
   public class BubbleSort {
   /**
    * N个数字要排序完成，总共进行N-1趟排序，每i趟的排序次数为(N-i)次，所以可以用双重循环语句，外层控制循环多少趟，内层控制每一趟的循环次数。
    * @param args
    */
       public static void main(String[] args) {
           int arr[] = {26,15,29,66,99,88,36,77,111,1,6,8,8};
           for(int i=0;i < arr.length-1;i++) {//外层循环控制排序趟数
               for(int j=0; j< arr.length-i-1;j++) {
                           //内层循环控制每一趟排序多少次
                   // 把小的值交换到前面
                   if (arr[j]>arr[j+1]) {
                       int temp = arr[j];
                       arr[j] = arr[j+1];
                       arr[j+1] = temp;
                   }
               }
               System.out.print("第"+(i+1)+"次排序结果：");
                                   //列举每次排序的数据
               for(int a=0;a<arr.length;a++) {
                   System.out.print(arr[a] + "\t");
               }
               System.out.println("");
           }
           System.out.println("最终排序结果：");
           for(int a = 0; a < arr.length;a++) {
               System.out.println(arr[a] + "\t");
           }
       }
   }
   ```

5. 选择排序：（比冒泡排序更快，运行次数更少）：

   ```
   public class Start
   {
       public static void main(String[] args)
       {
           int[] arr={20,60,51,81,285,12,165,51,81,318,186,9,70};
           for(int a:arr)
           {
               System.out.print(a+" ");
           }
           
           System.out.println("\n"+"---------------从大到小---------------");
           
           arr=toSmall(arr);
           for(int a:arr)
           {
               System.out.print(a+" ");
           }
           
           System.out.println("\n"+"---------------从小到大---------------");
           
           arr=toBig(arr);
           for(int a:arr)
           {
               System.out.print(a+" ");
           }
       }
   //    从大到小
       public static int[] toSmall(int[] arr)
       {
   //遍历数组里除最后一个的其他所有数，因为最后的对象没有与之可以相比较的数
           for(int i=0;i<arr.length-1;i++)
           {
   /*遍历数组里没有排序的所有数，并与上一个数进行比较
    *“k=i+1”因为自身一定等于自身，所以相比没有意义
    *而前面已经排好序的数，在比较也没有意义
    */
               for(int k=i+1;k<arr.length;k++)
               {
                   if(arr[k]<arr[i])//交换条件（排序条件）
                   {
                       int number=arr[i];
                       arr[i]=arr[k];
                       arr[k]=number;
                   }//交换
               }
           }
           return arr;
       }
   //    从小到大
   //和前面一样
       public static int[] toBig(int[] arr)
       {
           for(int i=0;i<arr.length-1;i++)
           {
               for(int k=i+1;k<arr.length;k++)
               {
                   if(arr[k]>arr[i])
                   {
                       int number=arr[i];
                       arr[i]=arr[k];
                       arr[k]=number;
                   }
               }
           }
           return arr;
       }
   }
   ```

   ​

6. java.util.Arrays类能方便地操作数组，它提供的所有方法都是静态的。具有以下功能：

   - ** 给数组赋值：通过fill方法。
   - ** 对数组排序：通过sort方法,按升序。
   - ** 比较数组：通过equals方法比较数组中元素值是否相等。
   - ** 查找数组元素：通过binarySearch方法能对排序好的数组进行二分查找法操作。

   ```
   import java.util.Arrays;

   public class TestArrays {
       public static void output(int[] array) {
           if (array != null) {
               for (int i = 0; i < array.length; i++) {
                   System.out.print(array[i] + " ");
               }
           }
           System.out.println();
       }

       public static void main(String[] args) {
           int[] array = new int[5];
           // 填充数组
           Arrays.fill(array, 5);
           System.out.println("填充数组：Arrays.fill(array, 5)：");
           TestArrays.output(array);
           // 将数组的第2和第3个元素赋值为8
           Arrays.fill(array, 2, 4, 8);
           System.out.println("将数组的第2和第3个元素赋值为8：Arrays.fill(array, 2, 4, 8)：");
           TestArrays.output(array);
           int[] array1 = { 7, 8, 3, 2, 12, 6, 3, 5, 4 };
           // 对数组的第2个到第6个进行排序进行排序
           Arrays.sort(array1, 2, 7);
           System.out.println("对数组的第2个到第6个元素进行排序进行排序：Arrays.sort(array,2,7)：");
           TestArrays.output(array1);
           // 对整个数组进行排序
           Arrays.sort(array1);
           System.out.println("对整个数组进行排序：Arrays.sort(array1)：");
           TestArrays.output(array1);
           // 比较数组元素是否相等
           System.out.println("比较数组元素是否相等:Arrays.equals(array, array1):" + "\n" + Arrays.equals(array, array1));
           int[] array2 = array1.clone();
           System.out.println("克隆后数组元素是否相等:Arrays.equals(array1, array2):" + "\n" + Arrays.equals(array1, array2));
           // 使用二分搜索算法查找指定元素所在的下标（必须是排序好的，否则结果不正确）
           Arrays.sort(array1);
           System.out.println("元素3在array1中的位置：Arrays.binarySearch(array1, 3)：" + "\n" + Arrays.binarySearch(array1, 3));
           // 如果不存在就返回负数
           System.out.println("元素9在array1中的位置：Arrays.binarySearch(array1, 9)：" + "\n" + Arrays.binarySearch(array1, 9));
       }
   }
   ```

   输出结果：

   ```
   填充数组：Arrays.fill(array, 5)：
   5 5 5 5 5 
   将数组的第2和第3个元素赋值为8：Arrays.fill(array, 2, 4, 8)：
   5 5 8 8 5 
   对数组的第2个到第6个元素进行排序进行排序：Arrays.sort(array,2,7)：
   7 8 2 3 3 6 12 5 4 
   对整个数组进行排序：Arrays.sort(array1)：
   2 3 3 4 5 6 7 8 12 
   比较数组元素是否相等:Arrays.equals(array, array1):
   false
   克隆后数组元素是否相等:Arrays.equals(array1, array2):
   true
   元素3在array1中的位置：Arrays.binarySearch(array1, 3)：
   1
   元素9在array1中的位置：Arrays.binarySearch(array1, 9)：
   -9
   ```

   # Java面向对象基础知识总结

## 面向对象

将跟对象有关的功能都封装在其内

面向对象三大特征：封装，继承，多态

找对象，创建对象，使用对象，并维护对象之间的关系

1.  类：对现实中事物的描述

2. 对象：就是实实在在 存在的事物映射到java中，描述就是class定义的类具体对象就是对应java在堆内存new建立的实体

3. 类与对象：设计图纸就是类，里面包含对象的描述：比如说车的颜色，轮胎数，发动机....

   > Note：对象建立的时候，属性值都会先置为null，显式初始化后才会变成具体的值



## 成员变量VS局部变量

根据定义变量位置的不同，可以将变量分为成员变量和局部变量

* 成员变量是在类范围内定义的变量
* 局部变量是在一个方法内定义的变量


成员变量可以分为：

* 实例属性 （不用static修饰） 随着实例属性的存在而存在
* 类属性 （static修饰）随着类的存在而存在


成员变量无需显式初始化，系统会自动对其进行默认初始化

局部变量可分为：

- 形参（形式参数）//在整个方法内有效
- 方法局部变量 （方法内定义）// 从定义这个变量开始到方法结束这一段时间内有效  
- 代码块局部变量 （代码块内定义）  //从定义这个变量开始到代码块结束这一段时间内有效

局部变量除了形参外，都必须显示初始化，也就是要指定一个初始值，否则不能访问。

示例：

```java
package object;

/
 * 成员变量和局部变量
 * 
 * */
public class VariableTest {
    
    //成员变量
    public static String staticname = "类属性";
    public String nostaticname = "实例属性";
    
    //没有初始化的成员变量
    public static String staticname1;
    public String nostaticname1;
    
    //定义一个方法
    public void info(){
        //在方法中定义一个局部变量i
        //int i;
        //直接输出是输出不出来的，因为没有初始化
        //System.out.println(i);
        //定义一个局部变量i并初始化
        int i = 10;
        //输出i
        System.out.println(i);
    }
    
    //定义了一个静态的方法
    public static void infos(){
        int i = 20;
        System.out.println(i);
    }
    
    public static void main(String[] args) {
        /*第一问：类属性和实例属性的范围一样吗？*/
        //在没创建实例之前 可以调用类属性,但不能调用实例属性
        System.out.println(VariableTest.staticname);//结果：类属性
        //实例化对象之后,就可以调用实例属性了
        VariableTest vt = new VariableTest();
        System.out.println(vt.nostaticname);//结果：实例属性
        /*--- 结论：在成员变量中，类属性的范围比实例属性大一点 ---*/
        
        System.out.println("----------");
        
        /*第二问：成员变量需要显性初始化吗？*/
        //直接调用没有初始化的类属性
        System.out.println(VariableTest.staticname1);//结果：null
        //用实例化对象调用没有初始化的实例属性
        System.out.println(vt.nostaticname1);//结果：null
        /*--- 结论：成员变量会自动隐性初始化，赋给变量一个默认值  ---*/
        
        System.out.println("----------");
        
        /*第三问：如果用实例化后的对象去调用类属性会怎么样？*/
        //vt.staticname; 
        //这样会报错
        //Syntax error, insert "VariableDeclarators" to complete LocalVariableDeclaration
        //翻译：语法错误,插入“变量声明符”来完成局部变量声明
        /*为什么会报错。一开始我以为是因为eclipse出错了
         *后来我直接用文本文档重写了一个test
         *编译文件后，报不是语句的错，然后我又试了一下
         *VariableTest.staticname
         *也是报错，说明这种写法是不正确的，具体为什么有待研究*/
        vt.staticname = "改变了的类属性";
        //如果同时给类属性赋值，就不会报错...有警告
        //The static field Variable Test.static name should be accessed in a static way
        //翻译：静态字段变量测试。静态的名字应该在一个静态方法访问
        System.out.println(vt.staticname);//结果：改变了的类属性
        //这样就不会报错，但是会有警告，同上↑
        /*结论：用实例化后的对象调用类属性，格式正确的情况下，是可以调用的，但有警告
         *通过对象调用类属性，同样可以改变类属性的值*/
        
        System.out.println("----------");
        
        //定义在方法中的局部变量
        /*第四问：定义在方法中的局部变量，出了方法还能访问吗？*/
        //调用方法
        vt.info();//结果：10
        //现在还能用info中的i吗？
        //System.out.println(i);
        //报错：i cannot be resolved to a variable
        //翻译：i 不能转换成一个变量
        /*结论：定义在方法中的局部变量，出了方法就不能被访问了*/
        
        System.out.println("----------");
        
        //定义在代码块中的局部变量
        /*第五问：定义在代码块中的局部变量，出了代码块还能访问吗？*/
        {
            int j = 11;
            System.out.println(j);//结果：11
        }
        //出了代码块
        //System.out.println(j);
        //同样报错，内容与上面的一样
        /*定义在代码块中的局部变量，出了代码块就不能访问了*/
        
        System.out.println("----------");
        
        //后续：一个静态方法
        infos();//结果：20
        //这样依然报错
        //System.out.println(i);
        
    }
}
```

java允许局部变量和成员变量重名，局部变量会覆盖成员变量的值

代码示例：

```java
package object;

/
 * 局部变量覆盖成员变量
 * */
public class VariableCover {
    
    /*当实例变量与方法中的局部变量同名时，
     *局部变量的值会覆盖实例变量*/
    //定义实例变量
    public String city = "合肥";
    private static String citys = "滁州";
    //定义一个方法
    public void function(){
        String city = "蚌埠";
        //输出
        //方法中的同名局部变量会覆盖实例变量
        System.out.println(city);//结果：蚌埠
        //要想调用实例变量，可以用this
        System.out.println(this.city);//结果：合肥
    }
    
    public static void main(String[] args) {
        String citys = "南京";
        //方法中的同名局部变量会覆盖实例变量
        System.out.println(citys);//结果：南京
        //可以通过类名调用被覆盖的类属性
        System.out.println(VariableCover.citys);//结果：滁州
        
        String city = "上海";
        System.out.println(city);//结果：上海
        //这样也可以
        System.out.println(new VariableCover().city);//结果：合肥
        new VariableCover().function();
    }
}
```

## 匿名对象：没有定义名称的对象

匿名对象的传值调用：调用结束后，在堆内存新生成的对象成为垃圾（无指向）,因此过一段时间就会被垃圾回收机制回收.

new Car().run(); 

## 封装

隐藏对象的属性和实现细节，仅仅提供公共访问方式；关键字：private 权限修饰符，只在本类中有效私有只是封装的一种表现形式；一般情况下，把属性都隐藏，提供公共访问方式访问；对访问的数据进行操作，提高代码的健壮性。

1. 对外提供公开的用于设置对象属性的public方法

​        设置set     获取get

2. 在set方法中加入逻辑判断，过滤掉非法数据。
3. 将所有的成员变量封装加上private，提供get、set方法

## 构造函数

函数名和类名一致，不能用return；对象一建立就会调用构造函数，可以用于对特定对象进行初始化；

若类中没有定义构造函数，系统会默认加入一个空参数的构造函数；构造函数也可以私有化，私有化后不能使用该函数创建对象；如果所有构造函数都私有化，那么就不能创建对象；一般方法是对象调用才运行，可以被调用多次；构造代码块：给所有对象的共性进行初始化，对象一建立就运行，优先于构造函数执行

  构造函数的最大作用就是创建对象时完成初始化，当我们在new一个对象并传入参数的时候，会自动调用构造函数并完成参数的初始化。

## this

代表当前调用对象（当变量前面加了this，该变量可以认为是成员变量）

当本类功能内部需要使用本类对象时，都用this来表示

## static

静态，修饰成员（包括变量和方法,不能修饰局部），表示共性数据可以被类名调用：类名.静态成员被所有对象共享，只占一块内存（方法区，共享区，数据区）随着类的加载而加载，随着类的消失而消失，生命周期最长优先于对象存在被所有对象所共享可以直接被类名调用（可以不创建对象）

String name；//成员变量，实例变量，随着对象的建立而存在

static String country = "CN";//静态变量，类变量

## 静态变量和成员变量的区别

1. 存放位置：

实例变量随着对象的建立存在于堆内存中，

类变量随着类的加载存在于方法区中

2. 生命周期：

实例变量随着对象消失而消失

类变量随着类消失而消失

3. 使用注意事项：

静态方法只能访问静态成员

静态方法中不可以定义this, super关键字（因为静态有限于对象存在）

主函数是静态的，作为程序入口，可以被jvm调用

* 利：    对共享数据进行单独空间存储，节省空间.

可以直接被类名调用

* 弊：    生命周期过长，访问出现局限性（只能访问静态）

public static void main(String[] args)解析：

/*

public：代表这该函数访问权限最大

static：代表主函数随着类的加载就已经存在

void：没有返回值

main:特殊单词，可以被jvm识别

String[] args:参数是一个数组，该数组中的元素是字符串

*/

静态变量：当对象中出现共享数据时

静态函数： 当功能内部没有访问到非静态数据时

若编译时，当前调用的class不存在时，会先去当前目录下找相应的java文件，如果有，则会直接编译，生成class文件

静态代码块：随着类的加载而执行，只执行一次，优先于主函数

用于给类进行初始化

构造代码块会执行多次；

运行顺序：静态代码块，构造代码块，构造函数



一个对象的建立过程：

Person p= new Person("zhangsan",20);

1. 找到Person.class文件并加载到内存中
2. 执行static代码块
3. 在堆内存中开辟空间，分配内存地址（main函数开始）
4. 在堆内存中建立对象的特有属性，并进行默认初始化
5. 对属性进行显示初始化
6. 对对象进行构造代码块初始化
7. 对对象进行对应的构造函数初始化
8. 将内存地址赋值给栈内存中的p变量

初始化过程：默认初始化，显式初始化，构造初始化

## 继承

将类的共有属性提取出来，将之变为超类，父类提高了代码复用性、让类与类之间产生了关系，因此有了多态的特性，只支持单继承，不支持多继承。

（容易带来安全隐患：当多个父类中定义了相同功能，但内容不同时，子类不确定执行哪个功能）

但是java保留了这种机制，并用另一种体现形式来完成表示(接口的多实现);java支持多层继承，爷爷类-父亲类-孙子类，也叫做继承体系;在具体调用时，只需创建最子类的对象；父类可能不能创建对象;创建子类对象可以使用更多功能，包括共有的和特有的;查阅父类功能，创建子类对象使用功能; 聚集，聚合，组合

1. 若子类和父类有同名变量：

子类访问本类中的变量，前面加this；

子类要访问父类的变量，前面加super；

若变量不同名，则this和super（如果父类中有该变量）指向同一个变量

若子类和父类中函数同名，则会使用子类的函数；父类的函数被覆盖（重写,overide）

沿袭父类功能，但是重写功能内容.

2. 子类方法覆盖父类方法条件：

静态只能覆盖静态

必须保证子类权限大于父类,（父类的权限不能为(private)）

public >默认权限>private

重载：只看参数列表

重写：两个方法需要一模一样(包括返回值，参数类型)



子类和父类的构造函数：绝对不能重写！

父类先于子类加载，因为在子类的所有构造函数之前都有一句隐世的super()（空参数的父类构造函数）；

父类中若有空参数的父类构造函数，子类中的构造函数可以不写super();

父类中若没有空参数的构造函数，则子类的每个构造函数第一句需要显式的写明super(XXX);

父类中的数据子类可以直接获取，子类对象在建立时，需要先查看父类是如何对这些数据进行初始化的；

因此子类在对象初始化时，要先访问父类中的构造函数。

this();或者super();都只能写在第一行，只能存在一个.

子类中至少有一个构造函数会访问父类中的构造函数；

extends Object（所有类的上帝，默认父类）

 

## final

修饰类，函数，变量

被修饰的类不可以被继承

被修饰的方法不可以被复写

被修饰的变量是一个常量，只能赋值一次，可以修饰成员变量和局部变量

所有字母都大写

修饰类：public final

## 抽象类和抽象方法

abstract class Student//抽象方法必须存在于抽象类中，不能用该类创建对象，因为没有意义

{

abstract void study();//抽象方法，内容待定，要被使用，必须有子类复写该方法

}

子类如果不覆盖所有的抽象方法，则子类还是一个抽象类

父类可以强制子类执行抽象方法；

抽象类和一般类：抽象类多了一些不确定的功能（抽象方法），需要子类具体执行

 

## 接口

Interface,不能创建对象

特点：

1.所以变量都是public static final

2.所有方法都是public abstract class interfaceTest implements Interface1

接口可以被类多实现，一个类中可以实现多个interface：因为多个接口的方法都没有主体；

一个类在继承一个父类的同时，可以实现多个接口；

接口之间可以继承，并且一个接口可以继承多个接口

接口的特点：降低了耦合性

 

## 多态

函数的多态体现：重载和覆盖

1. 多态的体现：

父类的引用指向了自己的子类对象

父类的引用也可以接受自己的子类对象

2. 多态的前提：

类与类有关系，要么是继承，要么是实现;

存在覆盖;

3. 多态的好处：

提高了程序的扩展性，但是只能使用父类的引用访问父类中的成员

4. 多态的应用:

多态中（非静态）成员函数的特点：

编译时期：参阅引用型变量所属的类中是否有调用的方法，如果有，则编译可以通过

运行时期：参阅对象所属类中是否有调用方法

Fu z = new zi();

编译时，看左边的Fu类

运行时，看右边的zi类

面试注意点：

多态中成员变量和（静态）成员函数的特点：

无论编译或运行，都参考左边

静态绑定，只看引用，只参考左边；

动态绑定，

如果每个子类每次都要调用父类中的共性方法，可以在主函数中或者一个类中创建一个共性方法，

参数以父类对象为准，调用的时候只需要将子类对象传入即可.

object类：所有类的直接或间接父类

## 内部类

在Java中，可以将一个类定义在另一个类里面或者一个方法里面，这样的类称为内部类。广泛意义上的内部类一般来说包括这四种：成员内部类、局部内部类、匿名内部类和静态内部类。

内部类不用建立对象就可以访问外部类的成员变量和函数，包括私有；外部类要访问内部类，必须建立内部类对象；建立在非所属类中时，需先建立外部类，Outer.Inner in = new Outer().new Inner();内部类可以私有

内部类访问外部类成员变量    Outer.this.x。

注意：当内部类中定义了静态成员，则该内部类必须也是静态的

当外部类中的静态方法访问内部类时，内部类也必须是静态的

局部内部类不能静态

内部类定义在局部时，不可以被成员修饰符修饰

可以直接访问外部类中的成员

但是不可以访问所在局部中的变量，只能访问被final修饰的局部变量

成员修饰符(Static,private…)只能修饰成员变量

匿名内部类：

前提，内部类必须是继承一个类或者实现接口

```java
abstract class Absdemo{    
	abstract void show();
}
		class Outer{
			...
		public void function(){
			new Absdemo()//这是一个Absdemo的一个匿名子类对象
							{
			void show(){
				System.out.println("匿名内部类！")；
						}
					}.show();
				}
...
}
```

 

格式：new 父类或者接口(){定义子类内容}；

其实匿名内部类是一个匿名子类对象，而且这个对象是带有内容的

匿名内部类中定义的方法最好不要超过3个(方法比较多的话就直接创建一个有名字的子类)

内部类参考: https://www.cnblogs.com/dolphin0520/p/3811445.html

# Java常用类——Object的通用方法

Java类层次结构的顶层是`Object`类，所有的其他类都隐式的继承于它。因此，所有的类也都从`Object`中继承了方法，其中最重要的几个方法如下表：

| 方法                                                         | 描述                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| `protected Object clone()`                                   | 创建并返回当前对象的一份拷贝                                 |
| `protected void finalize()`                                  | 当垃圾回收器判断出该对象不再被引用时，就会调用finalize()方法。在[对象的创建与销毁](https://segmentfault.com/a/1190000004390247)中有对finalizers的介绍。 |
| `boolean equals(Object obj)`                                 | 判断另外一个对象是否与当前对象相等                           |
| `int hasCode()`                                              | 返回当前对象的哈希值                                         |
| `String toString()`                                          | 返回一个表示当前对象的字符串                                 |
| `void notify()`                                              | 唤醒一个等待当前对象的锁监视器的线程。我们将会在第9篇文章并发最佳实践中详细介绍此方法 |
| `void notifyAll()`                                           | 唤醒所有等待当前对象的锁监视器的线程。我们将会在第9篇文章并发最佳实践中详细介绍此方法 |
| `void wait()` `void wait(long timeout)` `void wait(long timeout, int nanos)` | 使当前线程进入等待状态直到其他线程调用了当前对象的`notify()`或`notifyAll()`方法。我们将会在第9篇文章并发最佳实践中详细介绍此方法 |

表1

重点介绍`equals`、`hashCode`、`toString`和`clone`方法。

## equlas和hashCode方法

默认情况下，Java 中任何两个对象引用(或类实例引用)只有指向相同的内存地址时才认为是相等的(引用相等)。但是Java允许通过重载`Object`的`equals()`方法给类自定义判等规则。听起来这是个很强大的概念，然而在适当的`equals()`方法实现需要满足以下几个规则限制：

- 自反性：对象x必须与其自身相等，equals(x)返回true
- 对称性：如果equals(y)为true，则y.equals(x)也要返回true
- 传递性：如果equals(y)为true，并且y.equals(z)也为true，则x.equals(z)也要为true
- 一致性：多次调用equals()方法应该返回相同值，除非对用于判等的任何一个属性进行了修改
- 与null判等：equals(null)总是要返回false

不幸的是Java编译器并不会在编译时对以上规则进行检查。然而，不遵守上述规则时可能会引入非常怪异并难以解决的问题。通用的建议是：如果需要重写`equals()`方法，请至少思考两次重写的必要性。遵循以上规则，我们为`Person`类重写一个简单的`equals()`实现。

```java
package com.javacodegeeks.advanced.objects;

public class Person {
    private final String firstName;
    private final String lastName;
    private final String email;
    
    public Person( final String firstName, final String lastName, final String email ) {
        this.firstName = firstName;
        this.lastName = lastName;
        this.email = email;
    }
    
    public String getEmail() {
        return email;
    }
    
    public String getFirstName() {
        return firstName;
    }
    
    public String getLastName() {
        return lastName;
    }

    // Step 0: Please add the @Override annotation, it will ensure that your
    // intention is to change the default implementation.
    @Override
    public boolean equals( Object obj ) {
        // Step 1: Check if the 'obj' is null
        if ( obj == null ) {
            return false;
        }
        
        // Step 2: Check if the 'obj' is pointing to the this instance
        if ( this == obj ) {
            return true;
        }
        
        // Step 3: Check classes equality. Note of caution here: please do not use the 
        // 'instanceof' operator unless class is declared as final. It may cause 
        // an issues within class hierarchies.
        if ( getClass() != obj.getClass() ) {
            return false;
        }
        
        // Step 4: Check individual fields equality
        final Person other = (Person) obj;
        if ( email == null ) {
            if ( other.email != null ) {
                return false;
            } 
        } else if( !email.equals( other.email ) ) {
            return false;
        }
        
        if ( firstName == null ) {
            if ( other.firstName != null ) {
                return false;
            } 
        } else if ( !firstName.equals( other.firstName ) ) {
            return false;
        }
            
        if ( lastName == null ) {
            if ( other.lastName != null ) {
                return false;
            }
        } else if ( !lastName.equals( other.lastName ) ) {
            return false;
        }
        
        return true;
    }        
}
```

在此部分介绍`hashCode()`方法并不是偶然的，至少要记住下面这条规则：任何时候重载`equals()`方法时，需要一并重载`hashCode()`方法。如果两个对象通过`equals()`方法判等时返回true，则每个对象的`hashCode()`方法需要返回相同的整数值（反过来并没有限制：如果两个对象通过`equals()`方法返回false，则`hashCode()`方法可以返回相同或不同的整数值）。下面看一下`Person`类的`hashCode()`方法：

```java
// Please add the @Override annotation, it will ensure that your
// intention is to change the default implementation.
@Override
public int hashCode() {
    final int prime = 31;
        
    int result = 1;
    result = prime * result + ( ( email == null ) ? 0 : email.hashCode() );
    result = prime * result + ( ( firstName == null ) ? 0 : firstName.hashCode() );
    result = prime * result + ( ( lastName == null ) ? 0 : lastName.hashCode() );
        
    return result;
}      
```

为了避免得到不可预期的结果，尽可能在实现`equals()`和`hashCode()`方法时使用`final`字段，从而保证方法的结果不会受到字段变化的影响(尽管真实场景中未必发生)。

最后，要确保在实现`equals()`和`hashCode()`方法是使用相同的字段，以确保在不可预期的字段调整时保证这两个方法行为的一致性。

## toString方法

`toString()`是最让人感兴趣的方法，并且被重载的频率也更高。此方法的目的是提供对象(类实例)的字符串表现。如果对`toString()`方法重载恰当，能极大的简化debug难度和分析解决问题的过程。

默认情况下，`toString()`的结果仅仅返回以`@`符分隔的全类名与对象哈希值串，然而这个结果在大多场景下并没什么用途。如下：

```
com.javacodegeeks.advanced.objects.Person@6104e2ee
```

我们来通过重写Person和`toString()`方法以使其输出更有用，下面是其中一种实例：

```java
// Please add the @Override annotation, it will ensure that your
// intention is to change the default implementation.
@Override
public String toString() {
    return String.format( "%s[email=%s, first name=%s, last name=%s]", 
        getClass().getSimpleName(), email, firstName, lastName );
}
```

现在我们在`toString()`方法中包含了Person的所有字段，然后执行下面的代码片段：

```java
final Person person = new Person( "John", "Smith", "john.smith@domain.com" );
System.out.println( person.toString() );
```

控制台中将输出以下结果：

```java
Person[email=john.smith@domain.com, first name=John, last name=Smith]
```

遗憾的是在Java标准库中对`toString()`方法实现的支持有限，不过还是有几个有用的方法：`Objects.toString()`, `Arrays.toString() / Arrays.deepToString()`。下面看一下`Office`类以及其`toString()`的实现。

```java
package com.javacodegeeks.advanced.objects;

import java.util.Arrays;

public class Office {
    private Person[] persons;

    public Office( Person ... persons ) {
         this.persons = Arrays.copyOf( persons, persons.length );
    }
    
    @Override
    public String toString() {
        return String.format( "%s{persons=%s}", 
            getClass().getSimpleName(), Arrays.toString( persons ) );
    }
    
    public Person[] getPersons() {
        return persons;
    }
}
```

相应的控制台输出如下(同时也有`Person`实例的字符串值)：

```
Office{persons=[Person[email=john.smith@domain.com, first name=John, last name=Smith]]}
```

Java社区实例了大量有用的类库以简化`toString()`的实现。其中广泛使用的有`Google Guava`的[Objects.toStringHelper](http://docs.guava-libraries.googlecode.com/git/javadoc/com/google/common/base/Objects.ToStringHelper.html)和`Apache Commons Lang`的[ToStringBuilder](http://commons.apache.org/proper/commons-lang/apidocs/org/apache/commons/lang3/builder/ToStringBuilder.html)

## clone方法

如果举出Java中最声名狼藉的方法，当属`clone()`无疑。`clone()`方法的目的很简单——返回对象实例的拷贝，然而有一堆理由可证明其使用并不像听起来那么轻而易举。

首先，实现自定义的`clone()`方法时需要遵守[Java文档](http://docs.oracle.com/javase/7/docs/api/java/lang/Object.html#clone())中列出的一系列约定。其次，在`Object`类中`clone()`方法被声明为`protected`，所以为了提高方法的可见性，在重载时需要声明为`public`并把返回值类型调整为重载类自身类型。再次，重载类需要实现`Cloneable`接口(尽管该接口作为一种声明，并未提供任何方法定义)，否则将会抛出`CloneNotSupportedException`异常。最后，在实现`clone()`方法时要先调用`super.clone()`然后再执行其他需要的动作。下面看一下`Person`类中的实现：

```java
public class Person implements Cloneable {
    // Please add the @Override annotation, it will ensure that your
    // intention is to change the default implementation.
    @Override
    public Person clone() throws CloneNotSupportedException {
        return ( Person )super.clone();
    }
}
```

上面的实现看起来简单直接，然而却隐藏着错误。当类实例的clone动作被执行时，未调用任何构造方法，后果将导致预料外的数据泄露。下面再看下`Office`类中的定义：

```java
package com.javacodegeeks.advanced.objects;

import java.util.Arrays;

public class Office implements Cloneable {
    private Person[] persons;

    public Office( Person ... persons ) {
         this.persons = Arrays.copyOf( persons, persons.length );
    }

    @Override
    public Office clone() throws CloneNotSupportedException {
        return ( Office )super.clone();
    }
    
    public Person[] getPersons() {
        return persons;
    }
}
```

在这个实现中，`Office`实例克隆出来的所有对象都将共享相同的person数组，然而这并不是我们预期的行为。为了让`clone()`实现正确的行为，我们还要做一些额外的工作：

```java
@Override
public Office clone() throws CloneNotSupportedException {
    final Office clone = ( Office )super.clone();
    clone.persons = persons.clone();
    return clone;
}
```

看起来是正确了，但如果对persons字段声明为`final`就将破坏这种正确性，因此`final`字段不能被重新赋值，从而导致数据再次被共享。

总之，当需要类实例的拷贝时，尽可能避免使用`clone()` / `Cloneable`，相反可以选择其他更简单的替代方案(例如：C++程序员熟悉的复制构造方法，或者工厂方法——在[对象的创建与销毁](https://segmentfault.com/a/1190000004390247)中讨论过的一种有用的构造模式)。

## equals方法与"=="操作符

在Java中，`==`操作符与equals()方法有种奇怪的关系，却会引入大量的问题与困惑。大多数情况下(除比较基本数据类型)，==操作符执行的是引用相等：只要两个引用指向同一个对象时为true，否则返回false。下面举例说明二者的区别：

```java
final String str1 = new String( "bbb" );
System.out.println( "Using == operator: " + ( str1 == "bbb" ) );
System.out.println( "Using equals() method: " + str1.equals( "bbb" ) );
```

从我们人类的视角来看，str1 == "bbb" 和 str1.equals("bbb")并无区别：str1仅仅是"bbb"的一个引用，所以结果应该是相同的；但对于Java来说却不尽然：

```
Using == operator: false
Using equals() method: true
```

尽管两个字符串看起来完全一样，但事实上却是两个不同的`String`实例。作为建议，在处理对象引用时要使用`equals()`或`Objects.equals()`进行判等，除非你真的是要判断两个引用是否指向同一个实例。

## 有用的帮助类

从Java 7发布以来，一批有用的帮助类加入到了标准Java库中，`Objects`便是其中之一。具体来说，以下三个方法可以简化你的`equals()`和`hashCode()`方法实现。

| 方法                                        | 描述                                            |
| ------------------------------------------- | ----------------------------------------------- |
| `static boolean equals(Object a, Object b)` | 当参数中的两个对象相等时返回true，否则返回false |
| `static int hash(Object...values)`          | 为参数列表生成哈希值                            |
| `static int hashCode(Object o)`             | 为非null参数生成哈希值，如果参数为null返回0     |

如果使用上面的方法来重写`Person`的`equals()`和`hashCode()`实现，代码量将会大大缩减，同时代码的可读性也将大大增强。

```java
@Override
public boolean equals( Object obj ) {
    if ( obj == null ) {
        return false;
    }
        
    if ( this == obj ) {
        return true;
    }
        
    if ( getClass() != obj.getClass() ) {
        return false;
    }
        
    final PersonObjects other = (PersonObjects) obj;
    if( !Objects.equals( email, other.email ) ) {
        return false;
    } else if( !Objects.equals( firstName, other.firstName ) ) {
        return false;            
    } else if( !Objects.equals( lastName, other.lastName ) ) {
        return false;            
    }
        
    return true;
}
        
@Override
public int hashCode() {
    return Objects.hash( email, firstName, lastName );
}      
```

# Java API ——StringBuffer类

## StringBuffer类概述

　　1）我们如果对字符串进行拼接操作，每次拼接，都会构建一个新的String对象，既耗时，又浪费空间。而StringBuffer就可以解决这个问题

　　2）线程安全的可变字符序列

　　3）StringBuffer和String的区别

　　　　· 前者长度和内容可变，后者不可变。

​        　 · 如果使用前者做字符串的拼接，不会浪费太多的资源。

 

## 构造方法

​     StringBuffer 方法

以下是 StringBuffer 类支持的主要方法：

| 序号 | 方法描述                                                     |
| ---- | ------------------------------------------------------------ |
| 1    | public StringBuffer append(String s)将指定的字符串追加到此字符序列。 |
| 2    | public StringBuffer reverse() 将此字符序列用其反转形式取代。 |
| 3    | public delete(int start, int end)移除此序列的子字符串中的字符。 |
| 4    | public insert(int offset, int i)将 `int` 参数的字符串表示形式插入此序列中。 |
| 5    | replace(int start, int end, String str)使用给定 `String` 中的字符替换此序列的子字符串中的字符。 |

下面的列表里的方法和 String 类的方法类似：

| 序号 | 方法描述                                                     |
| ---- | ------------------------------------------------------------ |
| 1    | int capacity()返回当前容量。                                 |
| 2    | char charAt(int index)返回此序列中指定索引处的 `char` 值。   |
| 3    | void ensureCapacity(int minimumCapacity)确保容量至少等于指定的最小值。 |
| 4    | void getChars(int srcBegin, int srcEnd, char[] dst, int dstBegin)将字符从此序列复制到目标字符数组 `dst`。 |
| 5    | int indexOf(String str)返回第一次出现的指定子字符串在该字符串中的索引。 |
| 6    | int indexOf(String str, int fromIndex)从指定的索引处开始，返回第一次出现的指定子字符串在该字符串中的索引。 |
| 7    | int lastIndexOf(String str)返回最右边出现的指定子字符串在此字符串中的索引。 |
| 8    | int lastIndexOf(String str, int fromIndex)返回 String 对象中子字符串最后出现的位置。 |
| 9    | int length() 返回长度（字符数）。                            |
| 10   | void setCharAt(int index, char ch)将给定索引处的字符设置为 `ch`。 |
| 11   | void setLength(int newLength)设置字符序列的长度。            |
| 12   | CharSequence subSequence(int start, int end)返回一个新的字符序列，该字符序列是此序列的子序列。 |
| 13   | String substring(int start)返回一个新的 `String`，它包含此字符序列当前所包含的字符子序列。 |
| 14   | String substring(int start, int end)返回一个新的 `String`，它包含此序列当前所包含的字符子序列。 |
| 15   | String toString()返回此序列中数据的字符串表示形式。          |



```java
public class StringBufferDemo01 {
    public static void main(String[] args) {
        // public StringBuffer():无参构造方法
        StringBuffer sb = new StringBuffer();
        System.out.println("sb:"+sb);
        System.out.println("sb.capacity:"+sb.capacity()); //16
        System.out.println("sb.length:"+sb.length()); //0
        System.out.println("--------------------------");
        // public StringBuffer(int capacity):指定容量的字符串缓冲区对象
        StringBuffer sb2 = new StringBuffer(50);
        System.out.println("sb2:"+sb2);
        System.out.println("sb2.capacity:"+sb2.capacity()); //50
        System.out.println("sb2.length:"+sb2.length()); //0
        System.out.println("--------------------------");
        // public StringBuffer(String str):指定字符串内容的字符串缓冲区对象
        StringBuffer sb3 = new StringBuffer("hello");
        System.out.println("sb3:"+sb3); //"hello"
        System.out.println("sb3.capacity:"+sb3.capacity()); //21
        System.out.println("sb3.length:"+sb3.length());//5
        System.out.println("--------------------------");
    }
}
```



注意返回值，可以查看源码，默认空间是16。



```java
/
     * Constructs a string buffer with no characters in it and an
     * initial capacity of 16 characters.
     */
    public StringBuffer() {
        super(16);
    }
    /
     * Constructs a string buffer with no characters in it and
     * the specified initial capacity.
     *
     * @param      capacity  the initial capacity.
     * @exception  NegativeArraySizeException  if the <code>capacity</code>
     *               argument is less than <code>0</code>.
     */
    public StringBuffer(int capacity) {
        super(capacity);
    }
    /
     * Constructs a string buffer initialized to the contents of the
     * specified string. The initial capacity of the string buffer is
     * <code>16</code> plus the length of the string argument.
     *
     * @param   str   the initial contents of the buffer.
     * @exception NullPointerException if <code>str</code> is <code>null</code>
     */
    public StringBuffer(String str) {
        super(str.length() + 16);
        append(str);
    }
```



 

## StringBuffer类的成员方法

### 添加功能

​            · public StringBuffer append(String str)：可以把任意类型数据添加到字符串缓冲区里面，并返回字符串缓冲区本身

​            · public StringBuffer insert(int offset,String str)：在指定位置把任意类型的数据插入到字符串缓冲区里面，并返回字符串缓冲区本身



```java
public class StringBufferDemo02 {
    public static void main(String[] args) {
        // 创建字符串缓冲区对象
        StringBuffer sb = new StringBuffer();
        //返回对象本身
        StringBuffer sb2 = sb.append("hello");
        System.out.println("sb:"+sb); //sb:hello
        System.out.println("sb2:"+sb2); //sb2:hello
        System.out.println(sb == sb2); //true
        //一步一步的添加数据
        StringBuffer sb3 = new StringBuffer();
        sb3.append("hello");
        sb3.append(true);
        sb3.append(12);
        sb3.append(34.56);
        System.out.println("sb3:"+sb3); //sb3:hellotrue1234.56
        //链式编程
        StringBuffer sb4 = new StringBuffer();
        sb4.append("hello").append(true).append(12).append(34.56);
        System.out.println("sb4:"+sb4); //sb4:hellotrue1234.56
        //StringBuffer insert(int offset,Stringstr):插入数据
        sb3.insert(5,"hello");
        System.out.println("sb3:"+sb3); //sb3:hellohellotrue1234.56
    }
}
```



### 删除功能

​            · public StringBuffer deleteCharAt(int index)：删除指定位置的字符，并返回本身

​            · public StringBuffer delete(int start,int end)：删除从指定位置开始指定位置结束的内容，并返回本身



```java
public class StringBufferDemo03 {
    public static void main(String[] args) {
        // 创建对象
        StringBuffer sb1 = new StringBuffer();
        // 创建对象
        sb1.append("hello").append("world").append("java");
        System.out.println("sb1:"+sb1); //sb1:helloworldjava
        // public StringBuffer deleteCharAt(int index):删除指定位置的字符，并返回本身
        // 需求：我要删除e这个字符
        sb1.deleteCharAt(1);
        // 需求:我要删除第一个l这个字符
        sb1.deleteCharAt(1);
        System.out.println("sb1:"+sb1);  //sb1:hloworldjava
        System.out.println("----------------");
        // public StringBuffer delete(int start,int end):删除从指定位置开始指定位置结束的内容，并返回本身
        StringBuffer sb2 = new StringBuffer("Hello World Java");
        System.out.println("sb2:"+sb2); //sb2:Hello World Java
        // 需求：我要删除World这个字符串
        sb2.delete(5,11);
        System.out.println("sb2:"+sb2); //sb2:Hello Java
        // 需求:我要删除所有的数据
        sb2.delete(0, sb2.length());
        System.out.println("sb2:"+sb2); //sb2:
    }
}
```



### 替换功能

​            · public StringBuffer replace(int start,int end,String str)：使用给定String中的字符替换词序列的子字符串中的字符



```java
public class StringBufferDemo04 {
    public static void main(String[] args) {
        // 创建字符串缓冲区对象
        StringBuffer sb = new StringBuffer();
        // 添加数据
        sb.append("hello");
        sb.append("world");
        sb.append("java");
        System.out.println("sb:" + sb); //sb:helloworldjava
        // public StringBuffer replace(int start,int end,String str):从start开始到end用str替换
        // 需求：我要把world这个数据替换为"节日快乐"
        sb.replace(5,10,"节日快乐");
        System.out.println("sb:"+sb); //sb:hello节日快乐java
    }
}
```



### 反转功能  

​            · public StringBuffer reverse()：将此字符序列用其反转形式取代，返回对象本身



```java
public class StringBufferDemo05 {
    public static void main(String[] args) {
        //创建字符串缓冲区对象
        StringBuffer sb = new StringBuffer();
        //添加数据
        sb.append("林青霞爱我");
        System.out.println("sb:"+sb); //sb:林青霞爱我
        //public StringBuffer reverse()
        sb.reverse();
        System.out.println("sb:"+sb); //sb:我爱霞青林
    }
}
```



### 截取功能

​            · public String substring(int start)：返回一个新的String，它包含此字符序列当前所包含的字符子序列

​            · public String substring(int start,int end)：返回一个新的String，它包含此序列当前所包含的字符子序列

​        注意：截取功能和前面几个功能的不同

​            · 返回值类型是String类型，本身没有发生改变



```java
public class StringBufferDemo06 {
    public static void main(String[] args) {
        //创建字符串缓冲区对象
        StringBuffer sb = new StringBuffer();
        sb.append("hello").append("world").append("java");
        System.out.println("sb:"+sb); //sb:helloworldjava
        //截取功能
        String s = sb.substring(5);
        System.out.println("s:"+s); //s:worldjava
        System.out.println("sb:"+sb); //sb:helloworldjava
        String ss = sb.substring(5,10);
        System.out.println("ss:"+ss); //ss:world
        System.out.println("sb:"+sb); //sb:helloworldjava
    }
}
```



 

## String与StringBuffer的相互转换



```java
public class StringBufferDemo07 {
    public static void main(String[] args) {
        //String --> StringBuffer
        String s = "hello";
        // 注意：不能把字符串的值直接赋值给StringBuffer
        // StringBuffer sb = "hello";
        // StringBuffer sb = s;
        //方式一：通过构造方法
        StringBuffer sb = new StringBuffer(s);
        //方式二：通过append方法
        StringBuffer sb2 = new StringBuffer();
        sb2.append(s);
        System.out.println("sb:"+sb); //sb:hello
        System.out.println("sb2:"+sb2); //sb2:hello
        System.out.println("-------------------------");
        //StringBuffer --> String
         StringBuffer buffer = new StringBuffer("java");
        //方式一：通过构造方法
        String str = new String(buffer);
        //方式二：通过toString()方法
        String str2 = buffer.toString();
        System.out.println("str:"+str); //str:java
        System.out.println("str2:"+str2); //str2:java
    }
}
```



## 把数组拼接成一个字符串



```java
public class StringBufferDemo08 {
    public static void main(String[] args) {
        //定义一个数组
        int[] arr = {44,33,55,11,22};
        //定义功能
        //方式一：用String做拼接的方式
        String result1 = arrayToString1(arr);
        System.out.println("result1:"+result1); //result1:[44,33,55,11,22]
        //方式二：用StringBuffer做拼接的方式
        String result2 = arrayToString2(arr);
        System.out.println("result2:"+result2); //result2:[44,33,55,11,22]
    }
    public static String arrayToString1(int[] arr){
        String s = "";
        s += "[";
        for(int x = 0; x < arr.length; x++){
            if (x == arr.length - 1){
                s += arr[x];
            }else{
                s += arr[x];
                s += ',';
            }
        }
        s += ']';
        return s;
    }
    public static String arrayToString2(int[] arr){
        StringBuffer sb = new StringBuffer();
        sb.append("[");
        for(int x = 0; x < arr.length; x++){
            if (x == arr.length-1){
                sb.append(arr[x]);
            }else{
                sb.append(arr[x]).append(",");
            }
        }
        sb.append("]");
        return sb.toString();
    }
}
```



## 把字符串反转



```java
public class StringBufferDemo09 {
    public static void main(String[] args) {
        String s = "I love Java";
        String result1 = myReverse1(s);
        System.out.println("result1:"+result1); //result1:avaJ evol I
        String result2 = myReverse2(s);
        System.out.println("result2:"+result2); //result2:avaJ evol I
    }
    public static String myReverse1(String s){
        String result = "";
        char[] ch = s.toCharArray();
        for(int x = s.length()-1; x >= 0; x--){
            result += ch[x];
        }
        return result;
    }
    public static String myReverse2(String s){
        return new StringBuffer(s).reverse().toString();
    }
}
```



## 判断一个字符串是否是对称的



```java
public class StringBufferDemo10 {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        System.out.println("请输入一个字符串：");
        String str = sc.nextLine();  //abcba
        boolean result1 = isSymmertrical1(str);
        System.out.println("result1:"+result1); //result1:true
        boolean result2 = isSymmertrical2(str); //result2:true
        System.out.println("result2:"+result2);
    }
    public static boolean isSymmertrical1(String s){
        boolean flag = true;
        char ch[] = s.toCharArray();
        for(int start = 0, end = ch.length-1; start <= end;start++,end--){
            if (ch[start] != ch[end]){
                flag = false;
                break;
            }
        }
        return flag;
    }
    public static boolean isSymmertrical2(String s){
        return new StringBuffer(s).reverse().toString().equals(s);
    }
}
```



 

## 了解一下StringBuilder类

​        一个可变的字符序列。此类提供一个与 `StringBuffer` 兼容的 API，但不保证同步。该类被设计用作 `StringBuffer` 的一个简易替换，用在字符串缓冲区被单个线程使用的时候（这种情况很普遍）。如果可能，建议优先采用该类，因为在大多数实现中，它比 `StringBuffer` 要快。

​        只要将StringBuffer的功能替换到StringBuilder就可以了。

## String,StringBuffer,StringBuilder的区别

　　1）String是内容不可变的，而StringBuffer,StringBuilder都是内容可变的。

　　2）StringBuffer是同步的，数据安全,效率低;StringBuilder是不同步的,数据不安全,效率高

 

10、StringBuffer和数组的区别？

​            A：二者都可以看出是一个容器，装其他的数据。

​            B：但是呢，StringBuffer的数据最终是一个字符串数据。

​            C：而数组可以放置多种数据，但必须是同一种数据类型的。

11、

​        A：String作为参数传递

​        B：StringBuffer作为参数传递

​        形式参数：

　　　　　　基本类型：形式参数的改变不影响实际参数

　　　　　　引用类型：形式参数的改变直接影响实际参数

​        注意：

　　　　String作为参数传递，效果和基本类型作为参数传递是一样的。



```java
public class StringBufferDemo11 {
    public static void main(String[] args) {
        String s1 = "hello";
        String s2 = "world";
        System.out.println(s1 + "---" + s2);//hello---world
        change(s1, s2);
        System.out.println(s1 + "---" + s2);//hello---world
        StringBuffer sb1 = new StringBuffer("hello");
        StringBuffer sb2 = new StringBuffer("world");
        System.out.println(sb1 + "---" + sb2);//hello---world
        change(sb1, sb2);
        System.out.println(sb1 + "---" + sb2);//hello---worldworld
    }
    //Stringz作为形参传递不会改变实参
    public static void change(String s1, String s2) {
        s1 = s2;
        s2 = s1 + s2;
    }
    //StringBuffer作为形参，如果直接赋值则不会影响实参，但是如果是使用方法改变形参则会影响实参
    public static void change(StringBuffer sb1, StringBuffer sb2) {
        sb1 = sb2;
        sb2.append(sb1);
    }
}
```

# Java 常用类——String类
String类在java.lang包中，java使用String类创建一个字符串变量，字符串变量属于对象。java把String类声明的final类，不能有类。String类对象创建后不能修改，由0或多个字符组成，包含在一对双引号之间。

下面是 String 类支持的方法，更多详细，参看 [Java String API](http://www.runoob.com/manual/jdk1.6/java/lang/String.html) 文档:

| SN(序号) | 方法描述                                                     |
| -------- | ------------------------------------------------------------ |
| 1        | [char charAt(int index)](http://www.runoob.com/java/java-string-charat.html)返回指定索引处的 char 值。 |
| 2        | [int compareTo(Object o)](http://www.runoob.com/java/java-string-compareto.html)把这个字符串和另一个对象比较。 |
| 3        | [int compareTo(String anotherString)](http://www.runoob.com/java/java-string-compareto.html)按字典顺序比较两个字符串。 |
| 4        | [int compareToIgnoreCase(String str)](http://www.runoob.com/java/java-string-comparetoignorecase.html)按字典顺序比较两个字符串，不考虑大小写。 |
| 5        | [String concat(String str)](http://www.runoob.com/java/java-string-concat.html)将指定字符串连接到此字符串的结尾。 |
| 6        | [boolean contentEquals(StringBuffer sb)](http://www.runoob.com/java/java-string-contentequals.html)当且仅当字符串与指定的StringButter有相同顺序的字符时候返回真。 |
| 7        | [static String copyValueOf(char[\] data)](http://www.runoob.com/java/java-string-copyvalueof.html)返回指定数组中表示该字符序列的 String。 |
| 8        | [static String copyValueOf(char[\] data, int offset, int count)](http://www.runoob.com/java/java-string-copyvalueof.html)返回指定数组中表示该字符序列的 String。 |
| 9        | [boolean endsWith(String suffix)](http://www.runoob.com/java/java-string-endswith.html)测试此字符串是否以指定的后缀结束。 |
| 10       | [boolean equals(Object anObject)](http://www.runoob.com/java/java-string-equals.html)将此字符串与指定的对象比较。 |
| 11       | [boolean equalsIgnoreCase(String anotherString)](http://www.runoob.com/java/java-string-equalsignorecase.html)将此 String 与另一个 String 比较，不考虑大小写。 |
| 12       | [byte[\] getBytes()](http://www.runoob.com/java/java-string-getbytes.html) 使用平台的默认字符集将此 String 编码为 byte 序列，并将结果存储到一个新的 byte 数组中。 |
| 13       | [byte[\] getBytes(String charsetName)](http://www.runoob.com/java/java-string-getbytes.html)使用指定的字符集将此 String 编码为 byte 序列，并将结果存储到一个新的 byte 数组中。 |
| 14       | [void getChars(int srcBegin, int srcEnd, char[\] dst, int dstBegin)](http://www.runoob.com/java/java-string-getchars.html)将字符从此字符串复制到目标字符数组。 |
| 15       | [int hashCode()](http://www.runoob.com/java/java-string-hashcode.html)返回此字符串的哈希码。 |
| 16       | [int indexOf(int ch)](http://www.runoob.com/java/java-string-indexof.html)返回指定字符在此字符串中第一次出现处的索引。 |
| 17       | [int indexOf(int ch, int fromIndex)](http://www.runoob.com/java/java-string-indexof.html)返回在此字符串中第一次出现指定字符处的索引，从指定的索引开始搜索。 |
| 18       | [int indexOf(String str)](http://www.runoob.com/java/java-string-indexof.html) 返回指定子字符串在此字符串中第一次出现处的索引。 |
| 19       | [int indexOf(String str, int fromIndex)](http://www.runoob.com/java/java-string-indexof.html)返回指定子字符串在此字符串中第一次出现处的索引，从指定的索引开始。 |
| 20       | [String intern()](http://www.runoob.com/java/java-string-intern.html) 返回字符串对象的规范化表示形式。 |
| 21       | [int lastIndexOf(int ch)](http://www.runoob.com/java/java-string-lastindexof.html) 返回指定字符在此字符串中最后一次出现处的索引。 |
| 22       | [int lastIndexOf(int ch, int fromIndex)](http://www.runoob.com/java/java-string-lastindexof.html)返回指定字符在此字符串中最后一次出现处的索引，从指定的索引处开始进行反向搜索。 |
| 23       | [int lastIndexOf(String str)](http://www.runoob.com/java/java-string-lastindexof.html)返回指定子字符串在此字符串中最右边出现处的索引。 |
| 24       | [int lastIndexOf(String str, int fromIndex)](http://www.runoob.com/java/java-string-lastindexof.html) 返回指定子字符串在此字符串中最后一次出现处的索引，从指定的索引开始反向搜索。 |
| 25       | [int length()](http://www.runoob.com/java/java-string-length.html)返回此字符串的长度。 |
| 26       | [boolean matches(String regex)](http://www.runoob.com/java/java-string-matches.html)告知此字符串是否匹配给定的正则表达式。 |
| 27       | [boolean regionMatches(boolean ignoreCase, int toffset, String other, int ooffset, int len)](http://www.runoob.com/java/java-string-regionmatches.html)测试两个字符串区域是否相等。 |
| 28       | [boolean regionMatches(int toffset, String other, int ooffset, int len)](http://www.runoob.com/java/java-string-regionmatches.html)测试两个字符串区域是否相等。 |
| 29       | [String replace(char oldChar, char newChar)](http://www.runoob.com/java/java-string-replace.html)返回一个新的字符串，它是通过用 newChar 替换此字符串中出现的所有 oldChar 得到的。 |
| 30       | [String replaceAll(String regex, String replacement](http://www.runoob.com/java/java-string-replaceall.html)使用给定的 replacement 替换此字符串所有匹配给定的正则表达式的子字符串。 |
| 31       | [String replaceFirst(String regex, String replacement)](http://www.runoob.com/java/java-string-replacefirst.html) 使用给定的 replacement 替换此字符串匹配给定的正则表达式的第一个子字符串。 |
| 32       | [String[\] split(String regex)](http://www.runoob.com/java/java-string-split.html)根据给定正则表达式的匹配拆分此字符串。 |
| 33       | [String[\] split(String regex, int limit)](http://www.runoob.com/java/java-string-split.html)根据匹配给定的正则表达式来拆分此字符串。 |
| 34       | [boolean startsWith(String prefix)](http://www.runoob.com/java/java-string-startswith.html)测试此字符串是否以指定的前缀开始。 |
| 35       | [boolean startsWith(String prefix, int toffset)](http://www.runoob.com/java/java-string-startswith.html)测试此字符串从指定索引开始的子字符串是否以指定前缀开始。 |
| 36       | [CharSequence subSequence(int beginIndex, int endIndex)](http://www.runoob.com/java/java-string-subsequence.html) 返回一个新的字符序列，它是此序列的一个子序列。 |
| 37       | [String substring(int beginIndex)](http://www.runoob.com/java/java-string-substring.html)返回一个新的字符串，它是此字符串的一个子字符串。 |
| 38       | [String substring(int beginIndex, int endIndex)](http://www.runoob.com/java/java-string-substring.html)返回一个新字符串，它是此字符串的一个子字符串。 |
| 39       | [char[\] toCharArray()](http://www.runoob.com/java/java-string-tochararray.html)将此字符串转换为一个新的字符数组。 |
| 40       | [String toLowerCase()](http://www.runoob.com/java/java-string-tolowercase.html)使用默认语言环境的规则将此 String 中的所有字符都转换为小写。 |
| 41       | [String toLowerCase(Locale locale)](http://www.runoob.com/java/java-string-tolowercase.html) 使用给定 Locale 的规则将此 String 中的所有字符都转换为小写。 |
| 42       | [String toString()](http://www.runoob.com/java/java-string-tostring.html) 返回此对象本身（它已经是一个字符串！）。 |
| 43       | [String toUpperCase()](http://www.runoob.com/java/java-string-touppercase.html)使用默认语言环境的规则将此 String 中的所有字符都转换为大写。 |
| 44       | [String toUpperCase(Locale locale)](http://www.runoob.com/java/java-string-touppercase.html)使用给定 Locale 的规则将此 String 中的所有字符都转换为大写。 |
| 45       | [String trim()](http://www.runoob.com/java/java-string-trim.html)返回字符串的副本，忽略前导空白和尾部空白。 |
| 46       | [static String valueOf(primitive data type x)](http://www.runoob.com/java/java-string-valueof.html)返回给定data type类型x参数的字符串表示形式。 |

## String类对象的创建

字符串声明：String stringName;
字符串创建：stringName = new String(字符串常量);或stringName = 字符串常量;
## String类构造方法
1. public String()

无参构造方法，用来创建空字符串的String对象。
 1 String str1 = new String(); 
2. public String(String value)
用已知的字符串value创建一个String对象。
 1 String str2 = new String("asdf"); 2 String str3 = new String(str2); 
3. public String(char[] value)
用字符数组value创建一个String对象。

```
1 char[] value = {"a","b","c","d"};
2 String str4 = new String(value);//相当于String str4 = new String("abcd");
```

4. public String(char chars[], int startIndex, int numChars)
用字符数组chars的startIndex开始的numChars个字符创建一个String对象。

```
1 char[] value = {"a","b","c","d"};
2 String str5 = new String(value, 1, 2);//相当于String str5 = new String("bc");
```

5. public String(byte[] values)
用比特数组values创建一个String对象。

```
1 byte[] strb = new byte[]{65,66};
2 String str6 = new String(strb);//相当于String str6 = new String("AB");
```

## String类常用方法

1. 求字符串长度
public int length()//返回该字符串的长度

```
1 String str = new String("asdfzxc");
2 int strlength = str.length();//strlength = 7
```

2. 求字符串某一位置字符
public char charAt(int index)//返回字符串中指定位置的字符；注意字符串中第一个字符索引是0，最后一个是length()-1。

```
1 String str = new String("asdfzxc");
2 char ch = str.charAt(4);//ch = z
```

3. 提取子串
用String类的substring方法可以提取字符串中的子串，该方法有两种常用参数:
1)public String substring(int beginIndex)//该方法从beginIndex位置起，从当前字符串中取出剩余的字符作为一个新的字符串返回。
2)public String substring(int beginIndex, int endIndex)//该方法从beginIndex位置起，从当前字符串中取出到endIndex-1位置的字符作为一个新的字符串返回。

```
1 String str1 = new String("asdfzxc");
2 String str2 = str1.substring(2);//str2 = "dfzxc"
3 String str3 = str1.substring(2,5);//str3 = "dfz"
```

4. 字符串比较
1)public int compareTo(String anotherString)//该方法是对字符串内容按字典顺序进行大小比较，通过返回的整数值指明当前字符串与参数字符串的大小关系。若当前对象比参数大则返回正整数，反之返回负整数，相等返回0。
2)public int compareToIgnore(String anotherString)//与compareTo方法相似，但忽略大小写。
3)public boolean equals(Object anotherObject)//比较当前字符串和参数字符串，在两个字符串相等的时候返回true，否则返回false。
4)public boolean equalsIgnoreCase(String anotherString)//与equals方法相似，但忽略大小写。



```
1 String str1 = new String("abc");
2 String str2 = new String("ABC");
3 int a = str1.compareTo(str2);//a>0
4 int b = str1.compareTo(str2);//b=0
5 boolean c = str1.equals(str2);//c=false
6 boolean d = str1.equalsIgnoreCase(str2);//d=true
```



5. 字符串连接
public String concat(String str)//将参数中的字符串str连接到当前字符串的后面，效果等价于"+"。

```
1 String str = "aa".concat("bb").concat("cc");
2 相当于String str = "aa"+"bb"+"cc";
```

6. 字符串中单个字符查找
1)public int indexOf(int ch/String str)//用于查找当前字符串中字符或子串，返回字符或子串在当前字符串中从左边起首次出现的位置，若没有出现则返回-1。
2)public int indexOf(int ch/String str, int fromIndex)//改方法与第一种类似，区别在于该方法从fromIndex位置向后查找。
3)public int lastIndexOf(int ch/String str)//该方法与第一种类似，区别在于该方法从字符串的末尾位置向前查找。
4)public int lastIndexOf(int ch/String str, int fromIndex)//该方法与第二种方法类似，区别于该方法从fromIndex位置向前查找。



```
1 String str = "I am a good student";
2 int a = str.indexOf('a');//a = 2
3 int b = str.indexOf("good");//b = 7
4 int c = str.indexOf("w",2);//c = -1
5 int d = str.lastIndexOf("a");//d = 5
6 int e = str.lastIndexOf("a",3);//e = 2
```



7. 字符串中字符的大小写转换
1)public String toLowerCase()//返回将当前字符串中所有字符转换成小写后的新串
2)public String toUpperCase()//返回将当前字符串中所有字符转换成大写后的新串

```
1 String str = new String("asDF");
2 String str1 = str.toLowerCase();//str1 = "asdf"
3 String str2 = str.toUpperCase();//str2 = "ASDF"
```

8. 字符串中字符的替换
1)public String replace(char oldChar, char newChar)//用字符newChar替换当前字符串中所有的oldChar字符，并返回一个新的字符串。
2)public String replaceFirst(String regex, String replacement)//该方法用字符replacement的内容替换当前字符串中遇到的第一个和字符串regex相匹配的子串，应将新的字符串返回。
3)public String replaceAll(String regex, String replacement)//该方法用字符replacement的内容替换当前字符串中遇到的所有和字符串regex相匹配的子串，应将新的字符串返回。

```
1 String str = "asdzxcasd";
2 String str1 = str.replace('a','g');//str1 = "gsdzxcgsd"
3 String str2 = str.replace("asd","fgh");//str2 = "fghzxcfgh"
4 String str3 = str.replaceFirst("asd","fgh");//str3 = "fghzxcasd"
5 String str4 = str.replaceAll("asd","fgh");//str4 = "fghzxcfgh"
```

9. 其他类方法
1)String trim()//截去字符串两端的空格，但对于中间的空格不处理。

```
1 String str = " a sd ";
2 String str1 = str.trim();
3 int a = str.length();//a = 6
4 int b = str1.length();//b = 4
```

2)boolean statWith(String prefix)或boolean endWith(String suffix)//用来比较当前字符串的起始字符或子字符串prefix和终止字符或子字符串suffix是否和当前字符串相同，重载方法中同时还可以指定比较的开始位置offset。

```
1 String str = "asdfgh";
2 boolean a = str.statWith("as");//a = true
3 boolean b = str.endWith("gh");//b = true
```

3)regionMatches(boolean b, int firstStart, String other, int otherStart, int length)//从当前字符串的firstStart位置开始比较，取长度为length的一个子字符串，other字符串从otherStart位置开始，指定另外一个长度为length的字符串，两字符串比较，当b为true时字符串不区分大小写。
4)contains(String str)//判断参数s是否被包含在字符串中，并返回一个布尔类型的值。

```
1 String str = "student";
2 str.contains("stu");//true
3 str.contains("ok");//false
```

5)String[] split(String str)//将str作为分隔符进行字符串分解，分解后的字字符串在字符串数组中返回。

```
1 String str = "asd!qwe|zxc#";
2 String[] str1 = str.split("!|#");//str1[0] = "asd";str1[1] = "qwe";str1[2] = "zxc";
```

##  字符串与基本类型的转换

1. 字符串转换为基本类型
java.lang包中有Byte. Short、Integer、Float、Double类的调用方法：
1)public static byte parseByte(String s)
2)public static short parseShort(String s)
3)public static short parseInt(String s)
4)public static long parseLong(String s)
5)public static float parseFloat(String s)
6)public static double parseDouble(String s)
例如：

```
1 int n = Integer.parseInt("12");
2 float f = Float.parseFloat("12.34");
3 double d = Double.parseDouble("1.124");
```

2. 基本类型转换为字符串类型
String类中提供了String valueOf()放法，用作基本类型转换为字符串类型。
1)static String valueOf(char data[])
2)static String valueOf(char data[], int offset, int count)
3)static String valueOf(boolean b)
4)static String valueOf(char c)
5)static String valueOf(int i)
6)static String valueOf(long l)
7)static String valueOf(float f)
8)static String valueOf(double d)
例如：

```
1 String s1 = String.valueOf(12);
2 String s1 = String.valueOf(12.34);
```

3. 进制转换
使用Long类中的方法得到整数之间的各种进制转换的方法：
Long.toBinaryString(long l)
Long.toOctalString(long l)
Long.toHexString(long l)
Long.toString(long l, int p)//p作为任意进制
# Java类——Math类

## java.math.Math类常用的常量和方法

```java
Math.PI //记录的圆周率
Math.E//记录e的常量
Math.abs //求绝对值
Math.sin //正弦函数 Math.asin 反正弦函数
Math.cos //余弦函数 Math.acos 反余弦函数
Math.tan //正切函数 Math.atan 反正切函数&amp;nbsp;Math.atan2 商的反正切函数
Math.toDegrees //弧度转化为角度 Math.toRadians 角度转化为弧度
Math.ceil //得到不小于某数的最大整数
Math.floor //得到不大于某数的最大整数
例如：Math.floor(12.7) =12.0
ceil()//是天花板，即向上取整。floor是地板，向下取整。round是四舍五入。
Math.IEEEremainder //求余
Math.max //求两数中最大
Math.min //求两数中最小
Math.sqrt //求开方
Math.pow //求某数的任意次方, 抛出ArithmeticException处理溢出异常
Math.sqrt(x)：//平方根
Math.pow(x,y)：//x的y次方
Math.exp //求e的任意次方
Math.log10 //以10为底的对数
Math.log //自然对数
Math.rint //求距离某数最近的整数（可能比某数大，也可能比它小）
Math.round //同上，返回int型或者long型（上一个函数返回double型）
Math.random //返回0，1之间的一个随机数</p>
java.math.BigInteger(大整数)：
BigInteger bi1=new BigInteger("1234567890123456890");
BigInteger bi2=BigInteger.valueOf(123L);
bi1=bi1.add(bi2);//b1+b2
bi1=bi1.multiply(bi2);//b1*b
bi1=bi1.subtract(bi2);//b1-b2
bi1=bi1.divide(bi2);// b1/b2
java.math.BigDecimal(大浮点数):
BigDecimal bd = new BigDecimal("3.1415926");
bd = bd.setScale(2,BigDecimal.ROUND_DOWN);//取3.1415926小数点后面二位
```

## Java的Math类封装了很多与数学有关的属性和方法，大致如下：

```java
public class Main {

	public static void main(String[] args) {
		// TODO Auto-generated method stub
		System.out.println(Math.E);//比任何其他值都更接近 e（即自然对数的底数）的 double 值。
		System.out.println(Math.PI);//比任何其他值都更接近 pi（即圆的周长与直径之比）的 double 值。
		/*
		 * 1.abs绝对值函数
		 * 对各种数据类型求绝对值
		 */
		System.out.println(Math.abs(-10));//输出10
		
		/*
		 * 2.三角函数与反三角函数
		 * cos求余弦
		 * sin求正弦
		 * tan求正切
		 * acos求反余弦
		 * asin求反正弦
		 * atan求反正切
		 * atan2(y,x)求向量(x,y)与x轴夹角
		 */
		System.out.println(Math.acos(-1.0));//输出圆周率3.14...
		System.out.println(Math.atan2(1.0, 1.0));//输出 π/4 的小数值
		
		/*
		 * 3.开根号
		 * cbrt(x)开立方
		 * sqrt(x)开平方
		 * hypot(x,y)求sqrt(x*x+y*y)在求两点间距离时有用sqrt((x1-x2)^2+(y1-y2)^2)
		 */
		System.out.println(Math.sqrt(4.0));//输出2.0
		System.out.println(Math.cbrt(8.0));//输出2.0
		System.out.println(Math.hypot(3.0, 4.0));//输出5.0
		
		/*
		 * 4.最值
		 * max(a,b)求最大值
		 * min(a,b)求最小值
		 */
		System.out.println(Math.max(1, 2));//输出2
		System.out.println(Math.min(1.9, -0.2));//输出-0.2
		/*
		 * 5.对数
		 * log(a) a的自然对数(底数是e)
		 * log10(a) a 的底数为10的对数
		 * log1p(a) a+1的自然对数
		 * 值得注意的是，前面其他函数都有重载，对数运算的函数只能传double型数据并返回double型数据
		 */
		System.out.println(Math.log(Math.E));//输出1.0
		System.out.println(Math.log10(10));//输出1.0
		System.out.println(Math.log1p(Math.E-1.0));//输出1.0
		/*
		 * 6.幂
		 * exp(x) 返回e^x的值
		 * expm1(x) 返回e^x - 1的值
		 * pow(x,y) 返回x^y的值
		 * 这里可用的数据类型也只有double型
		 */
		System.out.println(Math.exp(2));//输出E^2的值
		System.out.println(Math.pow(2.0, 3.0));//输出8.0
		
		/*
		 * 7.随机数
		 * random()返回[0.0,1.0)之间的double值
		 * 这个产生的随机数其实可以通过*x控制
		 * 比如(int)(random*100)后可以得到[0,100)之间的整数
		 */
		System.out.println((int)(Math.random()*100));//输出[0,100)间的随机数
		
		/*
		 * 8.转换
		 * toDegrees(a) 弧度换角度
		 * toRadians(a) 角度换弧度
		 */
		System.out.println(Math.toDegrees(Math.PI));//输出180.0
		System.out.println(Math.toRadians(180));//输出 π 的值
		/*
		 * 9.其他
		 */
		
		//copySign(x,y) 返回 用y的符号取代x的符号后新的x值
		System.out.println(Math.copySign(-1.0, 2.0));//输出1.0
		System.out.println(Math.copySign(2.0, -1.0));//输出-2.0
		
		//ceil(a) 返回大于a的第一个整数所对应的浮点数(值是整的，类型是浮点型)
		//可以通过强制转换将类型换成整型
		System.out.println(Math.ceil(1.3443));//输出2.0
		System.out.println((int)Math.ceil(1.3443));//输出2
		
		//floor(a) 返回小于a的第一个整数所对应的浮点数(值是整的，类型是浮点型)
		System.out.println(Math.floor(1.3443));//输出1.0
		
		//rint(a) 返回最接近a的整数的double值
		System.out.println(Math.rint(1.2));//输出1.0
		System.out.println(Math.rint(1.8));//输出2.0
		
		
		//nextAfter(a,b) 返回(a,b)或(b,a)间与a相邻的浮点数 b可以比a小
		System.out.println(Math.nextAfter(1.2, 2.7));//输出1.2000000000000002
		System.out.println(Math.nextAfter(1.2, -1));//输出1.1999999999999997	
		//所以这里的b是控制条件
		
		//nextUp(a) 返回比a大一点点的浮点数
		System.out.println(Math.nextUp(1.2));//输出1.2000000000000002
		
		//nextDown(a) 返回比a小一点点的浮点数
		System.out.println(Math.nextDown(1.2));//输出1.1999999999999997	
	}
}
```

另外，当我尝试这样使用数学类的时候是错误的：

 ```
   Math m = new Math();m.sqrt(4.0);  
 ```

为什么呢？

查了下Math的源码，惊呆了！它的构造方法居然是这样写的：

```
private Math() {}  
```

构造方法写成私有的

所以根本就不能创建对象。

# Java中Date各种相关用法


## 计算某一月份的最大天数

Java代码

```java
Calendar time=Calendar.getInstance(); time.clear(); time.set(Calendar.YEAR,year); time.set(Calendar.MONTH,i-1);//注意,Calendar对象默认一月为0 int day=time.getActualMaximum(Calendar.DAY_OF_MONTH);//本月份的天数 
```

注：在使用set方法之前，必须先clear一下，否则很多信息会继承自系统当前时间

## Calendar和Date的转化

(1) Calendar转化为Date

Java代码

```java
Calendar cal=Calendar.getInstance(); Date date=cal.getTime(); 
```

(2) Date转化为Calendar

Java代码

```java
Date date=new Date(); 
Calendar cal=Calendar.getInstance(); 
cal.setTime(date); 
```

## 格式化输出日期时间

Java代码

```java
Date date=new Date(); 
SimpleDateFormat df=new SimpleDateFormat("yyyy-MM-dd hh:mm:ss"); System.out.println(df.format(date)); 
```

## 计算一年中的第几星期

(1)计算某一天是一年中的第几星期

Java代码

```java
Calendar cal=Calendar.getInstance(); 
cal.set(Calendar.YEAR, 2006); 
cal.set(Calendar.MONTH, 8); 
cal.set(Calendar.DAY_OF_MONTH, 3); 
int weekno=cal.get(Calendar.WEEK_OF_YEAR); 
```

(2)计算一年中的第几星期是几号

Java代码

```java
SimpleDateFormat df=new SimpleDateFormat("yyyy-MM-dd"); 
Calendar cal=Calendar.getInstance(); 
cal.set(Calendar.YEAR, 2006); 
cal.set(Calendar.WEEK_OF_YEAR, 1); 
cal.set(Calendar.DAY_OF_WEEK, Calendar.MONDAY); 
System.out.println(df.format(cal.getTime())); 
```

输出：

```
2006-01-02
```

## add()和roll()的用法

(1)add()方法

Java代码

```java
SimpleDateFormat df=new SimpleDateFormat("yyyy-MM-dd"); 
Calendar cal=Calendar.getInstance(); 
cal.set(Calendar.YEAR, 2006); 
cal.set(Calendar.MONTH, 8); 
cal.set(Calendar.DAY_OF_MONTH, 3); 
cal.add(Calendar.DATE, -4); 
Date date=cal.getTime(); 
System.out.println(df.format(date)); 
cal.add(Calendar.DATE, 4); 
date=cal.getTime(); 
System.out.println(df.format(date)); 
```

输出：

```
2006-08-30
2006-09-03
```

(2)roll方法

Java代码

```java
cal.set(Calendar.YEAR, 2006); 
cal.set(Calendar.MONTH, 8); 
cal.set(Calendar.DAY_OF_MONTH, 3); 
cal.roll(Calendar.DATE, -4); 
date=cal.getTime(); 
System.out.println(df.format(date)); 
cal.roll(Calendar.DATE, 4); 
date=cal.getTime(); 
System.out.println(df.format(date)); 
```

输出：

```
2006-09-29
2006-09-03
```

可见，roll()方法在本月内循环，一般使用add()方法；


## 计算两个任意时间中间的间隔天数

(1)传进Calendar对象

Java代码

```java
/ *//计算两个时间之间相隔天数 
* @param startday 开始时间 
* @param endday 结束时间 
* @return 
*/ 
public int getIntervalDays(Calendar startday,Calendar endday)...{ 
//确保startday在endday之前 
if(startday.after(endday))...{ 
Calendar cal=startday; 
startday=endday; 
endday=cal; 
} 
//分别得到两个时间的毫秒数 
long sl=startday.getTimeInMillis(); 
long el=endday.getTimeInMillis(); 
long ei=el-sl; 
//根据毫秒数计算间隔天数 
return (int)(ei/(1000*60*60*24)); 
} 
```

(2)传进Date对象

Java代码

```java
/ *//计算两个时间之间相隔天数 
* @param startday 开始时间 
* @param endday 结束时间 
* @return 
*/ 
public int getIntervalDays(Date startday,Date endday)...{ 
//确保startday在endday之前 
if(startday.after(endday))...{ 
Date cal=startday; 
startday=endday; 
endday=cal; 
} 
//分别得到两个时间的毫秒数 
long sl=startday.getTime(); 
long el=endday.getTime(); 
long ei=el-sl; 
//根据毫秒数计算间隔天数 
return (int)(ei/(1000*60*60*24)); 
} 
```

同理，可以用相同的方法计算出任意两个时间相隔的小时数，分钟数，秒钟数等

注：以上方法是完全按时间计算，有时并不能令人满意，如：

```
startday="2006-10-11 20:00:00"
endday="2006-10-12 8:00:00"
```

计算结果为0，但是我们也许相让计算结果变为1，此时可以用如下方法实现：

在传参之前，先设定endday的时间，如：

Java代码

```java
endday.set(Calendar.HOUR_OF_DAY, 23); 
endday.set(Calendar.MINUTE, 59); 
endday.set(Calendar.SECOND, 59); 
endday.set(Calendar.MILLISECOND, 59); 
```

这样再传进去startday,endday，则结果就如我们所愿了。不过，如果嫌以上方法麻烦，可以参考以下方法：

(3)改进精确计算相隔天数的方法

Java代码

```java
public int getDaysBetween (Calendar d1, Calendar d2) ...{ 
if (d1.after(d2)) ...{ // swap dates so that d1 is start and d2 is end 
java.util.Calendar swap = d1; 
d1 = d2; 
d2 = swap; 
} 
int days = d2.get(Calendar.DAY_OF_YEAR) - d1.get(Calendar.DAY_OF_YEAR); 
int y2 = d2.get(Calendar.YEAR); 
if (d1.get(Calendar.YEAR) != y2) ...{ 
d1 = (Calendar) d1.clone(); 
do ...{ 
days += d1.getActualMaximum(Calendar.DAY_OF_YEAR);//得到当年的实际天数 
d1.add(Calendar.YEAR, 1); 
} while (d1.get(Calendar.YEAR) != y2); 
} 
return days; 
} 
```

## 获取系统当前时间

Java代码

```java
public static String getSystemTime(){ 
Date date=new Date(); 
SimpleDateFormat df=new SimpleDateFormat("yyyy-MM-dd HH:mm:ss"); 
return df.format(date); 
} 
//字符串转化成时间类型（字符串可以是任意类型，只要和SimpleDateFormat中的格式一致即可） 
java.text.SimpleDateFormat sdf = new java.text.SimpleDateFormat("M/dd/yyyy hh:mm:ss a",java.util.Locale.US); 
java.util.Date d = sdf.parse("5/13/2003 10:31:37 AM"); 
SimpleDateFormat formatter = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss"); 
String mDateTime1=formatter.format(d); 
//当前时间 
Calendar cal = Calendar.getInstance(); 
// SimpleDteFormat formatter = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss"); 
SimpleDateFormat formatter =new SimpleDateFormat("yyyy-MM-dd HH:mm:ss G E D F w W a E F"); 
String mDateTime=formatter.format(cal.getTime()); 
//1年前日期 
java.util.Date myDate=new java.util.Date(); 
long myTime=(myDate.getTime()/1000)-60*60*24*365; 
myDate.setTime(myTime*1000); 
String mDate=formatter.format(myDate); 
//明天日期 
myDate=new java.util.Date(); 
myTime=(myDate.getTime()/1000)+60*60*24; 
myDate.setTime(myTime*1000); 
mDate=formatter.format(myDate); 
//两个时间之间的天数 
SimpleDateFormat myFormatter = new SimpleDateFormat("yyyy-MM-dd"); 
java.util.Date date= myFormatter.parse("2003-05-1"); 
java.util.Date mydate= myFormatter.parse("1899-12-30"); 
long day=(date.getTime()-mydate.getTime())/(24*60*60*1000); 
//加半小时 
SimpleDateFormat format = new SimpleDateFormat("yyyy-MM-dd hh:mm:ss"); 
java.util.Date date1 = format.parse("2002-02-28 23:16:00"); 
long Time=(date1.getTime()/1000)+60*30; 
date1.setTime(Time*1000); 
String mydate1=formatter.format(date1); 
//年月周求日期 
SimpleDateFormat formatter2 = new SimpleDateFormat("yyyy-MM F E"); 
java.util.Date date2= formatter2.parse("2003-05 5 星期五"); 
SimpleDateFormat formatter3 = new SimpleDateFormat("yyyy-MM-dd"); 
String mydate2=formatter3.format(date2); 
//求是星期几 
mydate= myFormatter.parse("2001-1-1"); 
SimpleDateFormat formatter4 = new SimpleDateFormat("E"); 
String mydate3=formatter4.format(mydate); 
} 
```

在 开发web应用中，针对不同的数据库日期类型，我们需要在我们的程序中对日期类型做各种不同的转换。若对应数据库数据是oracle的Date类型，即只 需要年月日的，可以选择使用java.sql.Date类型，若对应的是MSsqlserver数据库的DateTime类型，即需要年月日时分秒的，选 择java.sql.Timestamp类型

你可以使用dateFormat定义时间日期的格式，转一个字符串即可

Java代码

```java
package personal.jessica; 
import java.util.Date; 
import java.util.Calendar; 
import java.sql.Timestamp; 
import java.text.DateFormat; 
import java.text.SimpleDateFormat; 
import java.util.Locale; 
class Datetest{ 
/ 
*method 将字符串类型的日期转换为一个timestamp（时间戳记java.sql.Timestamp） 
*@param dateString 需要转换为timestamp的字符串 
*@return dataTime timestamp 
*/ 
public final static java.sql.Timestamp string2Time(String dateString) 
throws java.text.ParseException { 
DateFormat dateFormat; 
dateFormat = new SimpleDateFormat("yyyy-MM-dd kk:mm:ss.SSS", Locale.ENGLISH);//设定格式 
//dateFormat = new SimpleDateFormat("yyyy-MM-dd kk:mm:ss", Locale.ENGLISH); 
dateFormat.setLenient(false); 
java.util.Date timeDate = dateFormat.parse(dateString);//util类型 
java.sql.Timestamp dateTime = new java.sql.Timestamp(timeDate.getTime());//Timestamp类型,timeDate.getTime()返回一个long型 
return dateTime; 
} 
```

## 将字符串类型的日期转换为一个Date（java.sql.Date）  

```java
/  
*method 将字符串类型的日期转换为一个Date（java.sql.Date）  
*@param dateString 需要转换为Date的字符串  
*@return dataTime Date  
*/  
public final static java.sql.Date string2Date(String dateString)  
throws java.lang.Exception {  
DateFormat dateFormat;  
dateFormat = new SimpleDateFormat("yyyy-MM-dd", Locale.ENGLISH);  
dateFormat.setLenient(false);  
java.util.Date timeDate = dateFormat.parse(dateString);//util类型  
java.sql.Date dateTime = new java.sql.Date(timeDate.getTime());//sql类型  
return dateTime;  
}  
public static void main(String[] args){  
Date da = new Date();  
//注意：这个地方da.getTime()得到的是一个long型的值  
System.out.println(da.getTime());  
//由日期date转换为timestamp  
//第一种方法：使用new Timestamp(long)  
Timestamp t = new Timestamp(new Date().getTime());  
System.out.println(t);  
//第二种方法：使用Timestamp(int year,int month,int date,int hour,int minute,int second,int nano)  
Timestamp tt = new Timestamp(Calendar.getInstance().get(  
Calendar.YEAR) - 1900, Calendar.getInstance().get(  
Calendar.MONTH), Calendar.getInstance().get(  
Calendar.DATE), Calendar.getInstance().get(  
Calendar.HOUR), Calendar.getInstance().get(  
Calendar.MINUTE), Calendar.getInstance().get(  
Calendar.SECOND), 0);  
System.out.println(tt);  
try {  
String sToDate = "2005-8-18";//用于转换成java.sql.Date的字符串  
String sToTimestamp = "2005-8-18 14:21:12.123";//用于转换成java.sql.Timestamp的字符串  
Date date1 = string2Date(sToDate);  
Timestamp date2 = string2Time(sToTimestamp);  
System.out.println("Date:"+date1.toString());//结果显示  
System.out.println("Timestamp:"+date2.toString());//结果显示  
}catch(Exception e) {  
e.printStackTrace();  
}  
}  
}  
```

Java获取系统时间的年份

Java代码

```java
public static String getYear(){ 
Calendar ca = Calendar.getInstance(); 
ca.setTime(new java.util.Date()); 
String year = ""+ca.get(Calendar.YEAR); 
return year; 
} 
public void getYear(){ 
Calendar ca = Calendar.getInstance(); 
ca.setTime(new java.util.Date()); 
SimpleDateFormat simpledate = new SimpleDateFormat("yyyyMMdd"); 
String date = simpledate.format(ca.getTime()); 
int year = ca.get(Calendar.YEAR); 
int month = ca.get(Calendar.MONTH); 
int day = ca.get(Calendar.DAY_OF_MONTH); 
System.out.println(date+"||"+year+"||"+month+"||"+day); 
} 
```
