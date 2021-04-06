---
title: Freemaker开发学习笔记
tags:
  - Freemaker
copyright: true
category: Freemaker
abbrlink: 39074
date: 2017-12-06 21:34:36
---
![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0036.jpg)

Freemaker是一个强大的模板引擎，相比velocity而言，其强大的过程调用、递归和闭包回调功能让freemaker可以完成几乎所有我们所想的功能。从个人看法而言，freemaker完全有能力作为MDA的代码辅助生成工具。 
<!--more-->
本文试图越过传统的概念性介绍，通过一组例子直接把读者带入到Freemaker应用的较高层阶。

大家看文章标题就应该知道，我想用一篇文章，把大家从对freemaker的陌生直接带入到比较深入的境界，所以不想说一些基础性的东西，如果大家不习惯我的表达方法，大可通过google去找习惯于自己阅读方式的相关文章。
我用过velocity，最近才用freemaker，才知道我以前的选择是错了，因为velocity不支持过程的调用，所以我为velocity增加了很多的东西，写了很多代码，而且脚本也累赘得要命。freemaker首先吸引我的是它强大的过程调用和递归处理能力，其次则是xml风格的语法结构有着明显的边界，不象velocity要注意段落之间要留空格。所以我建议大家直接使用Freemaker，虽然freemaker没有.net版本，我想不嵌入程序中使用的话，freemaker是绝对的首选。（题外话，谁有兴趣移植一个NFreeMaker？）
在使用之前我们先要设置运行环境，在使用Freemaker的时候，我们需要下载相关的程序：
- freemaker: http://freemarker.sourceforge.net/
- fmpp: http://fmpp.sourceforge.net/

其中fmpp是一个freemaker的辅助工具，有了它，我们可以实现更多的功能。以下例子必须fmpp辅助。
这里我们首先提出问题。大家看如下的一个xml文件，虽然freemaker的能力不仅在于处理xml文件，但是用xml作为例子更直观一些：

```xml
<?xml version='1.0' encoding="gb2312" ?>
<types xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="urn:DruleForm-Lite.xsd">
         <type name="Type1" >
             <labels>
                 <label lang="zh-CN" value="投保单"/>
             </labels>
             <field name="Field11" type="Float" lbound="1" ubound="1" >
                 <labels>
                     <label lang="zh-CN" value="投保单ID"/>
                 </labels>
             </field>
             <field name="Field12" type="String" lbound="1" ubound="*"/>
             <field name="Field13" type="Integer" lbound="1" ubound="*"/>
             <field name="Field14" type="Type2" lbound="1" ubound="*">
                 <type name="Type2">
                     <field name="Field21" type="String" lbound="1" ubound="*"/>
                     <field name="Field22" type="Integer" lbound="1" ubound="*"/>    
                 </type>
             </field>
             <field name="Field15" type="InsuranceProduct" lbound="1" ubound="*"/>
         <type>
         <type name="Type3">
             <field name="Field31" type="Type1" lbound="1" ubound="*" />
         </type>
     </types>
```

### freemaker的基本语法
- <# ... > 中存放所有freemaker的内容，之外的内容全部原样输出。
- <@ ... /> 是函数调用两个定界符内的内容中，第一个符号表示指令或者函数名，其后的跟随参数。
- freemaker提供的控制包括如下：
## freemaker的基本语法
-  <# ... > 中存放所有freemaker的内容，之外的内容全部原样输出。
-  <@ ... /> 是函数调用
两个定界符内的内容中，第一个符号表示指令或者函数名，其后的跟随参数。

#### freemaker语句： 

```html
<#if condition>
    <#elseif condition>
    <#else>
</#if>
```
条件判断

```html
<#list hash_or_seq as var>
</#list>
```

遍历hash表或者collection（freemaker称作sequence）的成员

```html
<#macro name param1 param2 ... >
<#nested param>
</#macro>
```
 宏，无返回参数

```html
<#function name param1 param2>
<#return val>
</#function>
```
 
函数，有返回参数

```html
var?member_function(...)
```
 用函数对var进行转换，freemaker称为build-ins。实际内部实现类似
 
```html
member_function(var, ...)
```

stringA[M .. N] 取子字符串，类似

```html
substring(stringA, M, N)
{key:value, key2:value2 ...}
```
直接定义一个hash表

```html
[item0, item1, item2 ...]
```
 直接定义一个序列

```html
hash0[key0]
```
存取hash表中key对应的元素

```html
seq0[5]
```
存取序列指定下标的元素 

```html
<@function1 param0 param1 ... />
```
调用函数function1

```html
<@macro0 param0 param1 ; nest_param0 nest_param1 ...> nest_body 
< /@macro>
```
调用宏，并处理宏的嵌套

```html
<#assign var = value >
```

定义变量并初始化

```html
<#local var = value>
```
在 macro 或者 function 中定义局部变量并初始化 

```html
<#global var = value >
```
定义全局变量并初始化

```html
${var}
```
输出并替换为表达式的值

```html
<#visit xmlnode>
```
调用macro匹配xmlnode本身及其子节点

