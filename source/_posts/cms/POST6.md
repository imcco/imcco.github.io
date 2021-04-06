---
title: FreeMarker自定义TemplateDirectiveModel(二)
category:
  - Java
copyright: true
tags: freemarker
abbrlink: 37707
date: 2017-09-07 22:06:37
---

![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0032.jpg)

FreeMarker是一个用Java语言编写的模板引擎，它基于模板来生成文本输出。FreeMarker与Web容器无关，即在Web运行时，它并不知道Servlet或HTTP。它不仅可以用作表现层的实现技术，而且还可以用于生成XML，JSP或Java 等。
<!--more-->

​	摘要: 采用的是freemarker版本 2.3.21 采用 BeansWrapperBuilder替代DEFAULT_WRAPPER 的方式。

* 自定义标签需要实现TemplateDirectiveModel这个接口中的execute方法  实例代码如下

```java
public class UserListDirective implements TemplateDirectiveModel{
	
	@Autowired
	private UserDAO  userDao;
	@Override
	public void execute(Environment env, Map params, TemplateModel[] loopVars,
			TemplateDirectiveBody body) 
                      throws TemplateException, IOException {
		String name = params.get("name").toString();
		List<User> userlist = userDao.findByProperty("name", name);

		env.setVariable("userList", getBeansWrapper().wrap(userlist));
		body.render(env.getOut());
	}
	
	public static BeansWrapper getBeansWrapper(){
		BeansWrapper beansWrapper = 
                         new BeansWrapperBuilder(Configuration.VERSION_2_3_21).build();
		return beansWrapper;
	}
}
```

* 配置 UserListDirective 到spring  bean xml中

```xml
<bean id="userListDirective" class="com.action.directive.UserListDirective"></bean>
```

* 将spring bean 设置到freemarkerConfig全局变量中去。 

```xml
<bean id="freemarkerConfig2"
        class="org.springframework.web.servlet.view.freemarker.FreeMarkerConfigurer">
        <property name="templateLoaderPath" value="/" />
        <property name="freemarkerVariables">
            <map >
                <entry key="userListDirective" value="userListTag" />
            </map>
        </property>
        <property name="freemarkerSettings">
            <props>
                <prop key="template_update_delay">0</prop>
                <prop key="defaultEncoding">UTF-8</prop>
                <prop key="url_escaping_charset">UTF-8</prop>
                <prop key="locale">zh_CN</prop>
                <prop key="boolean_format">true,false</prop>
                <prop key="datetime_format">yyyy-MM-dd HH:mm:ss</prop>
                <prop key="date_format">yyyy-MM-dd</prop>
                <prop key="time_format">HH:mm:ss</prop>
                <prop key="number_format">0.######</prop>
                <prop key="whitespace_stripping">true</prop>
            </props>
        </property>
    </bean>
```

* ftl文件中的访问方式

```html
<@userListTag name="zhangsan">
    <#if userList?? && userList?size gt 0>
        <#list userList as user>
            <a href="">${user.name}</a>
        </#list>
    </#if> 
</@userListTag>
```

* freemarker 遍历 map

第一种方式（2.3.21版本之前好像可以用）

```html
<#list testMap?keys as testKey>  
    < option value="${testKey}" >  
        ${testMap.get(testKey)}  
    </option>  
</#list>
```

```html
<#list testMap.keySet() as testKey>  
    < option value="${testKey}" >  
        ${testMap.get(testKey)}  
    </option>  
</#list>
```
* 附摘：

在采用FreeMarker做前台视图模板的情况下，我们可以通过<#include>标签和自定义宏来解决很多重复性工作。

一个简单的FreeMarker宏：

```html
<#macro sayHello name="">
    hello ${name}
</#macro>
```

然后通过如下的形式调用：

```html
<@sayHello name="shannon" />  
```

​	不过这种在模板页中定义的宏能力有限。【1】假设，我们很多页面都要输出一个热门排行框，而排行数据需要从controller层动态获取，我们可以用这种宏来完成所有的展示工作，但前提是相应的controller和接口中层需要预先将这些排行数据放到model中去，因此对于后端来说这也是一个重复性的工作。那么有没有一种方式可以让后端也脱离这种重复工作呢？答案是肯定的，这也是写这篇博客的目的。

