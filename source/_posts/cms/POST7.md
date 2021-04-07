---
title: FreeMarker自定义TemplateDirectiveModel(一)
category:
  - Java
copyright: true
tags: freemarker
abbrlink: 21487
date: 2017-09-07 19:36:37
---
![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0031.jpg)

FreeMarker是一个用Java语言编写的模板引擎，它基于模板来生成文本输出。FreeMarker与Web容器无关，即在Web运行时，它并不知道Servlet或HTTP。它不仅可以用作表现层的实现技术，而且还可以用于生成XML，JSP或Java 等。
<!--more-->
##### 一、Freemarker的介绍

​    Freemarker 是一款模板引擎，是一种基于模版生成静态文件的通用 工具，它是为java程序员提供的一个开发包，或者说是一个类库，它不是面向最终用户的，而是为程序员提供了一款可以嵌入他们开发产品的应用程序。

​    Freemarker 是使用纯java编写的，为了提高页面的访问速度，需要把页面静态化， 那么Freemarker就是被用来生成html页面。

​    到目前为止，Freemarker使用越来越广泛，不光光只是它强大的生成技术，而且它能够与spring进行很好的集成。

​    现在开始一层层揭开它的神秘面纱。。

##### 二、Freemarker的准备条件

​    freemarker.2.3.16.jar  

##### 三、Freemarker生成静态页面的原理

​    Freemarker 生成静态页面，首先需要使用自己定义的模板页面，这个模板页面可以是最最普通的html，也可以是嵌套freemarker中的 取值表达式， 标签或者自定义标签等等，然后后台读取这个模板页面，解析其中的标签完成相对应的操作， 然后采用键值对的方式传递参数替换模板中的的取值表达式，做完之后 根据配置的路径生成一个新的html页面， 以达到静态化访问的目的。

##### 四、Freemarker提供的标签

Freemarker提供了很多有用 常用的标签，Freemarker标签都是<#标签名称>这样子命名的，${value} 表示输出变量名的内容 ，具体如下：

* list：该标签主要是进行迭代服务器端传递过来的List集合，比如：

```html
<#list nameList as names>  
  ${names} 
</#list>
```

​	name是list循环的时候取的一个循环变量，freemarker在解析list标签的时候，等价于：

```java
		for (String names : nameList) {
				System.out.println(names);
			}
```

* if：    该标签主要是做if判断用的，比如：

```html
<#if (names=="陈靖仇")>
 他的武器是: 十五~~
</#if>
```

​	这个是条件判断标签，要注意的是条件等式必须用括号括起来， 等价于：

```java
		if(names.equals("陈靖仇")){
				System.out.println("他的武器是: 十五~~");
			}
```

* include：该标签用于导入文件用的。

```html
<#include "include.html"/>  
```

​	这个导入标签非常好用，特别是页面的重用。

另外在静态文件中可以使用${} 获取值，取值方式和el表达式一样，非常方便。

​	下面举个例子（static.html）：

```html

<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>Insert title here</title>
</head>
<body>

描述：${description}
<br/>
集合大小:${nameList?size}
<br/>
迭代list集合：
<br/>
<#list nameList as names>
这是第${names_index+1}个人，叫做：<label style="color:red">${names}</label>
if判断：
<br/>
<#if (names=="陈靖仇")>
 他的武器是: 十五~~
<#elseif (names=="宇文拓")>       <#--注意这里没有返回而是在最后面--> 
 他的武器是: 轩辕剑~·
<#else>
她的绝招是：蛊毒~~
</#if>
<br/>
</#list>
迭代map集合：
<br/>
<#list weaponMap?keys as key>
key--->${key}<br/>
value----->${weaponMap[key]!("null")}
<#-- 
fremarker 不支持null, 可以用！ 来代替为空的值。
其实也可以给一个默认值  
value-----${weaponMap[key]?default("null")}
还可以 在输出前判断是否为null
<#if weaponMap[key]??></#if>都可以
-->

<br/>
</#list>
include导入文件：
<br/>
<#include "include.html"/>

</body>
</html>
```

​	实际代码：

