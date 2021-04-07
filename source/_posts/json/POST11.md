---
title: Gson和Jackson的使用
tags: Json
category: Json
date: 2018-01-24 15:44:36
---
![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0102.jpg)

现在json的第三方解析工作很多，如json-lib，gson，jackson，fastjson等，先学习下面两种。
<!--more-->

### JackSON使用：

Jackson主要使用ObjectMapper对象来进行序列化以及反序列化操作。

使用Jackson可以毫无压力的将复杂对象进行完全序列化，也可以将各种类型的对象反序列化

以下代码均省略异常处理

普通对象

#### 序列化Order order对象：

```java
ObjectMapper mapper = new ObjectMapper();
Writer strWriter = new StringWriter();
mapper.writeValue(strWriter, order);
String json = strWriter.toString();
```

#### 反序列化：

```java
Order order = mapper.readValue(json, Order.class);
```

链表、Map等

#### 序列化List<Order> orderList：

```java
ObjectMapper mapper = new ObjectMapper();
Writer strWriter = new StringWriter();
mapper.writeValue(strWriter, orderList);
String json = strWriter.toString();
```

####  反序列化：

需要用到TypeReference

```java
List<Order> orderList = mapper.readValue(json, new TypeReference<List<Order>>() {  
   }
);  
```

#### 反序列化List<LinkedHashMap<String,String>>

```java
List orderList = mapper.readValue(json, List.class);  
```

#### 解析某个字段

```java
JsonNode node = mapper.readTree(json);
String name= node.get("name");
```

#### Jackson的JSON操作方法

##### 准备工作

首先去官网下载Jackson工具包，下载地址http://wiki.fasterxml.com/JacksonDownload。Jackson有1.x系列和2.x系列，截止目前2.x系列的最新版本是2.2.3，2.x系列有3个jar包需要下载：