```html
<#recurse xmlnode>
```
调用macro匹配xmlnode的子节点


## freemaker--设计指导  

一个ftl标记不能放在另外一个ftl标记里面，但是注释标记能够放在ftl标记里面。

- 系统预定义指令采用<#...></#>
- 用户自定义指令采用<@...></@>

hash片段可以采用： products[10..19] or products[5..] 的格式。

序列也可以做加法计算：passwords + {"joe":"secret42"}
缺省值: name!"unknown" 或者 (user.name)!"unknown" 或者 name! 或者 (user.name)!
null值检查: name?? or (user.name)??

### 转义列表：
- [x] Escape sequence	Meaning
- [x] \"	Quotation mark (u0022)
- [x] \'	Apostrophe (a.k.a. apostrophe-quote) (u0027)
- [x] \\	Back slash (u005C)
- [x] \n	Line feed (u000A)
- [x] \r	Carriage return (u000D)
- [x] \t	Horizontal tabulation (a.k.a. tab) (u0009)
- [x] \b	Backspace (u0008)
- [x] \f	Form feed (u000C)
- [x] \l	Less-than sign: <
- [x] \g	Greater-than sign: >
- [x] \a	Ampersand: &
- [x] \{	Curly bracket: {
- [x] \xCode	Character given with its hexadecimal Unicode code (UCS code)

如果想打印${，则需要将{转义，可以写成"$\{user}"，或者可以用生字符（r指令）：$(r "${xx}"}

### 序列构成：

```html
<#list ["winter", "spring", "summer", "autumn"] as x>${x}</#list>
```

不同的对象可以存放在一个序列里面，比如：[2 + 2, [1, 2, 3, 4], "whatnot"]. 第一个是数字，第二个是序列，第三个是字符串。

可用采用start..end的方式来定义一个数字序列，start可以小于end，同时，end也可以省略。

hash取值支持一下四种模式：

```html
book.author.name, 
book["author"].name, 
book.author.["name"], 
book["author"]["name"].
```

特殊变量是指freemaker引擎本身定义的变量。访问时，以.variable_name的语法访问。

变量表达式支持嵌套模式，比如：${"Hello ${user}!"}。

### 变量表达式在指令中的使用情况：
变量表达式可以在指令中，用“”的方式存在，不如：<#include "/footer/${company}.html">. 
但是不允许下面的方式存在： <#if ${isBig}>Wow!</#if>

**正确写法**
```
<#if isBig>Wow!</#if>
```

而且 <#if "${isBig}">Wow!</#if>写法也不正确，因为"${isBig}"返回的是字符串，不是boolean类型。

字符串中取字符或字符串采用以下语法：
```
${user[0]},${user[0..2]} ${user[4..]},${user?string(4)}
```


### 序列操作：
   加法：<#list ["Joe", "Fred"] + ["Julia", "Kate"] as user> 但要注意串联之后的读取速度变慢。
   子序列：seq[1..4]

序列和hash的串联都只能用于两个相加，不能有多个相加的模式，hash相加，如果两个相加的hash存在相同的key，则后面会覆盖前面的。

在使用>=或者>时，需要注意一些问题，因为freemaker会将>解释成标记的关闭符，为了解决这个问题，需要在表达式加上括号，比如： <#if (x > y)>. 或者使用 &gt; and &lt符号来代替。

无值变量（包括无该变量，null，返回void，无属性等）：unsafe_expr!default_expr or unsafe_expr! or (unsafe_expr)!default_expr or (unsafe_expr)!
缺省值可以是任何类型，不一定是数字，比如：hits!0 或者 colors!["red", "green", "blue"]. 

如果缺省值忽略，那么将会默认为空串、空序列或者空hash，因为freemarker支持多类型的值。不过要让默认值为0或false，则不能省略缺省值。

### 非顶层变量的无值处理：
   product.color!"red"：只处理product不为空，color为空的缺省值处理，如果product为空，则freemaker会抛出异常。(product.color)!"red"：则会处理product为空，color为空，或者没有color属性的无值情况。

### 无值变量的判断操作：
unsafe_expr?? or (unsafe_expr)??
判断变量是否是无值。

### 普通变量插入方式:
${expression},${3+5);
### 数字变量插入方式:
#{expression} or #{expression; format}：过期。
变量只能用于文本区或者是字符串里面，比如：<h1>Hello ${name}!</h1>以及 <#include "/footer/${company}.html">

### 数字值的插入：
根据缺省的number_format输出，以及可以通过setting来达到设置数字格式的目的，也可以通过内置函数string来改变输出格式。

### 日期类型的格式设置：
date_format, time_format 和 datetime_format


### 定义宏：
   不带参数：<#macro 宏名>...</#macro>，引用<@宏名 />
   带参数：<#macro 宏名 参数...>...</#macro>，引用<@宏名 参数1=值1.../>，带有参数的宏，调用是参数的值必须和参数的个数相同。当然也可以在宏定义时给参数一些默认值。比如：<#macro greet person color="black">


### 宏里面的嵌套内容：
<#macro border>
  <table border=4 cellspacing=0 cellpadding=4><tr><td>
    <#nested>
  </tr></td></table>
</#macro> 
在宏的定义body中加入<#nested>指令。嵌套的内容可以是任何正确的ftl块。

宏的本地变量在嵌套内容中是不可见的。

宏定义时，<#nest>指令相当于调用定义的内容，而使用宏时，nest body相当于定义。
<#macro repeat count>  <#list 1..count as x>    <#nested x, x/2, x==count>  </#list></#macro><@repeat count=4 ; c, halfc, last>  ${c}. ${halfc}<#if last> Last!</#if></@repeat> 


### 定义变量：
   在模板中定义的变量将会隐藏（不是更改）数据模型根下面的同名的变量。

#### 模板中的3种类型变量：
- [x]    1：plain variables，能够在模板中的任何地方访问，一个模板include另外一个模板，也可以访问被包含模板的变量。可以通过assign或者macro指令产生或替换变量。
   如果要访问数据模型中的变量，则可以通过.global来访问：
   <#assign user = "Joe Hider">
   ${user}          <#-- prints: Joe Hider -->
   ${.globals.user} <#-- prints: Big Joe--> 
- [x]    2：Local variables，宏定义body中用local指令创建或者替换。
- [x]    3：Loop variables:由list指令产生。


namespaces:
<#import "/lib/my_test.ftl" as my> <#-- the hash called "my" will be the "gate" -->
<@my.copyright date="1999-2002"/>
${my.mail} 

#### 设置命名空间里面的变量：<#assign mail="jsmith@other.com" in my>

#### 命名空间与数据模型：命名空间的ftl可以访问数据模型的变量。同样命名空间的变量也会隐藏数据模型中同名的变量。

### 空白问题：
- [x] 1：White-space stripping，默认为enabled，清除ftl标记带来的空白以及缩进。处理模板的空白。
- [x] 2：t, rt, lt指令。
- [x] 3：ftl的参数strip_text.

用compress directive或者transform来处理输出。
<#compress>...</#compress>：消除空白行。
<@compress single_line=true>.../@compress将输出压缩为一行。

可替换语法：
freemarker可用"["代替"<".在模板的文件开头加上[#ftl].

## Spring MVC 使用 Freemarker
•	Freemaker是取代JSP的又一种视图技术，和Velocity非常类似，但是它比Velocity多了一个格式化的功能，因此使用上较Velocity方便一点，但语法也稍微复杂一些。
将Velocity替换为Freemarker只需要改动一些配置文件，同样，在Spring中使用Freemarker也非常方便，根本无须与Freemarker的API打交道。我们将Spring_Velocity工程复制一份，命名为Spring_Freemarker

修改dispatcher-servlet.xml，将velocityConfig删除，修改viewResolver为FreeMarker ViewResolver，并添加一个freemarkerConfig。

```xml
<!-- 使用Freemarker视图解析器 -->
<bean id="viewResolver" class="org.springframework.web.servlet.view. freemarker.FreeMarkerViewResolver">
    <property name="contentType" value="text/html;charset=UTF-8" />
    <property name="prefix" value="/" />
    <property name="suffix" value=".html" />
</bean>
<!-- 配置Freemarker -->
<bean id="freemarkerConfig" class="org.springframework.web.servlet.view. freemarker.FreeMarkerConfigurer">
    <!-- 视图资源位置 -->
    <property name="templateLoaderPath" value="/" />
    <property name="defaultEncoding" value="UTF-8" />
</bean>
```

模板test.html可以稍做修改，加入Freemarker内置的格式化功能来定制Date类型的输出格式。

```html
<html>
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
    <title>Spring_Freemarker</title>
</head>
<body>
    <h3>Hello, ${name}, it is ${time?string("yyyy-MM-dd HH:mm:ss")}</h3>
</body>
</html>
```

添加freemarker.jar到web/WEB-INF/lib目录后，启动Resin，可以看到由Freemarker渲染的页面


## FreeMarker入门： 
### 快速入门 
#### 模板 + 数据模型 = 输出 
         FreeMarker基于设计者和程序员是具有不同专业技能的不同个体的观念 
        他们是分工劳动的：设计者专注于表示——创建HTML文件、图片、Web页面的其它可视化方面；程序员创建系统，生成设计页面要显示的数据 
        经常会遇到的问题是：在Web页面（或其它类型的文档）中显示的信息在设计页面时是无效的，是基于动态数据的 
        在这里，你可以在HTML（或其它要输出的文本）中加入一些特定指令，FreeMarker会在输出页面给最终用户时，用适当的数据替代这些代码 
下面是一个例子： 

```html
<html> 
<head> 
  <title>Welcome!</title> 
</head> 
<body> 
  <h1>Welcome ${user}!</h1> 
  <p>Our latest product: 
  <a href="${latestProduct.url}">${latestProduct.name}</a>! 
</body> 
</html>
```

这个例子是在简单的HTML中加入了一些由${…}包围的特定代码，这些特定代码是FreeMarker的指令，而包含FreeMarker的指令的文件就称为模板（Template） 
至于user、latestProduct.url和latestProduct.name来自于数据模型（data model） 
        数据模型由程序员编程来创建，向模板提供变化的信息，这些信息来自于数据库、文件，甚至于在程序中直接生成 
         模板设计者不关心数据从那儿来，只知道使用已经建立的数据模型 
         下面是一个可能的数据模型： 

```
(root) 
  | 
  +- user = "Big Joe" 
  | 
  +- latestProduct 
      | 
      +- url = "products/greenmouse.html" 
      | 
      +- name = "green mouse"
```

        数据模型类似于计算机的文件系统，latestProduct可以看作是目录，而user、url和name看作是文件，url和name文件位于latestProduct目录中（这只是一个比喻，实际并不存在） 
        当FreeMarker将上面的数据模型合并到模板中，就创建了下面的输出： 

```html
<html> 
<head> 
  <title>Welcome!</title> 
</head> 
<body> 
  <h1>Welcome Big Joe!</h1> 
  <p>Our latest product: 
  <a href="products/greenmouse.html">green mouse</a>! 
</body> 
</html>
```

### 数据模型 
        典型的数据模型是树型结构，可以任意复杂和深层次，如下面的例子： 

```
(root) 
  | 
  +- animals 
  |   | 
  |   +- mouse 
  |   |   |   
  |   |   +- size = "small" 
  |   |   |   
  |   |   +- price = 50 
  |   | 
  |   +- elephant 
  |   |   |   
  |   |   +- size = "large" 
  |   |   |   
  |   |   +- price = 5000 
  |   | 
  |   +- python 
  |       |   
  |       +- size = "medium" 
  |       |   
  |       +- price = 4999 
  | 
  +- test = "It is a test" 
  | 
  +- whatnot 
      | 
      +- because = "don't know"
```

        类似于目录的变量称为hashes，包含保存下级变量的唯一的查询名字 
       类似于文件的变量称为scalars，保存单值 
        scalars保存的值有两种类型：字符串（用引号括起，可以是单引号或双引号）和数字（不要用引号将数字括起，这会作为字符串处理） 
       对scalars的访问从root开始，各部分用“.”分隔，如animals.mouse.price 
        另外一种变量是sequences，和hashes类似，只是不使用变量名字，而使用数字索引，如下面的例子： 

```html
(root) 
  | 
  +- animals 
  |   | 
  |   +- (1st) 
  |   |   | 
  |   |   +- name = "mouse" 
  |   |   | 
  |   |   +- size = "small" 
  |   |   | 
  |   |   +- price = 50 
  |   | 
  |   +- (2nd) 
  |   |   | 
  |   |   +- name = "elephant" 
  |   |   | 
  |   |   +- size = "large" 
  |   |   | 
  |   |   +- price = 5000 
  |   | 
  |   +- (3rd) 
  |       | 
  |       +- name = "python" 
  |       | 
  |       +- size = "medium" 
  |       | 
  |       +- price = 4999 
  | 
  +- whatnot 
      | 
      +- fruits 
          | 
          +- (1st) = "orange" 
          | 
          +- (2nd) = "banana"
```

        这种对scalars的访问使用索引，如animals[0].name 
### 模板 
         在FreeMarker模板中可以包括下面三种特定部分： 
         ${…}：称为interpolations，FreeMarker会在输出时用实际值进行替代 
        FTL标记（FreeMarker模板语言标记）：类似于HTML标记，为了与HTML标记区分，用#开始（有些以@开始，在后面叙述） 
        注释：包含在<#--和-->（而不是<!--和-->）之间 
        下面是一些使用指令的例子： 
#### if指令 

```html
<#if animals.python.price < animals.elephant.price> 
  Pythons are cheaper than elephants today. 
<#else> 
  Pythons are not cheaper than elephants today. 
</#if>
```
  
#### list指令 
<p>We have these animals: 

``html
<table border=1> 
  <tr><th>Name<th>Price 
  <#list animals as being> 
  <tr><td>${being.name}<td>${being.price} Euros 
  </#list> 
</table>
```
 
输出为： 
<p>We have these animals: 

```html
<table border=1> 
  <tr><th>Name<th>Price 
  <tr><td>mouse<td>50 Euros 
  <tr><td>elephant<td>5000 Euros 
  <tr><td>python<td>4999 Euros 
</table>
```
 
#### include指令 

```html
<html> 
<head> 
  <title>Test page</title> 
</head> 
<body> 
  <h1>Test page</h1> 
  <p>Blah blah... 
<#include "/copyright_footer.html"> 
</body> 
</html>
```
   
#### 一起使用指令 
<p>We have these animals: 

```html
<table border=1> 
  <tr><th>Name<th>Price 
  <#list animals as being> 
  <tr> 
    <td> 
      <#if being.size = "large"><b></#if> 
      ${being.name} 
      <#if being.size = "large"></b></#if> 
    <td>${being.price} Euros 
  </#list> 
</table>
```

## 常用语法
EG.一个对象BOOK
### 1.输出 

```html
${book.name}
```

空值判断：
```html
${book.name?if_exists },
${book.name?default(‘xxx’)}//默认值xxx
${ book.name!"xxx"}//默认值xxx
```

日期格式：
```html
${book.date?string('yyyy-MM-dd')}
```

数字格式：
```html
${book?string.number}--20
${book?string.currency}--<#-- $20.00 -->
${book?string.percent}—<#-- 20% -->
```

插入布尔值：

```html
<#assign foo=ture />
${foo?string("yes","no")} <#-- yes -->
```
### 2．逻辑判断
a:

```html
<#if condition>...
<#elseif condition2>...
<#elseif condition3>......
<#else>...
```

其中空值判断可以写成
```html
<#if book.name?? >
```

 
b:

```html
<#switch value>
<#case refValue1>
...
<#break>
<#case refValue2>
...
<#break>
...
<#case refValueN>
...
<#break>
<#default>
...
```

 
### 3．循环读取

```html
<#list sequence as item>
...
```

### 空值判断


```html
<#if bookList?size = 0>
e.g.
<#list employees as e>
${e_index}. ${e.name}
```

输出:

```html
1. Readonly
2. Robbin
```


freemarker中Map的使用

```html
<#list testMap?keys as testKey> 
       < option value="${testKey}" > 
              ${testMap[testKey]} 
     </option> 
</#list>
```



## 解析FreeMarker视图
 
声明一个针对FreeMarker的视图解析器：
 
  
```xml
<bean id="viewResolver" class="org.springframework.
          ➥web.servlet.view.freemarker.FreeMarkerViewResolver">
    <property name="suffix"><value>.ftl</value></property>
  </bean>
```
FreeMarkerViewResolver和VelocityViewResolver或InternalResourceViewResolver的工作机制相同。模板资源是通过在视图的逻辑名上增加prefix属性的值作为前缀，以及增加suffix属性的值作为后缀进行解析的。和VelocityViewResolver一样，在这里我们又一次只设置suffix属性，因为模板的路径已经在FreeMarkerConfigurer的templateLoaderPath属性中定义了。
暴露请求和会话属性。你看到如何告诉VelocityViewResolver将请求和会话属性复制到模型map中，从而它们能够在模板中作为变量使用。采用同样的方式配置FreeMarkerViewResolver，可以将请求和会话属性作为变量暴露给FreeMarker模板使用。要做到这一点，可以设置exposeRequestAttributes或者exposeSessionAttributes为true：
 
  
```xml
<bean id="viewResolver" class="org.springframework.
          web.servlet.view.freemarker.FreeMarkerViewResolver">
  …
    <property name="exposeRequestAttributes">
      <value>true</value>
    </property>
    <property name="exposeSessionAttributes">
      <value>true</value>
    </property>
  </bean>
```

 
这里，两个属性都被设置为true。结果是请求和会话属性都被复制到模板的属性集中，可以使用FreeMarker的表达式语言来访问并显示。

## 数据源+freemarker+servlet生成xml文件  
### 一.步骤:
#### 1.在server.xml文件中建立数据源.

```xml
<Service name="Cms">
      <Connector debug="0" enableLookups="false" port="8084" protocol="AJP/1.3" redirectPort="8443"/>
     <Connector acceptCount="100" connectionTimeout="20000" debug="0" disableUploadTimeout="true" 
 
 enableLookups="false" maxSpareThreads="75" maxThreads="150" minSpareThreads="25" port="8081" redirectPort="8443"/>
     <Engine defaultHost="localhost_Cms" name="Catalina_Cms">
       <Logger className="org.apache.catalina.logger.FileLogger" prefix="localhost_cmt_log." suffix=".txt" timestamp="true"/>
       <Realm className="org.apache.catalina.realm.UserDatabaseRealm"/>
       <Host autoDeploy="true" debug="0" name="localhost_Cms" unpackWARs="true" xmlNamespaceAware="false" xmlValidation="false">
          <Context debug="0" docBase="D:WorkspaceCMSweb" path="/" reloadable="true" workDir="D:WorkspaceCMSj2src"> 
           <Resource name="jdbc/news_DB" auth="Container" type="javax.sql.DataSource"/>
         <ResourceParams name="jdbc/news_DB">
      <parameter>
        <name>factory</name>
        <!-- DBCP Basic Datasource Factory -->
        <value>org.apache.commons.dbcp.BasicDataSourceFactory</value>
      </parameter>
       
      <parameter>
        <name>maxActive</name>
        <value>1000</value>
      </parameter>
      <parameter>
         <name>validationQuery</name>
         <value>select 1+1</value>
      </parameter>
      <parameter>
        <name>maxIdle</name>
        <value>100</value>
      </parameter>
      <parameter>
        <name>maxWait</name>
        <value>10000</value>
      </parameter>
      <parameter>
        <name>removeAbandoned</name>
        <value>true</value>
      </parameter>
      <parameter>
        <name>removeAbandonedTimeout</name>
        <value>60</value>
      </parameter>
      <parameter>
        <name>logAbandoned</name>
        <value>false</value>
      </parameter>
       
      <parameter>
        <name>username</name>
        <value>aaaa</value>
      </parameter>
      <parameter>
        <name>password</name>
        <value>bbbbbb</value>
      </parameter>
      <parameter>
        <name>driverClassName</name>
        <value>net.sourceforge.jtds.jdbc.Driver</value>
      </parameter>
      <parameter>
        <name>url</name>
        <value>jdbc:jtds:sqlserver://111.111.111.111:1433/cms</value>
      </parameter>
       </ResourceParams>
       
    </Context>
       </Host>
 </Engine>
   </Service>
```
#### 2.在web.xml文件中配置servlet 
    
```xml
<servlet>
         <description>generate xml file</description>
         <servlet-name>NewsXmlServlet</servlet-name>
         <servlet-class>xml.NewsXmlServlet</servlet-class>
     </servlet>
     <servlet-mapping>
         <servlet-name>NewsXmlServlet</servlet-name>
         <url-pattern>/xmlServlet</url-pattern>
     </servlet-mapping>
     <servlet>
```

#### 3.newsXmlServlet.java
 
```java
package xml;
 
 import java.io.*;
 import java.nio.charset.Charset;
 import java.sql.Connection;
 import java.sql.ResultSet;
 import java.sql.SQLException;
 import java.sql.Statement;
 import java.util.ArrayList;
 import java.util.HashMap;
 import java.util.List;
 import java.util.Map;
 
 import javax.naming.Context;
 import javax.naming.InitialContext;
 import javax.naming.NamingException;
 import javax.servlet.ServletException;
 import javax.servlet.http.HttpServlet;
 import javax.servlet.http.HttpServletRequest;
 import javax.servlet.http.HttpServletResponse;
 import javax.sql.DataSource;
 
 import freemarker.template.Configuration;
 import freemarker.template.Template;
 import freemarker.template.TemplateException;
 
 import java.util.Locale;
 
  public class NewsXmlServlet extends HttpServlet{
 
     protected void doGet(HttpServletRequest req, HttpServletResponse resp)
              throws ServletException, IOException {
         // TODO Auto-generated method stub
          try {
             Connection conn=null;
             Context ctx = new InitialContext();
             DataSource ds=(DataSource)ctx.lookup("java:comp/env/jdbc/news_DB");
             conn=ds.getConnection();
             Statement stmt=conn.createStatement();
             ResultSet rs=stmt.executeQuery("select url,updatetime,tpf_edu_contentTitle,tpf_edu_contentneirong,tpf_edu_contentlaiyuan,tpf_edu_contentkeyword from tp5__edu_content where url<>'' and url is not null and dateDiff(d,updatetime,getDate())=1 order by updatetime desc");
             Configuration cfg=new Configuration();
             cfg.setDirectoryForTemplateLoading(new File("E:/wwwroot/CMS/web/WEB-INF/classes/xml"));
             Template tem=cfg.getTemplate("news.ftl");
             
             List list=new ArrayList();
             OutputStreamWriter out=new OutputStreamWriter(System.out);
              while(rs.next()){
                 Map item=new HashMap();
                 item.put("title",rs.getString(3));
                 item.put("link","http://test.com.cn"+rs.getString(1));
                 item.put("pubdate",rs.getTimestamp(2));
                 item.put("content",DelHtml(rs.getString(4)));
                 item.put("source",rs.getString(5));
                 item.put("keywords",DelHtml(rs.getString(6)));
                 list.add(item);
             }
             Map data=new HashMap();
             data.put("items",list);
             StringWriter writer=new StringWriter();
             tem.process(data,writer);
             String content=writer.toString();
             writer.close();
             createXml(content);
             out.close();
             //resp.setContentType("text/xml; charset=utf-8");
             //resp.getWriter().write(content);
             
          } catch (NamingException e) {
             // TODO Auto-generated catch block
             e.printStackTrace();
          } catch (SQLException e) {
             // TODO Auto-generated catch block
             e.printStackTrace();
          } catch (TemplateException e) {
             // TODO Auto-generated catch block
             e.printStackTrace();
         }
 
     }
      public String DelHtml(String content){
         String contents=content.replaceAll("<\/?\s*(\S+)(\s*[^>]*)?\s*\/?>","");
          contents=contents.replaceAll("&ldquo;", "”");
          contents=contents.replaceAll("&rdquo;","”");
          contents=contents.replaceAll("&ldquo;", "‘");
          contents=contents.replaceAll("&rdquo;","’");
          contents=contents.replaceAll("&middot;","•");
          contents=contents.replaceAll("&mdash;","—");
          contents=contents.replaceAll("&hellip;","…");
          contents=contents.replaceAll("&nbsp;","");
          contents=contents.replaceAll(","," ");
         return contents;        
     }
      public void createXml(String fileContent){
          try {
             String filePath="E:/wwwroot/cmsHtml/education/news.xml";
             File fileXml=new File(filePath);
              if(!fileXml.exists()){            
                 fileXml.createNewFile();
             }
             
              /*FileWriter fileWriter=new FileWriter(fileXml);
             fileWriter.
             fileWriter.write(fileContent);
             fileWriter.close();*/
                 OutputStreamWriter writer=new OutputStreamWriter(new FileOutputStream(fileXml), Charset.forName("utf-8"));     
                 writer.write(fileContent);
                 writer.close();
              } catch (IOException e) {
                 // TODO Auto-generated catch block
                 e.printStackTrace();
         }
     }
     
      public void destroy() {
         // TODO Auto-generated method stub
         super.destroy();
     }
 
      public void init() throws ServletException {
         // TODO Auto-generated method stub
         super.init();
     }
     
 }
```

 
#### 4.news.ftl
 
 
```xml
<?xml version="1.0" encoding="utf-8" ?>
 <document>
     <webSite>edu.aweb.com.cn</webSite>
     <webMaster>webmaster@aweb.com.cn</webMaster>
     <updatePeri>1440</updatePeri>
     <#list items as it>
     <item>
          <title><![CDATA[${it.title}]]></title>
          <link>${it.link}</link>
          <pubDate>${it.pubdate}</pubDate>
          <text><![CDATA[${it.content}]]></text>
         <image/>
          <source>${it.source}</source>
          <keywords><![CDATA[${it.keywords}]]></keywords>
     </item>
     </#list>
 </document>
```

## Spring中使用FreeMaker或Vilocity模板发送邮件

 本文以用户注册后为用户发送一封邮件为例子，讲述如何在Spring中使用FreeMaker或Vilocity发送邮件。

### Spring配置文件：

xml 代码


```xml
<bean id="mailSender" class="org.springframework.mail.javamail.JavaMailSenderImpl">   
 	        <property name="host" value="smtp.163.com"/>   
 	        <property name="username" value="test"/>   
 	        <property name="password" value="123456"/>   
 	        <property name="javaMailProperties">   
 	              <props>   
 	                <prop key="mail.smtp.auth">trueprop>   
 	              props>   
 	        property>   
 	    bean>   
 	   
 	       
 	    <bean id="freeMarkerConfigurer"                    class="org.springframework.web.servlet.view.freemarker.FreeMarkerConfigurer">   
 	        <property name="templateLoaderPath" value="/WEB-INF/freemakertemplate/" />   
 	        <property name="freemarkerSettings">   
 	            <props>   
 	                <prop key="template_update_delay">0prop>   
 	                <prop key="default_encoding">GBKprop>   
 	                <prop key="locale">zh_CNprop>   
 	            props>   
 	        property>   
 	    bean>   
 	        
 	       
 	    <bean id="velocityEngine" class="org.springframework.ui.velocity.VelocityEngineFactoryBean">   
 	        <property name="resourceLoaderPath" value="/WEB-INF/vilocitytemplate/" />   
 	        <property name="velocityProperties">   
 	            <props>   
 	                <prop key="velocimacro.library">*.vmprop>   
 	                <prop key="default.contentType">text/html; charset=utf-8prop>   
 	                <prop key="output.encoding">utf-8prop>   
 	                <prop key="input.encoding">utf-8prop>   
 	            props>   
 	        property>   
 	    bean>   
 	        
 	     <bean id="mailMessage" class="org.springframework.mail.SimpleMailMessage" singleton="false">   
 	        <property name="from" value="test@163.com"/>   
 	    bean>   
 	        
 	    <bean id="mailEngine" class="test.MailEngine">   
 	        <property name="mailSender" ref="mailSender"/>   
 	           
 	        <property name="velocityEngine" ref="velocityEngine"/>   
 	          
 	       <property name="freeMarkerConfigurer" ref="freeMarkerConfigurer" />   
 	    bean>
```

    
java 代码
MailEngine类：    
 
```java
	public class MailEngine {    
 	    protected static final Log log = LogFactory.getLog(MailEngine.class);    
 	   
 	//    private FreeMarkerConfigurer freeMarkerConfigurer;    
 	    private VelocityEngine velocityEngine;    
 	    private MailSender mailSender;    
 	   
 	//    public void setFreeMarkerConfigurer(    
 	//            FreeMarkerConfigurer freeMarkerConfigurer) {    
 	//        this.freeMarkerConfigurer = freeMarkerConfigurer;    
 	//    }    
 	   
 	    public void setMailSender(MailSender mailSender) {    
 	        this.mailSender = mailSender;    
 	    }    
 	   
 	    public void setVelocityEngine(VelocityEngine velocityEngine) {    
 	        this.velocityEngine = velocityEngine;    
 	    }    
 	   
 	    /**   
 	     * 通过模板产生邮件正文   
 	     * @param templateName    邮件模板名称   
 	     * @param map            模板中要填充的对象   
 	     * @return 邮件正文（HTML）   
 	     */   
 	    public String generateEmailContent(String templateName, Map map) {    
 	        //使用FreeMaker模板    
 	//        try {    
 	//            Configuration configuration = freeMarkerConfigurer.getConfiguration();    
 	//            Template t = configuration.getTemplate(templateName);    
 	//            return FreeMarkerTemplateUtils.processTemplateIntoString(t, map);    
 	//        } catch (TemplateException e) {    
 	//            log.error("Error while processing FreeMarker template ", e);    
 	//        } catch (FileNotFoundException e) {    
 	//            e.printStackTrace();    
 	//            //log.error("Error while open template file ", e);    
 	//        } catch (IOException e) {    
 	//            log.error("Error while generate Email Content ", e);    
 	//        }    
 	            
 	//        使用Vilocity模板    
 	        try {    
 	           return VelocityEngineUtils.mergeTemplateIntoString(velocityEngine, templateName, map);    
 	        } catch (VelocityException e) {    
 	            log.error("Error while processing Vilocity template ", e);    
 	        }    
 	            
 	        return null;    
 	    }    
 	   
 	    /**   
 	     * 发送邮件   
 	     * @param emailAddress        收件人Email地址的数组   
 	     * @param fromEmail            寄件人Email地址, null为默认寄件人web@vnvtrip.com   
 	     * @param bodyText            邮件正文   
 	     * @param subject            邮件主题   
 	     * @param attachmentName    附件名   
 	     * @param resource            附件   
 	     * @throws MessagingException   
 	     */   
 	    public void sendMessage(String[] emailAddresses, String fromEmail,    
 	            String bodyText, String subject, String attachmentName,    
 	            ClassPathResource resource) throws MessagingException {    
 	        MimeMessage message = ((JavaMailSenderImpl) mailSender)    
 	                .createMimeMessage();    
 	   
 	        // use the true flag to indicate you need a multipart message    
 	        MimeMessageHelper helper = new MimeMessageHelper(message, true);    
 	   
 	        helper.setTo(emailAddresses);    
 	        if(fromEmail != null){    
 	            helper.setFrom(fromEmail);    
 	        }    
 	        helper.setText(bodyText, true);    
 	        helper.setSubject(subject);    
 	            
 	        if(attachmentName!=null && resource!=null)    
 	            helper.addAttachment(attachmentName, resource);    
 	   
 	        ((JavaMailSenderImpl) mailSender).send(message);    
 	    }    
 	   
 	    /**   
 	     * 发送简单邮件   
 	     * @param msg       
 	     */   
 	    public void send(SimpleMailMessage msg) {    
 	        try {    
 	            ((JavaMailSenderImpl) mailSender).send(msg);    
 	        } catch (MailException ex) {    
 	            //log it and go on    
 	            log.error(ex.getMessage());    
 	        }    
 	    }    
 	        
 	    /**   
 	     * 使用模版发送HTML格式的邮件   
 	     *   
 	     * @param msg          装有to,from,subject信息的SimpleMailMessage   
 	     * @param templateName 模版名,模版根路径已在配置文件定义于freemakarengine中   
 	     * @param model        渲染模版所需的数据   
 	     */   
 	    public void send(SimpleMailMessage msg, String templateName, Map model) {    
 	        //生成html邮件内容    
 	        String content = generateEmailContent(templateName, model);    
 	        MimeMessage mimeMsg = null;    
 	        try {    
 	            mimeMsg = ((JavaMailSenderImpl) mailSender).createMimeMessage();    
 	            MimeMessageHelper helper = new MimeMessageHelper(mimeMsg, true, "utf-8");    
 	            helper.setTo(msg.getTo());    
 	                
 	            if(msg.getSubject()!=null)    
 	                helper.setSubject(msg.getSubject());    
 	                
 	            if(msg.getFrom()!=null)    
 	                helper.setFrom(msg.getFrom());    
 	                
 	            helper.setText(content, true);    
 	                
 	            ((JavaMailSenderImpl) mailSender).send(mimeMsg);    
 	        } catch (MessagingException ex) {    
 	            log.error(ex.getMessage(), ex);    
 	        }    
 	   
 	    }    
 	}    
 	   
 	//发送邮件：    
 	SimpleMailMessage message = (SimpleMailMessage) getBean("mailMessage");    
 	                message.setTo(user.getName() + "<" + user.getEmail() + ">");    
 	                    
 	                Map model = new HashMap();    
 	                model.put("user", user);    
 	                    
 	                MailEngine engine = (MailEngine)getBean("mailEngine");    
 	                //Vilocity模板    
 	                engine.send(message, "notifyUser.vm", model);    
 	                //FreeMaker模板    
 	                //engine.send(message, "NotifyUser.ftl", model);    
 	
```

以上的User为用户类。   
 
xml 代码
模板：    

```html
 	<html>   
 	<head>   
 	<meta http-equiv="Content-Type" content="text/html;charset=UTF-8">   
 	<title>用户注册通知title>   
 	head>   
 	<body>   
 	<p>${user.name} 您好，恭喜您，已经成为本站会员！p>   
 	<table>   
 	<tr><td>用户名：td><td>${user.name}td>tr>   
 	<tr><td>密码：td><td>${user.password}td>tr>   
 	table>   
 	body>   
 	html>   
```