```java

package com.chenghui.test;

import java.io.File;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.OutputStreamWriter;
import java.io.Writer;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

import freemarker.template.Configuration;
import freemarker.template.DefaultObjectWrapper;
import freemarker.template.Template;
import freemarker.template.TemplateException;

public class CreateHtml {
	public static void main(String[] args) {
		try {
			//创建一个合适的Configration对象
			Configuration configuration = new Configuration();
			configuration.setDirectoryForTemplateLoading(new File("D:\\project\\webProject\\WebContent\\WEB-INF\\template"));
			configuration.setObjectWrapper(new DefaultObjectWrapper());
			configuration.setDefaultEncoding("UTF-8");   //这个一定要设置，不然在生成的页面中 会乱码
			//获取或创建一个模版。
			Template template = configuration.getTemplate("static.html");
			Map<String, Object> paramMap = new HashMap<String, Object>();
			paramMap.put("description", "我正在学习使用Freemarker生成静态文件！");
			
			List<String> nameList = new ArrayList<String>();
			nameList.add("陈靖仇");
			nameList.add("玉儿");
			nameList.add("宇文拓");
			paramMap.put("nameList", nameList);
			
			Map<String, Object> weaponMap = new HashMap<String, Object>();
			weaponMap.put("first", "轩辕剑");
			weaponMap.put("second", "崆峒印");
			weaponMap.put("third", "女娲石");
			weaponMap.put("fourth", "神农鼎");
			weaponMap.put("fifth", "伏羲琴");
			weaponMap.put("sixth", "昆仑镜");
			weaponMap.put("seventh", null);
			paramMap.put("weaponMap", weaponMap);
			
			Writer writer  = new OutputStreamWriter(new FileOutputStream("success.html"),"UTF-8");
			template.process(paramMap, writer);
			
			System.out.println("恭喜，生成成功~~");
		} catch (IOException e) {
			e.printStackTrace();
		} catch (TemplateException e) {
			e.printStackTrace();
		}
		
	}
}
```

​	这样子基本上可以算的上可以简单的去做一点简单的生成了，但是要在实际中去运用，还是差的很远的，因为freemarker给的标签完全满足不了我们的需要，这时候就需要自定义标签来完成我们的需求了。。

##### 五、Freemarker自定义标签

Freemarker自定义标签就是自己写标签，然后自己解析，完全由自己来控制标签的输入输出，极大的为程序员提供了很大的发挥空间。

基于步骤：

​       以前写标签需要在<后加# ，但是freemarker要识别自定义标签需要在后面加上@，然后后面可以定义一些参数，当程序执行template.process(paramMap, out);,就会去解析整个页面的所有的freemarker标签。

​     自定义标签 需要自定义一个类，然后实现TemplateDirectiveModel，重写execute方法，完成获取参数，根据参数do something等等。

​    将自定义标签与解析类绑定在一起需要在paramMap中放入该解析类的实例，存放的key与自定义标签一致即可。

​    注意：在自定义标签中，如果标签内什么也没有，开始标签和结束标签绝对不能再同一行，不然会报错 freemarker.log.JDK14LoggerFactory$JDK14Logger error

* 例:static.html模板

```html

<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>Insert title here</title>
</head>
<body>
<#--自定义变量-->
<#assign num='hehe'/>
${num}
<br/>
自定义标签
  <@content name="chenghui" age="120">
  	${output}
  	${append}
  </@content>
  
</body>
</html>
```

* static.html模板解析类：

​	参数解释：

TemplateDirectiveModel接口是freemarker自定标签或者自定义指令的核心处理接口。通过实现该接口，用户可以自定义标签（指令）进行任意操作， 任意文本写入模板的输出。
该接口中只定义了如下方法，当模板页面遇到用户自定义的标签指令时，该方法会被执行。
public void execute(Environment env, Map params, TemplateModel[] loopVars,  TemplateDirectiveBody body) throws TemplateException, IOException;

>  @param  env：系统环境变量，通常用它来输出相关内容，如Writer out = env.getOut();

> @param  params：自定义标签传过来的对象，其key=自定义标签的参数名，value值是TemplateModel类型，而TemplateModel是一个接口类型，通常我们都使用TemplateScalarModel接口来替代它获取一个String 值，如TemplateScalarModel.getAsString();当然还有其它常用的替代接口，如TemplateNumberModel获取number，TemplateHashModel等