jackson-core-2.2.3.jar（核心jar包，[下载地址](http://repo1.maven.org/maven2/com/fasterxml/jackson/core/jackson-core/2.2.3/jackson-core-2.2.3.jar)）

jackson-annotations-2.2.3.jar（该包提供Json注解支持，[下载地址](http://repo1.maven.org/maven2/com/fasterxml/jackson/core/jackson-annotations/2.2.3/jackson-annotations-2.2.3.jar)）

jackson-databind-2.2.3.jar（[下载地址](http://repo1.maven.org/maven2/com/fasterxml/jackson/core/jackson-databind/2.2.3/jackson-databind-2.2.3.jar)）

```java
//JSON序列化和反序列化使用的User类
import java.util.Date;

public class User {
	private String name;
	private Integer age;
	private Date birthday;
	private String email;
	
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	
	public Integer getAge() {
		return age;
	}
	public void setAge(Integer age) {
		this.age = age;
	}
	
	public Date getBirthday() {
		return birthday;
	}
	public void setBirthday(Date birthday) {
		this.birthday = birthday;
	}
	
	public String getEmail() {
		return email;
	}
	public void setEmail(String email) {
		this.email = email;
	}
}
```

##### JAVA对象转JSON[JSON序列化]

```java

import java.io.IOException;
import java.text.ParseException;
import java.text.SimpleDateFormat;

import com.fasterxml.jackson.databind.ObjectMapper;

public class JacksonDemo {
	public static void main(String[] args) throws ParseException, IOException {
		User user = new User();
		user.setName("小民");	
		user.setEmail("xiaomin@sina.com");
		user.setAge(20);
		
		SimpleDateFormat dateformat = new SimpleDateFormat("yyyy-MM-dd");
		user.setBirthday(dateformat.parse("1996-10-01"));		
		
		/**
		 * ObjectMapper是JSON操作的核心，Jackson的所有JSON操作都是在ObjectMapper中实现。
		 * ObjectMapper有多个JSON序列化的方法，可以把JSON字符串保存File、OutputStream等不同的介质中。
		 * writeValue(File arg0, Object arg1)把arg1转成json序列，并保存到arg0文件中。
		 * writeValue(OutputStream arg0, Object arg1)把arg1转成json序列，并保存到arg0输出流中。
		 * writeValueAsBytes(Object arg0)把arg0转成json序列，并把结果输出成字节数组。
		 * writeValueAsString(Object arg0)把arg0转成json序列，并把结果输出成字符串。
		 */
		ObjectMapper mapper = new ObjectMapper();
		
		//User类转JSON
		//输出结果：{"name":"小民","age":20,"birthday":844099200000,"email":"xiaomin@sina.com"}
		String json = mapper.writeValueAsString(user);
		System.out.println(json);
		
		//Java集合转JSON
		//输出结果：[{"name":"小民","age":20,"birthday":844099200000,"email":"xiaomin@sina.com"}]
		List<User> users = new ArrayList<User>();
		users.add(user);
		String jsonlist = mapper.writeValueAsString(users);
		System.out.println(jsonlist);
	}
}
```

##### JSON转Java类[JSON反序列化]

```java
import java.io.IOException;
import java.text.ParseException;
import com.fasterxml.jackson.databind.ObjectMapper;

public class JacksonDemo {
	public static void main(String[] args) throws ParseException, IOException {
		String json = "{\"name\":\"小民\",\"age\":20,\"birthday\":844099200000,\"email\":\"xiaomin@sina.com\"}";
		
		/**
		 * ObjectMapper支持从byte[]、File、InputStream、字符串等数据的JSON反序列化。
		 */
		ObjectMapper mapper = new ObjectMapper();
		User user = mapper.readValue(json, User.class);
		System.out.println(user);
	}
}
```

##### JSON注解

Jackson提供了一系列注解，方便对JSON序列化和反序列化进行控制，下面介绍一些常用的注解。

@JsonIgnore 此注解用于属性上，作用是进行JSON操作时忽略该属性。

@JsonFormat 此注解用于属性上，作用是把Date类型直接转化为想要的格式，如@JsonFormat(pattern = "yyyy-MM-dd HH-mm-ss")。

@JsonProperty 此注解用于属性上，作用是把该属性的名称序列化为另外一个名称，如把trueName属性序列化为name，@JsonProperty("name")。

```java
import java.util.Date;
import com.fasterxml.jackson.annotation.*;

public class User {
	private String name;
	
	//不JSON序列化年龄属性
	@JsonIgnore 
	private Integer age;
	
	//格式化日期属性
	@JsonFormat(pattern = "yyyy年MM月dd日")
	private Date birthday;
	
	//序列化email属性为mail
	@JsonProperty("mail")
	private String email;
	
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	
	public Integer getAge() {
		return age;
	}
	public void setAge(Integer age) {
		this.age = age;
	}
	
	public Date getBirthday() {
		return birthday;
	}
	public void setBirthday(Date birthday) {
		this.birthday = birthday;
	}
	
	public String getEmail() {
		return email;
	}
	public void setEmail(String email) {
		this.email = email;
	}
}
```

```java
import java.io.IOException;
import java.text.ParseException;
import java.text.SimpleDateFormat;

import com.fasterxml.jackson.databind.ObjectMapper;

public class JacksonDemo {

	public static void main(String[] args) throws ParseException, IOException {
		User user = new User();
		user.setName("小民");	
		user.setEmail("xiaomin@sina.com");
		user.setAge(20);
		
		SimpleDateFormat dateformat = new SimpleDateFormat("yyyy-MM-dd");
		user.setBirthday(dateformat.parse("1996-10-01"));		
		
		ObjectMapper mapper = new ObjectMapper();
		String json = mapper.writeValueAsString(user);
		System.out.println(json);
		//输出结果：{"name":"小民","birthday":"1996年09月30日","mail":"xiaomin@sina.com"}
	}
}
```

### GSON的使用：

Gson（又称Google Gson）是Google公司发布的一个开放源代码的Java库，主要用途为串行化Java对象为JSON字符串，或反串行化JSON字符串成Java对象。GSON核心jar包不到1M，非常精简，但提供的功能无疑是非常强大的，如果使用JDK自带的JSON解析API，使用起来相对比较繁琐一点，而且代码量较多。

#### GSON目前的发展状况

初始版本	2008年5月22日
最新版本(稳定版本)	2.2.4/2013年5月13日；2个月前
编程语言	Java
操作平台	各种平台
许可协议	Apache License 2.0
官方网站	http://code.google.com/p/google-gson/
下载地址	http://code.google.com/p/google-gson/downloads/list
GSON帮助文档	http://google-gson.googlecode.com/svn/trunk/gson/docs/javadocs/index.html

#### 使用GSON转换时报的一个异常。

```xml
Exception in thread "main" com.google.gson.JsonSyntaxException: java.lang.IllegalStateException: Expected BEGIN_OBJECT but was BEGIN_ARRAY at line 1 column 2  at com.google.gson.internal.bind.ReflectiveTypeAdapterFactory$Adapter.read(ReflectiveTypeAdapterFactory.java:176)  
    at com.google.gson.Gson.fromJson(Gson.java:803)  
    at com.google.gson.Gson.fromJson(Gson.java:768)  
    at com.google.gson.Gson.fromJson(Gson.java:717)  
    at com.google.gson.Gson.fromJson(Gson.java:689)  
    at com.demoone.UseGson.testOneBeanFromJSON(UseGson.java:54)  
    at com.demoone.UseGson.main(UseGson.java:89)  
Caused by: java.lang.IllegalStateException: Expected BEGIN_OBJECT but was BEGIN_ARRAY at line 1 column 2   //注意这句话跟数组集合有关  
    at com.google.gson.stream.JsonReader.beginObject(JsonReader.java:374)  
    at com.google.gson.internal.bind.ReflectiveTypeAdapterFactory$Adapter.read(ReflectiveTypeAdapterFactory.java:165)  
    ... 6 more  

```


下面贴出，笔者的测试源码，注意上述异常，在代码的注释中已经指出问题原因

```java
package com.demoone;  
import java.util.ArrayList;  
import java.util.List;    
import com.entity.Person;  
import com.google.gson.Gson;  
import com.google.gson.reflect.*;  
  
/***  
 * 使用gson 
 * 进行序列化,反序列化的操作 
 * json 
 * **/  
public class UseGson {       
 static Gson g=new Gson();  
 /** 
  * 由集合类型的实体类 
  * 转换成Json类型  
  * **/  
    public static void testToJSON(){     
        List<Person> list=new ArrayList<>();      
        for(int i=0;i<2;i++){  
         Person p=new Person(i, "散仙"+i, "北京市"+i, "54152541@qq.com", 500.0);  
            list.add(p);  
        }  
        String json=g.toJson(list);  
        System.out.println(json);          
    }      
    /** 
     * 由json转换为实体类 
     * */  
    public static void testOneBeanFromJSON(){  
        /** 
         * 转换一个Java Bean的时候 
         * 记住不能使用[],只有在使用集合的时候才可以使用  
         * 如上图贴的那个异常就是由此原因造成的，另外 
         * 格式要规范，中间不能出现空格字符 
         *  
         * **/  
        String jsonBean="[{\"id\":0,\"name\":\"散仙0\",\"address\":\"北京市0\",\"email\":\"54152541@qq.com\",\"money\":500.0}]";  
         Person p=g.fromJson(jsonBean, Person.class);  
         System.out.println(p);  
   
    }  
      
    /** 
     * 由json转换为实体类集合 
     *  
     * */  
    public static void testListBeanFromJSON(){    
         /** 
          * 转换一个集合 的实体Bean 
          * 注意前面加上[]方括号，代表一个数组 
          * **/   
         String  jsonBeanList="[{\"id\":0,\"name\":\"散仙0\",\"address\":\"北京市0\",\"email\":\"54152541@qq.com\",\"money\":500.0},{\"id\":1,\"name\":\"散仙1\",\"address\":\"北京市1\",\"email\":\"54152541@qq.com\",\"money\":500.0}]";  
           
         List<Person> lists=g.fromJson(jsonBeanList,new   TypeToken<List<Person>>(){}.getType());  
         System.out.println("反序列之后");  
         for(Person pp:lists){  
               
             System.out.println(pp);  
         }  
    }     
      
    public static void main(String[] args) {        
         testListBeanFromJSON();//测试集合反序列化  
         testOneBeanFromJSON();//测试单个实体类反序列化  
         //testToJSON();//测试序列化            
    }   
}  
```

运行结果:

```xml
testListBeanFromJSON()  方法:  
反序列之后  
编号：0  名字:散仙0   地址:北京市0   E-mail:54152541@qq.com  钱:500.0  
编号：1  名字:散仙1   地址:北京市1   E-mail:54152541@qq.com  钱:500.0  
testOneBeanFromJSON()  方法:  
编号：0  名字:散仙0   地址:北京市0   E-mail:54152541@qq.com  钱:500.0  
```

实体类Person:

```java
package com.entity;  
  
/** 
 * 实体类 
 * **/  
public class Person {  
      
    private int id;  
    private String name;  
    private String address;  
    private String email;  
    private double money;  
      
    @Override  
    public String toString() {  
        // TODO Auto-generated method stub  
        return  "编号："+id+"  名字:"+name+"   地址:"+address+"   E-mail:"+email+"  钱:"+money;  
    }  
    public Person() {  
        // TODO Auto-generated constructor stub  
    }  
    public Person(int id, String name, String address, String email,  
            double money) {  
        super();  
        this.id = id;  
        this.name = name;  
        this.address = address;  
        this.email = email;  
        this.money = money;  
    }  
    public int getId() {  
        return id;  
    }  
    public void setId(int id) {  
        this.id = id;  
    }  
    public String getName() {  
        return name;  
    }  
    public void setName(String name) {  
        this.name = name;  
    }  
    public String getAddress() {  
        return address;  
    }  
    public void setAddress(String address) {  
        this.address = address;  
    }  
    public String getEmail() {  
        return email;  
    }  
    public void setEmail(String email) {  
        this.email = email;  
    }  
    public double getMoney() {  
        return money;  
    }  
    public void setMoney(double money) {  
        this.money = money;  
    }  
}  
```

