---
title: Spring学习笔记
tags: Spring
category: Spring
abbrlink: 41659
date: 2017-12-19 13:34:36
---
![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0023.jpg)
 
Spring是一个开放源代码的设计层面框架，他解决的是业务逻辑层和其他各层的松耦合问题，因此它将面向接口的编程思想贯穿整个系统应用。
<!--more-->
## spring只是一个框架

想跟着 spring in action 4 系统的研究下spring，结果发现忘了怎么建一个spring项目。

关键是，不知道该建一个什么项目，Java项目？Maven项目（Java项目？Web项目）？

一直以来都是直拿以前的项目配置修修改改，结果居然忘了spring的本质是什么，我是说，居然把spring当成web专属了。

颇有些提笔忘字的意思。

spring只是一个框架，第三方jar包，作用就是IoC、DI、AOP。

所以，spring与web没有必然关系，可以用在任何需要的项目中 --- 它就一中介和监管机构！！！

核心：加载配置文件applicationContext.xml，生成一个ApplicationContext对象，继而就可以提供被其代理的对象了！！！

本质就是这么简单。

spring提供xml方式和注解方式配置bean。（可以认为bean就是我们需要spring创建的对象）

　　前者是在xml中配置bean，<bean id='' class='' ..../>。

　　后者只需要在bean对应的类上@Component （或 @Repository ）即可。

需要注意的是，spring会根据配置或者注解生成对象，从而注入到需要的地方。

所以需要指定怎么生成对象，初始化参数是什么。

默认情况下，使用无参构造方法创建对象。

spring与web的结合：

　　在web.xml中启动spring即可！！！

spring与jdbc的结合：

　　在spring中配置连接所需信息，提供DataSource即可（也行还有事务）。
　　
## Spring事务：调用同一个类中的方法
    
    问题：

如果同一个类中有方法：methodA(); methodB()。methodA()没有开启事务，methodB()开启了事务

且methodA()会调用methodB()。

那么，methodA()调用methodB()时，不会开启事务！！！

即：同一个类中，无事务的方法调用有事务的方法，结果就是没有事务！！！