> @param  loopVars  循环替代变量

> @param  body 用于处理自定义标签中的内容，如<@myDirective>将要被处理的内容</@myDirective>；当标签是<@myDirective />格式时，body=null

```java

package com.chenghui.test;

import static freemarker.template.ObjectWrapper.DEFAULT_WRAPPER;

import java.io.IOException;
import java.io.Writer;
import java.util.Map;


import freemarker.core.Environment;
import freemarker.template.TemplateDirectiveBody;
import freemarker.template.TemplateDirectiveModel;
import freemarker.template.TemplateException;
import freemarker.template.TemplateModel;
import freemarker.template.TemplateModelException;
import freemarker.template.TemplateNumberModel;
import freemarker.template.TemplateScalarModel;

/**
 * 自定义标签解析类
 * @author Administrator
 *
 */
public class ContentDirective implements TemplateDirectiveModel{

	private static final String PARAM_NAME = "name";
	private static final String PARAM_AGE = "age";
	
	@Override
	public void execute(Environment env, Map params,TemplateModel[] loopVars,
			TemplateDirectiveBody body) throws TemplateException, IOException {
		if(body==null){
			throw new TemplateModelException("null body");
		}else{
			String name = getString(PARAM_NAME, params);
			Integer age = getInt(PARAM_AGE, params);
			//接收到参数之后可以根据做具体的操作，然后将数据再在页面中显示出来。
			if(name!=null){
				env.setVariable("output", DEFAULT_WRAPPER.wrap("从ContentDirective解析类中获得的参数是："+name+", "));
			}
			if(age!=null){
				env.setVariable("append", DEFAULT_WRAPPER.wrap("年龄："+age));
			}
			Writer out = env.getOut();
			out.write("从这里输出可以再页面看到具体的内容，就像document.writer写入操作一样。<br/>");
			body.render(out);
			
			/*
		  	如果细心的话，会发现页面上是显示out.write（）输出的语句，然后再输出output的内容，
			可见 在body在解析的时候会先把参数放入env中，在页面遇到对应的而来表单时的才会去取值
			但是，如果该表单时不存在，就会报错，  我觉得这里freemarker没有做好，解析的时候更加会把错误暴露在页面上。
			可以这样子弥补${output!"null"},始终感觉没有el表达式那样好。
			*/
		}
	}
	
	/**
	 * 获取String类型的参数的值
	 * @param paramName
	 * @param paramMap
	 * @return
	 * @throws TemplateModelException
	 */
	public static String getString(String paramName, Map<String, TemplateModel> paramMap) throws TemplateModelException{
		TemplateModel model = paramMap.get(paramName);
		if(model == null){
			return null;
		}
		if(model instanceof TemplateScalarModel){
			return ((TemplateScalarModel)model).getAsString();
		}else if (model instanceof TemplateNumberModel) {
			return ((TemplateNumberModel)model).getAsNumber().toString();
		}else{
			throw new TemplateModelException(paramName);
		}
	}
	
	/**
	 * 
	 * 获得int类型的参数
	 * @param paramName
	 * @param paramMap
	 * @return
	 * @throws TemplateModelException 
	 */
	public static Integer getInt(String paramName, Map<String, TemplateModel> paramMap) throws TemplateModelException{
		TemplateModel model = paramMap.get(paramName);
		if(model==null){
			return null;
		}
		if(model instanceof TemplateScalarModel){
			String str = ((TemplateScalarModel)model).getAsString();
			try {
				return Integer.valueOf(str);
			} catch (NumberFormatException e) {
				throw new TemplateModelException(paramName);
			}
		}else if(model instanceof TemplateNumberModel){
			return ((TemplateNumberModel)model).getAsNumber().intValue();
		}else{
			throw new TemplateModelException(paramName);
		}
	}
}
```

​	然后再前面的实际代码中加上：

```java
paramMap.put("content", new ContentDirective());  
```

这样子基本上可以使用，freemarker完成自定义标签了，解决一写简单的业务逻辑， 但是在实际的项目中不可能这样子去做，因为还没有和spring进行集成使用，每次都需要在解析的时候把解析类的实例放进去。
