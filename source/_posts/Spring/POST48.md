---
title: Spring Assert断言工具类使用
tags: Spring
category: Spring
abbrlink: 11911
date: 2018-01-10 21:34:36
---
![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0084.jpg)

Web 应用在接受表单提交的数据后都需要对其进行合法性检查，如果表单数据不合法，请求将被驳回。类似的，当我们在编写类的方法时，也常常需要对方法入参进行合法性检查，如果入参不符合要求，方法将通过抛出异常的方式拒绝后续处理。
<!--more-->
org.springframework.util.Assert

Assert断言工具类，通常用于数据合法性检查.

# 平时做判断

```java
if (message== null || message.equls("")) {  
    throw new IllegalArgumentException("输入信息错误!");  
}
```

# 用Assert工具类简化：  

```java
Assert.hasText((message, "输入信息错误!");
```

# Assert 类的常用断言方法：  
 
```java
Assert.notNull(Object object, "object is required")    -    对象非空 
Assert.isTrue(Object object, "object must be true")   -    对象必须为true   
Assert.notEmpty(Collection collection, "collection must not be empty")    -    集合非空  
Assert.hasLength(String text, "text must be specified")   -    字符不为null且字符长度不为0   
Assert.hasText(String text, "text must not be empty")    -     text 不为null且必须至少包含一个非空格的字符  
Assert.isInstanceOf(Class clazz, Object obj, "clazz must be of type [clazz]")    -    obj必须能被正确造型成为clazz 指定的类
```
举一个例子：有一个根据文件名获取输入流的方法：InputStream getData(String file)，为了使方法能够成功执行，必须保证 file 入参不能为 null 或空白字符，否则根本无须进行后继的处理。这时方法的编写者通常会在方法体的最前面编写一段对入参进行检测的代码，如下所示： 

```java
public InputStream getData(String file) { 
    if (file == null || file.length() == 0|| file.replaceAll("\\s", "").length() == 0) { 
        throw new IllegalArgumentException("file入参不是有效的文件地址"); 
    } 
… 
}
```

类似以上检测方法入参的代码是非常常见，但是在每个方法中都使用手工编写检测逻辑的方式并不是一个好主意。阅读 Spring 源码，您会发现 Spring 采用一个 org.springframework.util.Assert 通用类完成这一任务。 

Assert 翻译为中文为“断言”，使用过 JUnit的读者都熟知这个概念，它断定某一个实际的运行值和预期想一样，否则就抛出异常。Spring对方法入参的检测借用了这个概念，其提供的 Assert 类拥有众多按规则对方法入参进行断言的方法，可以满足大部分方法入参检测的要求。这些断言方法在入参不满足要求时就会抛出 IllegalArgumentException。下面，我们来认识一下 Assert 类中的常用断言方法： 

# Spring Assert的所有方法：

## 断言方法 说明 

1. notNull(Object object) 

当 object 不为 null 时抛出异常，notNull(Object object, String message) 方法允许您通过 message 定制异常信息。和 notNull() 方法断言规则相反的方法是 isNull(Object object)/isNull(Object object, String message)，它要求入参一定是 null； 

2. isTrue(boolean expression) / isTrue(boolean expression, String message)  

当 expression 不为 true 抛出异常； 

3. notEmpty(Collection collection) / notEmpty(Collection collection, String message)  

当集合未包含元素时抛出异常。 
notEmpty(Map map) / notEmpty(Map map, String message) 和 notEmpty(Object[] array, String message) / notEmpty(Object[] array, String message) 分别对 Map 和 Object[] 类型的入参进行判断； 

4. hasLength(String text) / hasLength(String text, String message) 

当 text 为 null 或长度为 0 时抛出异常； 

5. hasText(String text) / hasText(String text, String message) 

text 不能为 null 且必须至少包含一个非空格的字符，否则抛出异常； 

6. isInstanceOf(Class clazz, Object obj) / isInstanceOf(Class type, Object obj, String message) 

如果 obj 不能被正确造型为 clazz 指定的类将抛出异常； 

7. isAssignable(Class superType, Class subType) / isAssignable(Class superType, Class subType, String message)

subType 必须可以按类型匹配于 superType，否则将抛出异常； 

使用 Assert 断言类可以简化方法入参检测的代码，如 InputStream getData(String file) 在应用 Assert 断言类后，其代码可以简化为以下的形式： 

```java
public InputStream getData(String file){ 
    Assert.hasText(file,"file入参不是有效的文件地址"); 
    ① 使用 Spring 断言类进行方法入参检测 
… 
}
```

只记录这么多，具体自己去找Spring的工具类（import org.springframework.util.Assert）

查阅API　http://www.apihome.cn/api/spring/Assert.html