原因：[点这里](http://blog.csdn.net/aya19880214/article/details/50640596)

解决办法：要么声明要事务，要么分开成两个类，要么直接在方法里使用编程式事务。

建议直接分成两个类。

## Spring配置相关

Spring容器中bean的id或name，都可以有多个，且第一个为标识符（Qualifier），其余皆为别名（Alias）。所以都可以通过applicationContext.getBean("id or name", Type.class)获取。

如果同时存在id和name，则name为别名！！！

Spring容器目前能对各种基本类型把配置的String参数转换为需要的类型。

注：Spring类型转换系统对于boolean类型进行了容错处理，除了可以使用“true/false”标准的Java值进行注入，还能使用“yes/no”、“on/off”、“1/0”来代表“真/假”，所以大家在学习或工作中遇到这种类似问题不要觉得是人家配置错了，而是Spring容错做的非常好。

```java
// 测试类
public class BooleanTestBean {
    private boolean success;
    public void setSuccess(boolean success) {
        this.success = success;
    }
    public boolean isSuccess() {
        return success;
    }
}
```

```xml
// 配置文件（chapter3/booleanInject.xml）片段：
<!-- boolean参数值可以用on/off -->
<bean id="bean2" class="cn.javass.spring.chapter3.bean.BooleanTestBean">
    <property name="success" value="on"/>
</bean>
<!-- boolean参数值可以用yes/no -->
<bean id="bean3" class="cn.javass.spring.chapter3.bean.BooleanTestBean">
    <property name="success" value="yes"/>
</bean>
<!-- boolean参数值可以用1/0 -->
<bean id="bean4" class="cn.javass.spring.chapter3.bean.BooleanTestBean">
    <property name="success" value="1"/>
</bean>

```
Spring通过<value>标签或value属性注入常量值，所有注入的数据都是字符串，那如何注入null值呢？

通过“null”值吗？当然不是因为如果注入“null”则认为是字符串。Spring通过<null/>标签注入null值。

即可以采用如下配置方式：

```xml
<bean class="...">
    <property name="message"><null></property>
    ...
</bean>

```
使用<context:annotation-config/>标签来开启注解形式的依赖注入。

使用<context:component-scan/>标签来表示需要要自动注册Bean定义，而通过base-package属性指定扫描的类路径位置。

注意，<context:component-scan/>默认开启了annotation-config。

使用<aop:aspectj-autoproxy/>标签开启Spring对@AspectJ风格切面的支持。

 @AspectJ风格的切面可以通过@Compenent注解标识其为Spring管理Bean，而@Aspect注解不能被Spring自动识别并注册为Bean，必须通过@Component注解来完成。
 
 
```java
package cn.javass.spring.chapter12.aop;
//省略import
@Component
@Aspect
public class TestAspect {
    @Pointcut(value="execution(* *(..))")
    private void pointcut() {}
    @Before(value="pointcut()")
    public void before() {
        System.out.println("=======before");
    }
}

```
## Spring 父子容器

必须要说的是，父子容器是通过设置形成的关系。

容器实现了 ConfigurableApplicationContext 或 ConfigurableBeanFactory 接口，这两个接口中分别有setParent 及setParentBeanFactory 方法，可以将指定容器设置为当前容器的父容器。

首先，默认情况下，Spring + SpringMVC 框架的web项目，会先创建 WebApplicationContext（应用上下文环境，就是IoC容器啦），并以WebApplicationContext.ROOTWEBAPPLICATIONCONTEXTATTRIBUTE 为Key，将其存储到ServletContext 中 ---这就是根容器。

然后，创建DispatcherServlet ，注意，这是个Servlet，而Servlet也是有ServletContext 的，而且，它也会创建自己的IoC上下文（IoC容器）。这个IoC容器会去ServletContext 中查找根容器，并将其设为自己的父容器！！！---小疑问，这个ServletContext 和 IoC容器 是否一个？？？待验证。。

简单的可以推知：子容器可以访问父容器中的bean，父容器则无法访问子容器中的内容！！！

参考：

[spring的启动过程](http://blog.csdn.net/caomiao2006/article/details/51290494)

[Spring中父子容器的实现实例](http://blog.csdn.net/fenglibing/article/details/8597789) 

## Spring bean的初始化及销毁
Spring bean的几个属性：scope、init-method、destroy-method、depends-on等。

- Scope

在Spring容器中是指其创建的Bean对象相对于其他Bean对象的请求可见范围。

scope分类：singleton, prototype, request, session, global session。

这里的singleton和设计模式里面的单例模式不一样，标记为singleton的bean是由容器来保证这种类型的bean在同一个容器内只存在一个共享实例，而单例模式则是保证在同一个Classloader中只存在一个这种类型的实例。

- init-method

是指创建bean时调用的方法，注意，不是创建bean的方法。

- destroy-method

是指销毁bean时调用的方法，同样，不是销毁bean的方法。

注意：scope为prototype的bean，容器会将创建好的对象实例返回给请求方，之后，容器就不再拥有其引用，请求方需要自己负责当前对象后继生命周期的管理工作，包括该对象的销毁。

所以：scope为singleton的bean的destroy方法则是在容器关闭时执行，而scope为prototype的bean是不会执行destroy方法的。

- depends-on

用于指定bean初始化及销毁时的顺序。注意上面的结论。

```java
<bean id="helloApi" class="cn.javass.spring.chapter2.helloworld.HelloImpl"/>
<bean id="decorator" 
class="cn.javass.spring.chapter3.bean.HelloApiDecorator" 
depends-on="helloApi">
<property name="helloApi"><ref bean="helloApi"/></property>
</bean>
```
“decorator”指定了“depends-on”属性为“helloApi”，所以在“decorator”Bean初始化之前要先初始化“helloApi”，而在销毁“helloApi”之前先要销毁“decorator”，大家注意一下销毁顺序。

Spring 允许 Bean 在初始化完成后以及销毁前执行特定的操作。下面是常用的三种指定特定操作的方法：

1. 通过实现 InitializingBean / DisposableBean 接口；
2. 通过<bean> 元素的 init-method / destroy-method属性；
3. 通过@PostConstruct或@PreDestroy注解。

Bean在实例化的过程中：Constructor > @PostConstruct >InitializingBean > init-method

Bean在销毁的过程中：@PreDestroy > DisposableBean > destroy-method

参考：
Spring bean 的init-method和destroy-[method](http://outofmemory.cn/java/spring/spring-bean-init-method-and-destroy-method)
[Spring容器中的Bean几种初始化方法和销毁方法的先后顺序](http://blog.csdn.net/clerk0324/article/details/25374041) 
Spring [scope属性详解](http://blog.csdn.net/camper001/article/details/6121910)

可以和@Component一起使用的注解：

- @Lazy(true) -- 延迟初始化
- @DependsOn({"managedBean"}) --  初始化及销毁时的顺序
- @Qualifier -- 见 Spring 依赖注入（DI）的注解
- @Primary -- 当有多个候选时，被注解的bean作为首选项，否则异常。

## Resource接口，及资源
### Resource介绍

编码的时候，除了代码本身，我们还需要对外部的资源进行处理。例如：URL资源、URI资源、File资源、ClassPath相关资源、服务器相关资源（VFS等）等等。

而这些资源的处理是类似而繁琐的，如：打开资源、读取资源、关闭资源。

所以Spring提供了一个专门的接口Resource 用于统一这些底层资源的访问。

就是说，Spring的Resource接口代表底层外部资源，提供了对底层外部资源的一致性访问接口。

源码如下：

```java
public interface InputStreamSource {
    InputStream getInputStream() throws IOException;
}
public interface Resource extends InputStreamSource {
    boolean exists();
    boolean isReadable();
    boolean isOpen();
    URL getURL() throws IOException;
    URI getURI() throws IOException;
    File getFile() throws IOException;
    long contentLength() throws IOException;
    long lastModified() throws IOException;
    Resource createRelative(String relativePath) throws IOException;
    String getFilename();
    String getDescription();
}
```
Resource接口提供了足够的抽象，足够满足我们日常使用。而且提供了很多内置Resource实现：ByteArrayResource、InputStreamResource 、FileSystemResource 、UrlResource 、ClassPathResource、ServletContextResource、VfsResource等。

#### ByteArrayResource
代表byte[]数组资源，对于 getInputStream() 操作将返回一个ByteArrayInputStream。ByteArrayResource可多次读取数组资源，即 isOpen() 永远返回false。

#### InputStreamResource
代表java.io.InputStream字节流，对于getInputStream() 操作将直接返回该字节流，因此只能读取一次该字节流，即 isOpen() 永远返回true。
#### FileSystemResource
代表java.io.File资源，对于 getInputStream() 操作将返回底层文件的字节流，isOpen() 将永远返回false，从而表示可多次读取底层文件的字节流。
#### ClassPathResource
代表classpath路径的资源，将使用ClassLoader进行加载资源。classpath 资源存在于类路径中的文件系统中或jar包里，且 isOpen() 永远返回false，表示可多次读取资源。
#### ClassPathResource
加载资源替代了Class类和ClassLoader类的getResource(String name)和getResourceAsStream(String name)两个加载类路径资源方法，提供一致的访问方式。

###### ClassPathResource提供了三个构造器：
1. public ClassPathResource(String path)：使用默认的ClassLoader加载“path”类路径资源；

2. public ClassPathResource(String path, ClassLoader classLoader)：使用指定的ClassLoader加载“path”类路径资源；
　　-- 比如当前类路径是“cn.javass.spring.chapter4.ResourceTest”，而需要加载的资源路径是“cn/javass/spring/chapter4/test1.properties”，则将加载的资源在“cn/javass/spring/chapter4/test1.properties”；

3. public ClassPathResource(String path, Class<?> clazz)：使用指定的类加载“path”类路径资源，将加载相对于当前类的路径的资源；
　　-- 比如当前类路径是“cn.javass.spring.chapter4.ResourceTest”，而需要加载的资源路径是“cn/javass/spring/chapter4/test1.properties”，则将加载的资源在“cn/javass/spring/chapter4/cn/javass/spring/chapter4/test1.properties”；
　　-- 而如果需要 加载的资源路径为“test1.properties”，将加载的资源为“cn/javass/spring/chapter4/test1.properties”。
　　-- 需要注意一定：资源查找顺序是先查找当前类路径的资源，再去查找jar包，而且，只要找到就会返回。
　　-- 如果是在jar包中的资源，需要使用getURL()，而不是getFile()，因为资源不是存在于File系统，而是存在于jar包中！！！

#### UrlResource
代表URL资源，用于简化URL资源访问。“isOpen”永远返回false，表示可多次读取资源。

###### UrlResource一般支持如下资源访问：
- http：通过标准的http协议访问web资源，如new UrlResource(“http://地址”)；
- ftp：通过ftp协议访问资源，如new UrlResource(“ftp://地址”)；
- file：通过file协议访问本地文件系统资源，如new UrlResource(“file:d:/test.txt”)；

#### ServletContextResource
代表web应用资源，用于简化servlet容器的ServletContext接口的getResource操作和getResourceAsStream操作；

#### VfsResource
代表Jboss 虚拟文件系统资源。

## Resource访问

ResourceLoader接口用于返回Resource对象；其实现可以看作是一个生产Resource的工厂类。

```java
public interface ResourceLoader {
    Resource getResource(String location);
    ClassLoader getClassLoader();
}
```
getResource接口用于根据提供的location参数返回相应的Resource对象；而getClassLoader则返回加载这些Resource的ClassLoader。

Spring提供了一个适用于所有环境的DefaultResourceLoader实现，可以返回ClassPathResource、UrlResource；

还提供一个用于web环境的ServletContextResourceLoader，它继承了DefaultResourceLoader的所有功能，又额外提供了获取ServletContextResource的支持。

ResourceLoader在进行加载资源时需要使用前缀来指定需要加载：“classpath:path”表示返回ClasspathResource，“http://path”和“file:path”表示返回UrlResource资源；如果不加前缀则需要根据当前上下文来决定；另外，DefaultResourceLoader默认实现可以加载classpath资源。

目前所有的ApplicationContext都实现了ResourceLoader，因此可以使用其来加载资源。

- ClassPathXmlApplicationContext：不指定前缀将返回默认的ClassPathResource资源，否则将根据前缀来加载资源；
- FileSystemXmlApplicationContext：不指定前缀将返回FileSystemResource，否则将根据前缀来加载资源；
- WebApplicationContext：不指定前缀将返回ServletContextResource，否则将根据前缀来加载资源；
- 其他：不指定前缀根据当前上下文返回Resource实现，否则将根据前缀来加载资源。

### ResourceLoader

ResourceLoaderAware是一个标记接口，用于通过ApplicationContext注入ResourceLoader。

### 注入Resource

通过注入来获取ResourceLoader，再来访问资源，很麻烦！！！

Spring提供了ResourceEditor（这是一个PropertyEditor），用于在注入的字符串和Resource之间进行转换。

因此可以使用注入方式注入Resource。

```java
package cn.javass.spring.chapter4.bean;
import org.springframework.core.io.Resource;
public class ResourceBean3 {
    private Resource resource;
    public Resource getResource() {
        return resource;
    }
    public void setResource(Resource resource) {
        this.resource = resource;
    }
}

```

```xml
<bean id="resourceBean1" class="cn.javass.spring.chapter4.bean.ResourceBean3">
   <property name="resource" value="cn/javass/spring/chapter4/test1.properties"/>
</bean>
<bean id="resourceBean2" class="cn.javass.spring.chapter4.bean.ResourceBean3">
　　<property name="resource" value="classpath:cn/javass/spring/chapter4/test1.properties"/>
</bean>

```
注意此处“resourceBean1”注入的路径没有前缀表示根据使用的ApplicationContext实现进行选择Resource实现。

### Resource通配符路径

Spring提供了一种更强大的Ant模式通配符匹配，能匹配一批资源。

```
Ant路径通配符支持“？”、“*”、“**”，注意通配符匹配不包括目录分隔符“/”：
    “?”：匹配一个字符，如“config?.xml”将匹配“config1.xml”；
    “*”：匹配零个或多个字符串，如“cn/*/config.xml”将匹配“cn/javass/config.xml”，但不匹配匹配“cn/config.xml”；而“cn/config-*.xml”将匹配“cn/config-dao.xml”；
    “**”：匹配路径中的零个或多个目录，如“cn/**/config.xml”将匹配“cn /config.xml”，也匹配“cn/javass/spring/config.xml”；而“cn/javass/config-**.xml”将匹配“cn/javass/config-dao.xml”，即把“**”当做两个“*”处理。
```

### 小结：

- ① Spring通过Resource接口统一访问外部资源，并提供了一堆实现类，可以分别访问File、classpath、URL、URI等等资源。

- ② Spring通过ResourceLoader接口获取Resource。

- ③ ApplicationContext都实现了ResourceLoader接口，所以可以直接加载资源！

- ④ 实现ResourceLoaderAware接口，可以注入ResourceLoader。

- ⑤ 注入Resource。

- ⑥ Resource通配符路径。

## Spring 依赖注入（DI）的注解

Spring中想要使用注解进行依赖注入，需要进行如下配置：

```xml
<beans xmlns="http://www.springframework.org/schema/beans" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="
       http://www.springframework.org/schema/beans 
       http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context-3.0.xsd">
    <context:annotation-config/>
</beans>
```

### Spring自带依赖注入的注解

@Required，该注解必须用是setter方法上面，目的是强制要求提供setter所需数据，否则报错。

例如，BeanA中的字段field，有一个setField( T field)方法。当在该方法上使用了@Required之后，在XML中创建BeanA时就必须给出设置field所需的数据。

如下所示：

```java
package o1.bean;

import org.springframework.beans.factory.annotation.Required;

public class BeanA {
    private String message;

    public String getMessage(){
        return message;
    }

    @Required //只能放在setter上,在XML配置BeanA时必须指定setter注入，否则在Spring容器启动时将抛出异常
    public void setMessage(String message){
        this.message = message;
    }
}
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-3.0.xsd">

    <!--开启注解支持-->
    <context:annotation-config/>


    <bean class="o1.bean.BeanA">
        <!--因为有了@Required，所以这里必须提供，否则报错-->
        <property name="message" ref="message"/>
    </bean>

    <bean name="message" class="java.lang.String">
        <constructor-arg index="0" value="hello world"/>
    </bean>

</beans>

```

```java
package o1;

import o1.bean.BeanA;
import org.junit.Before;
import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class A {
    private ApplicationContext applicationContext;

    @Before
    public void setUp(){
        applicationContext=new ClassPathXmlApplicationContext("classpath:applicationContext.xml");
    }

    @Test
    public void run1(){
        BeanA bean = applicationContext.getBean(BeanA.class);
        System.out.println(bean.getMessage());
    }

}

```
### @Autowired(required=true)

自动注入，required=true的作用与@Required相同。

可用于构造器、字段、方法。

默认根据参数类型自动装配，但必须只能有一个候选项（required=false则可以允许0个候选项）。

 

### @Value(value="SpEL")

可用于字段、方法（@Autowired method）。

如：

```java
@Value(value="#{message}")
private String message;
@Autowired
public void initMessage(@Value(value = "#{message}") String message) {
    this.message = message;
}
```

### @Qualifier(value="限定标识符")

可用于方法、字段、参数。

配合@Autowired使用，可用于多个候选项的情况。

 实例如下：
 
```java
package o1.bean;


import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;

import javax.sql.DataSource;

public class BeanB {
    private DataSource dataSourceA;
    private DataSource dataSourceB;


    public DataSource getDataSourceA(){
        return dataSourceA;
    }

    @Autowired
    public void initDataSource(@Qualifier( "mysqlDataSource2" ) DataSource dataSource){ //
        this.dataSourceA =dataSource;
    }

    public DataSource getDataSourceB(){
        return dataSourceB;
    }

    @Autowired
    public void setDataSourceB(@Qualifier( "mysqlDataSource1" ) DataSource dataSourceB){
        this.dataSourceB = dataSourceB;
    }
}

```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-3.0.xsd">

    <!--开启注解支持-->
    <context:annotation-config/>
    <context:property-placeholder location="db.properties"/>

    <bean class="o1.bean.BeanB"/>

    <bean class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <qualifier type="org.springframework.beans.factory.annotation.Qualifier" value="mysqlDataSource1"/> <!--type可以省略-->
        <property name="driverClassName" value="${driverClass}"/>
        <property name="url" value="${jdbcUrl_1}"/>
        <property name="username" value="${user}"/>
        <property name="password" value="${password}"/>
    </bean>

    <bean class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <qualifier type="org.springframework.beans.factory.annotation.Qualifier" value="mysqlDataSource2"/>
        <property name="driverClassName" value="${driverClass}"/>
        <property name="url" value="${jdbcUrl_2}"/>
        <property name="username" value="${user}"/>
        <property name="password" value="${password}"/>
    </bean>

</beans>

```

```java
package o1;

import o1.bean.BeanB;
import org.junit.Before;
import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class B {
    private ApplicationContext applicationContext;

    @Before
    public void setUp(){
        applicationContext=new ClassPathXmlApplicationContext("classpath:applicationContextB.xml");
    }

    @Test
    public void run1(){
        BeanB bean = applicationContext.getBean(BeanB.class);
        System.out.println(bean.getDataSourceA());
        System.out.println(bean.getDataSourceB());

    }
}
```
db.properties

```properties
driverClass=com.mysql.jdbc.Driver
jdbcUrl_1=jdbc\:mysql\://localhost\:3306/testdb1?useUnicode=true&amp;characterEncoding=UTF8
jdbcUrl_2=jdbc\:mysql\://localhost\:3306/testdb2?useUnicode=true&amp;characterEncoding=UTF8
user=root
password=root
```

如果有几个常用的DataSource，那么可以自定义注解来使用，而不必每次都是@Qualifier("xx")。如下：

自定义@MySQL和@Oracle


```java
package o1.customize_qualifier;

import org.springframework.beans.factory.annotation.Qualifier;

import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Target( {ElementType.FIELD, ElementType.PARAMETER, ElementType.TYPE} )
@Retention( RetentionPolicy.RUNTIME )
@Qualifier
public @interface MySQL {
}
```

```java
package o1.customize_qualifier;

import org.springframework.beans.factory.annotation.Qualifier;

import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Target( {ElementType.FIELD, ElementType.PARAMETER, ElementType.TYPE} )
@Retention( RetentionPolicy.RUNTIME )
@Qualifier
public @interface Oracle {
}
```
使用qualifier来限定需要注入的bean：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-3.0.xsd">

    <!--开启注解支持-->
    <context:annotation-config/>

    <bean class="o1.bean.BeanC"/>

    <bean id="dataSource1" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <qualifier type="o1.customize_qualifier.MySQL" value="mysqlDataSource"/><!--value可以省略！-->
    </bean>

    <bean id="dataSource2" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <qualifier type="o1.customize_qualifier.Oracle" value="oracleDataSource"/>
    </bean>

</beans>
```
要被注入的bean：

```java
package o1.bean;

import o1.customize_qualifier.MySQL;
import o1.customize_qualifier.Oracle;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;

import javax.sql.DataSource;

public class BeanC {
    private DataSource dataSourceA;
    private DataSource dataSourceB;

    @Autowired
    public void initDataSource(@MySQL DataSource dataSourceA, @Oracle DataSource dataSourceB){
        this.dataSourceA = dataSourceA;
        this.dataSourceB = dataSourceB;
    }

    public DataSource getDataSourceA(){
        return dataSourceA;
    }

    public DataSource getDataSourceB(){
        return dataSourceB;
    }

}
```
测试：

```java
package o1;

import o1.bean.BeanB;
import o1.bean.BeanC;
import org.junit.Assert;
import org.junit.Before;
import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

import javax.sql.DataSource;

public class C {
    private ApplicationContext applicationContext;

    @Before
    public void setUp(){
        applicationContext=new ClassPathXmlApplicationContext("classpath:applicationContextC.xml");
    }

    @Test
    public void run1(){
        BeanC bean = applicationContext.getBean(BeanC.class);
        DataSource dataSource1 = applicationContext.getBean("dataSource1", DataSource.class);
        DataSource dataSource2 = applicationContext.getBean("dataSource2", DataSource.class);

        Assert.assertEquals(dataSource1, bean.getDataSourceA());
        Assert.assertEquals(dataSource2, bean.getDataSourceB());
    }
}
```

使用<context:annotation-config/>标签来开启注解形式的依赖注入。

使用<context:component-scan/>标签来表示需要要自动注册Bean定义，而通过base-package属性指定扫描的类路径位置。

注意，<context:component-scan/>默认开启了annotation-config。

使用<aop:aspectj-autoproxy/>标签开启Spring对@AspectJ风格切面的支持。

 @AspectJ风格的切面可以通过@Compenent注解标识其为Spring管理Bean，而@Aspect注解不能被Spring自动识别并注册为Bean，必须通过@Component注解来完成。

## Spring的p标签
看Spring in action的时候看过p标签，可惜这东西不用就忘。

p标签是为了简化setter的注入而引入的。

用法：

```
p:属性 = "{值}"
p:属性-ref = "{引用bean的id}"
```
 
示例Bean：


```java
package o3.bean;

import java.util.Date;

public class Person {
    private String name;
    private int age;
    private Date date;

    public String getName(){
        return name;
    }

    public void setName(String name){
        this.name = name;
    }

    public int getAge(){
        return age;
    }

    public void setAge(int age){
        this.age = age;
    }

    public Date getDate(){
        return date;
    }

    public void setDate(Date date){
        this.date = date;
    }

    @Override public String toString(){
        return "Person{" +
               "name='" + name + '\'' +
               ", age=" + age +
               ", date=" + date +
               '}';
    }
}
```

XML配置：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:p="http://www.springframework.org/schema/p"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-3.0.xsd">

    <context:component-scan base-package="o3.bean"/>

    <bean id="person" class="o3.bean.Person">
        <property name="name" value="Kute"/>
        <property name="age" value="22"/>
        <property name="date" ref="date"/>
    </bean>
    <bean id="date" class="java.util.Date" autowire="constructor"/>
    <bean id="person2" class="o3.bean.Person" p:name="Bill" p:age="23" p:date-ref="date" />
</beans>
```

测试代码：


```java
package o3;

import o3.bean.Person;
import org.junit.Before;
import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class P {
    private ApplicationContext ac;
    @Before
    public void setUp(){
        ac=new ClassPathXmlApplicationContext("classpath:applicationContextPerson.xml");
    }

    @Test
    public void run1(){
        Person person = ac.getBean("person", Person.class);
        Person person2 = ac.getBean("person2", Person.class);
        System.out.println(person);
        System.out.println(person2);
    }

}
```

测试结果：

```
Person{name='Kute', age=22, date=Fri May 27 10:13:27 CST 2016}
Person{name='Bill', age=23, date=Fri May 27 10:13:27 CST 2016}
```

## Spring RestTemplate 小结
关于RestTemplate

首先，你可以把它理解为一个发起请求并接收响应的工具类（功能类似浏览器）。

其次，它其实是一个壳，具体还是通过调用别的接口来实现（如jdk自带的连接，或者HttpClient之类的，需要设置）。

### 官方介绍

Spring's central class for synchronous client-side HTTP access. 
It simplifies communication with HTTP servers, and enforces RESTful principles. 
It handles HTTP connections, leaving application code to provide URLs (with possible template variables) and extract results.

Note: by default the RestTemplate relies on standard JDK facilities to establish HTTP connections. 
You can switch to use a different HTTP library such as Apache HttpComponents, Netty, and OkHttp 
through the HttpAccessor.setRequestFactory(org.springframework.http.client.ClientHttpRequestFactory) property. 

### 使用

这个其实没什么说的，不外乎创建template，设置底层连接，然后增删改查。

请参考Spring REST和 RestTemplate实践 。

唯一需要注意的就是默认的jdk连接是不支持delete带请求体--解决办法见下面链接。

### 其他

暂时不清楚使用RestTemplate和直接使用HttpClient有什么区别，感觉HttpClient足够用了--囧。

--突然想到的，RestTemplate可以配合MessageConverter等使用！！！也就是配合SpringMVC使用，而HttpClient则需要手动转换！

杂：

```
httpHeaders.add("Accept-Language", "zh-CN,zh;q=0.8,en;q=0.6");
```

q是权重系数，范围 0 =< q <= 1，q 值越大，请求越倾向于获得其“;”之前的类型表示的内容，若没有指定 q 值，则默认为1，若被赋值为0，则用于提醒服务器哪些是浏览器不接受的内容类型。