​	在一个偶然的机会发现jeecms项目中用到了这种方式，于是借鉴了一番。

​	FreeMarker不仅可以在前端的模板页中定义宏，还可以通过扩展其接口在后端实现宏，这有什么好处呢？这种方式就好比让你的模板页具备了从前端再次回到后端的能力。这样我们就能很好的解决【1】处的假设，我们无需在各个controller的各个接口中去重复的向model中添加所需的排行数据，而是当FreeMarker渲染模板页时遇到相应的宏它可以回到后端去调用相应的方法取到所需的数据。例子如下：

```java
import freemarker.core.Environment;
import freemarker.template.ObjectWrapper;
import freemarker.template.TemplateDirectiveModel;

/**
 * FreeMarker自定义宏
 * 获取App下载排行列表
 * 参数包括 length（列表长度） mtypeCode(主类型代码) typeCode（小类型代码） rankMode（排行模式1、2、3）
 * @author shannon
 *
 */
public class FMAppRankDirective implements TemplateDirectiveModel {

    @Resource(name = "appRankService")
    private AppRankService appRankService;
    
    
    @SuppressWarnings("unchecked")
    @Override
    public void execute(Environment env, Map params, TemplateModel[] loopVars,
            TemplateDirectiveBody body) throws TemplateException, IOException {
        //DirectiveUtils是借用jeecms项目中的工具类，主要是因为它集成了一些异常处理功能，
        //其实完全可以不用它，params是个Map，自己通过key取值就可以了，做一下空值判断
        Integer length = DirectiveUtils.getInt("length", params);
        Integer mtypeCode = DirectiveUtils.getInt("mtypeCode", params);
        Integer typeCode = DirectiveUtils.getInt("typeCode", params);
        Integer rankMode = DirectiveUtils.getInt("rankMode", params);
        ArrayList<App> rankList = appRankService.getRankList(length, mtypeCode, typeCode, rankMode);
        
        env.setVariable("appRankList", ObjectWrapper.DEFAULT_WRAPPER.wrap(rankList));
        if (body != null) {
            body.render(env.getOut());
        }
    }
}
```

​	通过实现FreeMarker的TemplateDirectiveModel就在后端实现了一个自定义的宏，这个宏的功能很简单，只是根据给定的参数将排行数据“appRankList”放到model中去，然后模板页中就可以使用这个变量了。

FreeMarker的配置参数中需要将这个宏加入进去。

```html
	<bean id="appRankDirective" class="com.shannon.example.rank.util.FMAppRankDirective" />
	<bean id="freemarkerConfigurer" class="org.springframework.web.servlet.view.freemarker.FreeMarkerConfigurer">
		……其他配置略……
		<property name="freemarkerVariables">
			<map>
				……其他配置略……
				<entry key="appRankDirective" value-ref="appRankDirective"/>
			</map>
		</property>
	</bean>
```

在模板页中使用：

```html
<#-- 应用下载排行框，title为该框的标题，length为排行列表长度，mtypeCode为主类型代码，typeCode为小类型代码，rankMode为排行方式 
1为总下载量，2为月下载量，3为昨日增长下载量
-->
<#macro appRankBox title="" length=10 mtypeCode=1 typeCode=-1 rankMode=1>
      <@appRankDirective length=length mtypeCode=mtypeCode typeCode=typeCode rankMode=rankMode />
        <h3 class="box-title">${title}</h3>
    <div class="box">
      <ul class="row-list">
        <#list appRankList as item>
        ……详细输出内容略……
        </#list>
        </ul>
    </div>
</#macro>
```

​	这里我在模板页中又定义了一个宏，负责内容及样式的输出，因为模板页中的宏比较直观，让后端的宏只负责拿数据。其他页面直接使用“appRankBox”就可以了，然后由它来调用后端的“appRankDirective”宏来拿数据。

这样，controller就从重复工作中脱身了。
