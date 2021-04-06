---
title: Spring Boot微服框架学习(二)
tags: SpringBoot
category: SpringBoot
abbrlink: 31996
date: 2017-12-20 02:34:36
---
![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0026.jpg)

## 简介
Spring Boot是由Pivotal团队提供的全新框架，其设计目的是用来简化新Spring应用的初始搭建以及开发过程。该框架使用了特定的方式来进行配置，从而使开发人员不再需要定义样板化的配置。通过这种方式，Spring Boot致力于在蓬勃发展的快速应用开发领域(rapid application development)成为领导者。
<!--more-->
# 监听器、过滤器和拦截器

## 监听器、过滤器和拦截器
### 监听器

监听器Listener，它是实现了javax.servlet.XXXListener接口的服务器端程序，它也是随web应用的启动而启动，只初始化一次，随web应用的停止而销毁。主要作用是：做一些初始化的内容添加工作、设置一些基本的内容、比如一些参数或者是一些固定的对象等等。

在javax.servlet.XXXListener接口中定义了2种方法：

1. void contextInitialized(ServletContextEvent sce) 监听器的初始化
2. void contextDestroyed(ServletContextEvent sce) 监听器销毁

### 过滤器
Servlet中的过滤器Filter是实现了javax.servlet.Filter接口的服务器端程序，主要的用途是过滤字符编码、做一些业务逻辑判断等。其工作原理是，只要你在web.xml文件配置好要拦截的客户端请求，它都会帮你拦截到请求，此时你就可以对请求或响应(Request、Response)统一设置编码，简化操作；同时还可以进行逻辑判断，如用户是否已经登录、有没有权限访问该页面等等工作，它是随你的web应用启动而启动的，只初始化一次，以后就可以拦截相关的请求，只有当你的web应用停止或重新部署的时候才能销毁。

在javax.servlet.Filter接口中定义了3个方法：

1. void init(FilterConfig filterConfig) 用于完成过滤器的初始化
2. void destroy() 用于过滤器销毁前，完成某些资源的回收
3. void doFilter(ServletRequest request, ServletResponse response,FilterChain chain) 实现过滤功能，该方法对每个请求增加额外的处理

### 拦截器
拦截器主要是用在插件上，扩展件上比如spring、struts2等有点类似面向切片的技术，它是基于java反射机制。定义拦截器： 实现HandlerInterceptor 接口，或者继承实现了HandlerInterceptor 接口的类（常用） 。

## 监听器、过滤器和拦截器的关系及区别
- 过滤器（Filter）：当你有一堆东西的时候，你只希望选择符合你要求的某一些东西。定义这些要求的工具，就是过滤器。 对请求起到过滤的作用，它在监听器之后，作用在servlet之前，对请求进行过滤。

- 拦截器（Interceptor）：在一个流程正在进行的时候，你希望干预它的进展，甚至终止它进行，这是拦截器做的事情。

- 监听器（Listener）：当一个事件发生的时候，你希望获得这个事件发生的详细信息，而并不想干预这个事件本身的进程，这就要用到监听器。对项目起到监听的作用，它能感知到包括request(请求域)，session(会话域)和applicaiton(应用程序)的初始化和属性的变化 。对请求和返回进行拦截，它作用在servlet的内部 。

它们之间的关系，可以用一张图来表示：

![image](http://ovi3ob9p4.bkt.clouddn.com/springboot/springboot006.jpg)

#### 使用原则

对整个流程清楚之后，然后就是各自的使用，在使用之前应该有一个使用规则，为什么这个说，因为有些功能比如判断用户是否登录，既可以用过滤器，也可以用拦截器，用哪一个才是合理的呢？那么如果有一个原则，使用起来就会更加合理。实际上这个原则是有的：把整个项目的流程比作一条河，那么监听器的作用就是能够听到河流里的所有声音，过滤器就是能够过滤出其中的鱼，而拦截器则是拦截其中的部分鱼，并且作标记。所以当需要监听到项目中的一些信息，并且不需要对流程做更改时，用监听器；当需要过滤掉其中的部分信息，只留一部分时，就用过滤器；当需要对其流程进行更改，做相关的记录时用拦截器。

## 监听器实现过程
### 代码注册方式

```java
import javax.servlet.ServletContextEvent;
import javax.servlet.ServletContextListener;

public class IndexListener implements ServletContextListener{
    @Override
    public void contextDestroyed(ServletContextEvent arg0) {
        System.out.println("IndexListener contextDestroyed method");
    }
  
    @Override
    public void contextInitialized(ServletContextEvent arg0) {
        System.out.println("IndexListener contextInitialized method");
    }
}
@Bean
public ServletListenerRegistrationBean servletListenerRegistrationBean(){
    ServletListenerRegistrationBean servletListenerRegistrationBean = new ServletListenerRegistrationBean();
    servletListenerRegistrationBean.setListener(new IndexListener());
    return servletListenerRegistrationBean;
}
```

### 注解方式


```java
import javax.servlet.ServletContextEvent;
import javax.servlet.ServletContextListener;
import javax.servlet.annotation.WebListener;

@WebListener
public class IndexListener2 implements ServletContextListener{
    @Override
    public void contextDestroyed(ServletContextEvent arg0) {
        System.out.println("IndexListener2 contextDestroyed method");
    }

    @Override
    public void contextInitialized(ServletContextEvent arg0) {
        System.out.println("IndexListener2 contextInitialized method");
    }
}
@SpringBootApplication
@ServletComponentScan
public class SpringBootSimpleApplication {
    public static void main(String[] args) {
        SpringApplication.run(SpringBootSimpleApplication.class, args);
    }
}
```

## 过滤器实现过程
### 代码注册方式


```java
public class TestFilter implements Filter {

    @Override
    public void init(FilterConfig filterConfig) throws ServletException {

    }

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
            throws IOException, ServletException {
        if (......) {
            ......
            response.getWriter().write(writeValueAsString);          
        } else {
            chain.doFilter(request, response);
        }

    }

    @Override
    public void destroy() {

    }

}
@Bean
public FilterRegistrationBean  filterRegistrationBean() {
    FilterRegistrationBean registrationBean = new FilterRegistrationBean();

    TestFilter testFilter = new TestFilter();
    registrationBean.setFilter(testFilter);
    List<String> urlPatterns = new ArrayList<String>();
    urlPatterns.add("/service/extract/json/*");

    registrationBean.setUrlPatterns(urlPatterns);

    return registrationBean;
}
```

### 注解方式


```java
import javax.servlet.Filter;  
import javax.servlet.FilterChain;  
import javax.servlet.FilterConfig;  
import javax.servlet.ServletException;  
import javax.servlet.ServletRequest;  
import javax.servlet.ServletResponse;  
import javax.servlet.annotation.WebFilter;  

/** 
 * 实现javax.servlet.Filter,覆盖其三个方法 
 * @author Administrator 
 * 
 */  
@WebFilter(filterName="testFilter",urlPatterns="/*")  
public class TestFilter implements Filter{  
  
    @Override  
    public void destroy() {  
        System.out.println("TestFilter过滤器销毁");  
          
    }  
  
    @Override  
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)  
            throws IOException, ServletException {  
        System.out.println("TestFilter指定过滤器操作......");  
        //执行操作后必须doFilter  
        chain.doFilter(request, response);  
    }  
  
    @Override  
    public void init(FilterConfig arg0) throws ServletException {  
        System.out.println("TestFilter初始化......");  
    }  
  
}  
@SpringBootApplication
@ServletComponentScan
public class SpringBootSimpleApplication {
    public static void main(String[] args) {
        SpringApplication.run(SpringBootSimpleApplication.class, args);
    }
}
```

## 拦截器实现过程

1. 创建自己的拦截器实现HandlerInterceptor接口
2. 创建自己的拦截器链，继承WebMvcConfigurerAdapter类，重写addInterceptors方法。
3. 实例化自己的拦截器，并加入到拦截器链中。

```java
import javax.servlet.http.HttpServletRequest;  
import javax.servlet.http.HttpServletResponse;  
import org.springframework.web.servlet.HandlerInterceptor;  
import org.springframework.web.servlet.ModelAndView;  
  
public class CustomInterceptor implements HandlerInterceptor {  
  
    @Override  
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object object,  
            Exception exception) throws Exception {  
        //在整个请求结束之后被调用，也就是在DispatcherServlet 渲染了对应的视图之后执行（主要是用于进行资源清理工作）  
        System.out.println("3. 整个请求结束之后被调用......CustomInterceptor1......");  
    }  
  
    @Override  
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object object, ModelAndView view)  
            throws Exception {  
        // 请求处理之后进行调用，但是在视图被渲染之前  
        System.out.println("2. 请求处理之后进行调用，但是在视图被渲染之前......CustomInterceptor1......");  
    }  
  
    @Override  
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object object) throws Exception {  
        // 在请求处理之前进行调用  
        System.out.println("1. 在请求处理之前进行调用......CustomInterceptor1......");  
        // 只有返回true才会继续向下执行，返回false取消当前请求  
        return true;  
    }  
  
}
```


```java
import org.springframework.context.annotation.Configuration;  
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;  
import org.springframework.web.servlet.config.annotation.WebMvcConfigurerAdapter;  
  
import com.example.Interceptor.CustomInterceptor;  
import com.example.Interceptor.CustomInterceptor2;  
  
/** 
 * 继承WebMvcConfigurerAdapter，复写addInterceptors方法 
 * @author Administrator 
 * 
 */  
@Configuration  
public class WebAdapter extends WebMvcConfigurerAdapter{  
    /** 
     * 主要方法说明： 
     * addPathPatterns 用于添加拦截规则 
     * excludePathPatterns 用于排除拦截 
     */  
    @Override  
    public void addInterceptors(InterceptorRegistry registry) {  
        //众多的拦截器组成了一个拦截器链
        registry.addInterceptor(new CustomInterceptor()).addPathPatterns("/*");  

        super.addInterceptors(registry);  
    }  
}
```

# 默认日志logback配置

## 日志简介
Spring Boot使用Commons Logging记录所有内部日志，但是它将底层日志实现打开，为Java.Util.Logging，Log4J2和Logback提供默认配置。 在每个案例中，loggers都预先配置，以使用控制台输出，同时还提供可选的文件输出。

默认情况下，如果你使用“starters”，Logback将会被用于记录日志。还包括占用Logback路由，以确保使用Java Util Logging、Commons logging、Log4J或SLF4J的依赖库都能正常工作。

## 默认日志logback
### logback日志简介
SLF4J——Simple Logging Facade For Java，它是一个针对于各类Java日志框架的统一Facade抽象。Java日志框架众多——常用的有java.util.logging, log4j, logback，commons-logging, Spring框架使用的是Jakarta Commons Logging API (JCL)。而SLF4J定义了统一的日志抽象接口，而真正的日志实现则是在运行时决定的——它提供了各类日志框架的绑定。

Logback是log4j框架的作者开发的新一代日志框架，它效率更高、能够适应诸多的运行环境，同时天然支持SLF4J。

默认情况下，Spring Boot会用Logback来记录日志，并用INFO级别输出到控制台。在运行应用程序和其他例子时，可以看到很多INFO级别的日志。

![image](http://ovi3ob9p4.bkt.clouddn.com/springboot/springboot002.png)

从上图可以看到，日志输出内容元素具体如下：

- 时间日期：精确到毫秒
- 日志级别：ERROR, WARN, INFO, DEBUG or TRACE
- 进程ID
- 分隔符：--- 标识实际日志的开始
- 线程名：方括号括起来（可能会截断控制台输出）
- Logger名：通常使用源代码的类名
- 日志内容

用Logback来记录日志，需添加日志依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-logging</artifactId>
</dependency>
```
实际开发中我们不需要直接添加该依赖，你会发现spring-boot-starter其中包含了spring-boot-starter-logging，该依赖内容就是 Spring Boot 默认的日志框架 logback。

![image](http://ovi3ob9p4.bkt.clouddn.com/springboot/springboot007.jpg)

### application.properties配置中的日志相关属性
#### 控制台输出  

日志级别从低到高分为TRACE < DEBUG < INFO < WARN < ERROR < FATAL，如果设置为WARN，则低于WARN的信息都不会输出。Spring Boot中默认配置ERROR、WARN和INFO级别的日志输出到控制台。您还可以通过启动您的应用程序–debug标志来启用“调试”模式（开发的时候推荐开启）,以下两种方式皆可：

- 在运行命令后加入--debug标志，如：

```
$ java -jar springTest.jar --debug
```

- 在application.properties中配置debug=true

该属性置为true的时候，核心Loggers（包含嵌入式容器、hibernate、spring）会输出更多内容，启用debug模式并不会使用debug级别配置您的应用程序来记录所有信息。或者，您可以通过在应用程序中使用-trace标志(或在application.properties中配置trace=true)启动应用程序来启用“跟踪”模式。

#### 文件输出

默认情况下，Spring Boot将日志输出到控制台，不会写到日志文件。如果要编写除控制台输出之外的日志文件，则需在application.properties中设置logging.file或logging.path属性。

- logging.file，设置指定的日志文件，可以是绝对路径，也可以是相对路径。如：logging.file=my.log
- logging.path，设置指定的目录，会在该目录下创建spring.log文件，并写入日志内容，如：logging.path=/var/log

> 默认情况下，日志文件的大小达到10MB时会切分一次，产生新的日志文件，默认级别为：ERROR、WARN、INFO
> 日志系统在应用程序生命周期的早期被初始化，并且在通过@propertysource注解加载的属性文件中不会找到这样的日志属性。
> 日志属性与实际的日志基础结构无关。因此，特定的配置键(例如logback中的 logback.configurationFile )不是由spring boot管理的。

#### 彩色日志输出
      省略...     

#### 日志级别

所有支持日志记录的系统都可以在Spring环境中设置记录级别（例如在application.properties中）
格式为：’logging.level.* = LEVEL’

logging.level：日志级别控制前缀，*为包名或Logger名

LEVEL：选项TRACE, DEBUG, INFO, WARN, ERROR, FATAL, OFF 

root日志可以使用 logging.level.root 配置，application.properties实例：


```properties
logging.level.root=WARN
logging.level.org.springframework.web=DEBUG
logging.level.org.hibernate=ERROR
```
> 默认情况下，Spring boot对 Thymeleaf INFO 消息进行了重新映射，使它们在调试级别上被记录，这有助于减少标准日志输出中的噪声。有关如何在自己的配置中应用重新映射的详细信息，请参阅LevelRemappingAppender。

#### 自定义日志配置
可以通过在classpath中包含适当的库来激活各种日志系统，并通过在classpath根目录提供适当的配置进行定制。或者在适当的位置提供spring 环境属性logging.config。

还可以强制spring boot使用特定的日志系统通过使用 org.springframework.boot.logging.LoggingSystem 系统属性。 该值应该是一个 LoggingSystem 实现的完全限定类名。 您还可以使用none值完全禁用Spring boot的日志记录配置。

由于日志是在应用程序applicationContext上下文创建之前初始化的，所以不可能在Spring @configuration注解文件中控制来自@propertySource的日志记录。系统属性和常规的Spring boot外部配置文件已经工作得很好。

根据不同的日志系统，你可以按如下规则组织配置文件名，就能被正确加载：

- Logback：logback-spring.xml, logback-spring.groovy, logback.xml, logback.groovy
- Log4j2：log4j2-spring.xml, log4j2.xml
- JDK (Java Util Logging)：logging.properties

在可能的情况下，我们建议您使用-spring变体来进行日志配置( 如使用logback-spring.xml，而不是logback.xml )。如果使用标准配置logback.xml，Spring无法完全控制日志初始化。 Spring boot包含许多对Logback的扩展，可以帮助高级配置，您可以在您的logback-spring.xml文件中使用这些扩展。在标准配置logback.xml中不能使用这些扩展因为它太早被加载，当然如果一定要使用，需定义一个 logging.config 属性。

> 注：这些扩展不能与Logback的配置扫描一起使用。如果您尝试这样做，对配置文件进行更改将导致类似于以下日志记录之一的错误:

```log
ERROR in ch.qos.logback.core.joran.spi.Interpreter@4:71 - no applicable action for [springProperty], current ElementPath is [[configuration][springProperty]]
ERROR in ch.qos.logback.core.joran.spi.Interpreter@4:71 - no applicable action for [springProfile], current ElementPath is [[configuration][springProfile]]
```
## logback日志介绍与配置

### logback介绍

Logback是由log4j创始人设计的又一个开源日志组件。logback当前分成三个模块：logback-core,logback- classic和logback-access。logback-core是其它两个模块的基础模块。logback-classic是log4j的一个 改良版本。此外logback-classic完整实现SLF4J API使你可以很方便地更换成其它日志系统如log4j或JDK14 Logging。logback-access访问模块与Servlet容器集成提供通过Http来访问日志的功能。 Logback是要与SLF4J结合起来用两个组件的官方网站如下：

-       logback的官方网站： http://logback.qos.ch
 
-       SLF4J的官方网站：http://www.slf4j.org

### logback取代log4j的理由

###### Logback和log4j是非常相似的，如果你对log4j很熟悉，那对logback很快就会得心应手。下面列了logback相对于log4j的一些优点：

- 更快的实现  Logback的内核重写，在一些关键执行路径上性能提升10倍以上。而且logback不仅性能提升，初始化内存加载也更小。

- 非常充分的测试  Logback经过了几年，数不清小时的测试，Logback的测试完全不同级别的。在作者的观点，这是简单重要的原因选择logback而不是log4j。

- Logback-classic非常自然实现了SLF4j    因为logback-classic非常自然地实现了SLF4J，  所 以切换到log4j或者其他，非常容易，只需要提供成另一个jar包就OK，根本不需要去动那些通过SLF4JAPI实现的代码。

- 非常充分的文档  官方网站有两百多页的文档。

- 自动重新加载配置文件  当配置文件修改，Logback-classic能自动重新加载配置文件。扫描过程快且安全，它并不需要另外创建一个扫描线程。

- Lilith   Lilith是log事件的观察者，和log4j的chainsaw类似。而lilith还能处理大数量的log数据 。

- 谨慎的模式和非常友好的恢复  在谨慎模式下，多个FileAppender实例跑在多个JVM下，能 够安全地写道同一个日志文件，RollingFileAppender会有些限制。Logback的FileAppender和它的子类包括 RollingFileAppender能够非常友好地从I/O异常中恢复。

- 配置文件可以处理不同的情况   开发人员经常需要判断不同的Logback配置文件在不同的环境下（开发，测试，生产）。

- Filters（过滤器）  有些时候，需要诊断一个问题，需要打出日志。在log4j，只有降低日志级别，不过这样会打出大量的日志，会影响应用性能。在Logback，你可以继续 保持那个日志级别而除掉某种特殊情况，如alice这个用户登录，她的日志将打在DEBUG级别而其他用户可以继续打在WARN级别。要实现这个功能只需 加4行XML配置。可以参考MDCFIlter 。

- SiftingAppender（一个非常多功能的Appender）  它可以用来分割日志文件根据任何一个给定的运行参数。如，SiftingAppender能够区别日志事件跟进用户的Session，然后每个用户会有一个日志文件。

- 自动压缩已经打出来的log  RollingFileAppender在产生新文件的时候，会自动压缩已经打出来的日志文件。压缩是个异步过程，所以甚至对于大的日志文件，在压缩过程中应用不会受任何影响。

- 堆栈树带有包版本  Logback在打出堆栈树日志时，会带上包的数据。

- 自动去除旧的日志文件  通过设置TimeBasedRollingPolicy或者SizeAndTimeBasedFNATP的maxHistory属性，你可以控制已经产生日志文件的最大数量。

总之，logback比log4j优秀。

### logback配置
Logback 配置文件的语法非常灵活。正因为灵活，所以无法用 DTD 或 XML schema 进行定义。尽管如此，可以这样描述配置文件的基本结构：以<configuration>开头，后面有零个或多个<appender>元素，有零个或多个<logger>元素，有最多一个<root>元素。

logback配置文件实例：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration debug="false">
    <!--定义日志文件的存储地址 勿在 LogBack 的配置中使用相对路径-->  
    <property name="LOG_HOME" value="/home" />  
    <!-- 控制台输出 -->   
    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder"> 
             <!--格式化输出：%d表示日期，%thread表示线程名，%-5level：级别从左显示5个字符宽度%msg：日志消息，%n是换行符--> 
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n</pattern>   
        </encoder> 
    </appender>
    <!-- 按照每天生成日志文件 -->   
    <appender name="FILE"  class="ch.qos.logback.core.rolling.RollingFileAppender">   
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!--日志文件输出的文件名-->
            <FileNamePattern>${LOG_HOME}/TestWeb.log.%d{yyyy-MM-dd}.log</FileNamePattern> 
            <!--日志文件保留天数-->
            <MaxHistory>30</MaxHistory>
        </rollingPolicy>   
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder"> 
            <!--格式化输出：%d表示日期，%thread表示线程名，%-5level：级别从左显示5个字符宽度%msg：日志消息，%n是换行符--> 
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n</pattern>   
        </encoder> 
        <!--日志文件最大的大小-->
       <triggeringPolicy class="ch.qos.logback.core.rolling.SizeBasedTriggeringPolicy">
         <MaxFileSize>10MB</MaxFileSize>
       </triggeringPolicy>
    </appender> 
    
    <!--myibatis log configure--> 
    <logger name="com.apache.ibatis" level="TRACE"/>
    <logger name="java.sql.Connection" level="DEBUG"/>
    <logger name="java.sql.Statement" level="DEBUG"/>
    <logger name="java.sql.PreparedStatement" level="DEBUG"/>
    
    <!-- 日志输出级别 -->
    <root level="INFO">
        <appender-ref ref="STDOUT" />
        <appender-ref ref="FILE" />
    </root> 
</configuration>
```
# logback日志详解

## 根节点<configuration>包含的属性

**scan**:

当此属性设置为true时，配置文件如果发生改变，将会被重新加载，默认值为true。

**scanPeriod**:

设置监测配置文件是否有修改的时间间隔，如果没有给出时间单位，默认单位是毫秒。当scan为true时，此属性生效。默认的时间间隔为1分钟。

**debug**:

当此属性设置为true时，将打印出logback内部日志信息，实时查看logback运行状态。默认值为false。

例如：

```xml
<configuration scan="true" scanPeriod="60 seconds" debug="false"> 
    <!-- 其他配置省略-->
</configuration>
```

## 根节点<configuration>的子节点

![image](http://ovi3ob9p4.bkt.clouddn.com/springboot/springboot011.jpg)

## 设置上下文名称：<contextName>

每个logger都关联到logger上下文，默认上下文名称为“default”。但可以使用<contextName>设置成其他名字，用于区分不同应用程序的记录。一旦设置，不能修改。

```xml
<configuration scan="true" scanPeriod="60 seconds" debug="false">                           
    <contextName>myAppName</contextName> 
    <!-- 其他配置省略--> 
</configuration>
```

## 设置变量：<property>

用来定义变量值的标签，<property> 有两个属性，name和value；其中name的值是变量的名称，value的值时变量定义的值。通过<property>定义的值会被插入到logger上下文中。定义变量后，可以使“${}”来使用变量。

例如使用<property>定义上下文名称，然后在<contentName>设置logger上下文时使用。

```xml
<configuration scan="true" scanPeriod="60 seconds" debug="false"> 
    <property name="APP_Name" value="myAppName" /> 
    <contextName>${APP_Name}</contextName> 
    <!-- 其他配置省略--> 
</configuration>
```

## 获取时间戳字符串：<timestamp>

有两个属性，key:标识此<timestamp> 的名字；datePattern：设置将当前时间（解析配置文件的时间）转换为字符串的模式，遵循java.txt.SimpleDateFormat的格式。

例如将解析配置文件的时间作为上下文名称：

```xml
<configuration scan="true" scanPeriod="60 seconds" debug="false"> 
    <timestamp key="bySecond" datePattern="yyyyMMdd'T'HHmmss"/> 
    <contextName>${bySecond}</contextName> 
    <!-- 其他配置省略--> 
</configuration>
```

## 设置logger节点
<logger>

用来设置某一个包或者具体的某一个类的日志打印级别、以及指定<appender>。<logger>有一个必选的name属性，一个可选的level和一个可选的addtivity属性。

name:用来指定受此loger约束的某一个包或者具体的某一个类。

level:用来设置打印级别，大小写无关：TRACE, DEBUG, INFO, WARN, ERROR, ALL 和 OFF，还有一个特殊值INHERITED或者同义词NULL，代表强制执行上级的级别。如果未设置此属性，那么当前loger将会继承上级的级别。这里总结下各个节点的优先级：root<appender<logger。还需要在实践中不断总结，精确控制日志的输出

addtivity:是否向上级logger传递打印信息。默认是true。（用的不多）<logger>可以包含零个或多个<appender-ref>元素，标识这个appender将会添加到这个logger。

实践总结：当<logger>中包含<appender-ref>时，如果addtivity=true，则会将打印信息传递到root；如果addtivity=false，则只会在<logger>中<appender-ref>打印信息，不会向上传递。

## 设置root节点

<root>也是<logger>元素，但是它是根logger， 只有一个level属性。

level:用来设置打印级别，大小写无关：TRACE, DEBUG, INFO, WARN, ERROR, ALL 和 OFF，不能设置为INHERITED或者同义词NULL。默认是DEBUG。

<root>可以包含零个或多个<appender-ref>元素，标识这个appender将会添加到这个logger。

实例：

```java
package com.logback;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class LogbackDemo {
    private static final Logger log = LoggerFactory.getLogger(LogbackDemo.class);

    public static void main(String[] args) {
        log.trace("======trace");
        log.debug("======debug");
        log.info("======info");
        log.warn("======warn");
        log.error("======error");
    }
}
```

logback.xml配置文件

- 第1种：只配置root

```xml
<configuration> 
    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender"> 
    <!-- encoder 默认配置为PatternLayoutEncoder --> 
        <encoder> 
            <pattern>%d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n</pattern> 
        </encoder> 
    </appender> 

    <root level="INFO"> 
        <appender-ref ref="STDOUT" /> 
    </root> 
</configuration>
```

其中appender的配置表示打印到控制台(稍后详细讲解appender )；<root level="INFO">将root的打印级别设置为“INFO”，指定了名字为“STDOUT”的appender。

当执行com.logback.LogbackDemo类的main方法时，root将级别为“INFO”及大于“INFO”的日志信息交给已经配置好的名为“STDOUT”的appender处理，“STDOUT”appender将信息打印到控制台；

打印结果如下：

```log
13:30:38.484 [main] INFO com.logback.LogbackDemo - ======info 
13:30:38.500 [main] WARN com.logback.LogbackDemo - ======warn 
13:30:38.500 [main] ERROR com.logback.LogbackDemo - ======error
```
- 第2种：带有logger的配置，不指定级别，不指定appender，

```xml
<configuration>
	<appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender"> 
<!-- encoder 默认配置为PatternLayoutEncoder -->
		<encoder>
			<pattern>%d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n</pattern>
		</encoder>
	</appender> <!-- com.logback为java中的包 -->
	<logger name="com.logback" />
	<root level="DEBUG">
		<appender-ref ref="STDOUT" />
	</root>
</configuration>
```

其中appender的配置表示打印到控制台(稍后详细讲解appender )；<logger name="com.logback" />将控制logback包下的所有类的日志的打印，但是并没有设置打印级别，所以继承他的上级<root>的日志级别“DEBUG”；没有设置addtivity，默认为true，将此logger的打印信息向上级传递；没有设置appender，此logger本身不打印任何信息。<root level="DEBUG">将root的打印级别设置为“DEBUG”，指定了名字为“STDOUT”的appender。

当执行com.logback.LogbackDemo类的main方法时，因为LogbackDemo 在包logback中，所以首先执行<logger name="com.logback" />，将级别为“DEBUG”及大于“DEBUG”的日志信息传递给root，本身并不打印；

root接到下级传递的信息，交给已经配置好的名为“STDOUT”的appender处理，“STDOUT”appender将信息打印到控制台。

打印结果如下：

```log
13:19:15.406 [main] DEBUG com.logback.LogbackDemo - ======debug 
13:19:15.406 [main] INFO com.logback.LogbackDemo - ======info 
13:19:15.406 [main] WARN com.logback.LogbackDemo - ======warn 
13:19:15.406 [main] ERROR com.logback.LogbackDemo - ======error
```

- 第3种：带有多个loger的配置，指定级别，指定appender 

```xml
<configuration>
	<appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender"> 
        <!-- encoder 默认配置为PatternLayoutEncoder -->
		<encoder>
			<pattern>%d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n</pattern>
		</encoder>
	</appender> <!-- logback为java中的包 -->
	<logger name="com.logback" /> <!--logback.LogbackDemo：类的全路径 -->
	<logger name="com.logback.LogbackDemo" level="INFO" additivity="false">
		<appender-ref ref="STDOUT" />
	</logger>
	<root level="ERROR">
		<appender-ref ref="STDOUT" />
	</root>
</configuration>
```

其中appender的配置表示打印到控制台(稍后详细讲解appender )；<logger name="com.logback" />将控制logback包下的所有类的日志的打印，但是并没用设置打印级别，所以继承他的上级<root>的日志级别“DEBUG”；没有设置addtivity，默认为true，将此loger的打印信息向上级传递；没有设置appender，此loger本身不打印任何信息。

 <logger name="com.logback.LogbackDemo" level="INFO" additivity="false">控制logback.LogbackDemo类的日志打印，打印级别为“INFO”；additivity属性为false，表示此loger的打印信息不再向上级传递，指定了名字为“STDOUT”的appender。<root level="ERROR">将root的打印级别设置为“ERROR”，指定了名字为“STDOUT”的appender。

 当执行com.logback.LogbackDemo类的main方法时，先执行<logger name="com.logback.LogbackDemo" level="INFO" additivity="false">，将级别为“INFO”及大于“INFO”的日志信息交给此loger指定的名为“STDOUT”的appender处理，在控制台中打出日志，不再向次loger的上级 <logger name="logback"/> 传递打印信息；<logger name="com.logback"/>未接到任何打印信息，当然也不会给它的上级root传递任何打印信息；

打印结果如下：

```xml
14:05:35.937 [main] INFO  com.logback.LogbackDemo - ======info
14:05:35.937 [main] WARN  com.logback.LogbackDemo - ======warn
14:05:35.937 [main] ERROR com.logback.LogbackDemo - ======error
```

如果将<logger name="com.logback.LogbackDemo" level="INFO" additivity="false">修改为 <logger name="com.logback.LogbackDemo" level="INFO" additivity="true">那打印结果将是什么呢？

没错，日志打印了两次，想必大家都知道原因了，因为打印信息向上级传递，logger本身打印一次，root接到后又打印一次。

这里要注意，<logger>中是包含<appender-ref>的，所以<logger>和<root>节点中的append-ref同时打印日志信息。但是logger的优先级高于root，所以日志信息以logger为主

打印结果如下： 

```xml
14:09:01.531 [main] INFO  com.logback.LogbackDemo - ======info
14:09:01.531 [main] INFO  com.logback.LogbackDemo - ======info
14:09:01.531 [main] WARN  com.logback.LogbackDemo - ======warn
14:09:01.531 [main] WARN  com.logback.LogbackDemo - ======warn
14:09:01.531 [main] ERROR com.logback.LogbackDemo - ======error
14:09:01.531 [main] ERROR com.logback.LogbackDemo - ======error
```

## 设置appender节点

![image](http://ovi3ob9p4.bkt.clouddn.com/springboot/springboot008.jpg)

<appender>是<configuration>的子节点，是负责写日志的组件。

<appender>有两个必要属性name和class。name指定appender名称，class指定appender的全限定名。

1. ConsoleAppender:

把日志添加到控制台，有以下子节点：

<encoder>：对日志进行格式化。（具体参数稍后讲解 ）

<target>：字符串 System.out 或者 System.err ，默认 System.out ；

例如：

```xml
<configuration>  
    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">  
        <encoder>  
            <pattern>%-4relative [%thread] %-5level %logger{35} - %msg %n</pattern>  
        </encoder>  
    </appender>  
  
    <root level="DEBUG">  
        <appender-ref ref="STDOUT" />  
    </root>  
</configuration>
```
 
2. FileAppender:

把日志添加到文件，有以下子节点：

- <file>：被写入的文件名，可以是相对目录，也可以是绝对目录，如果上级目录不存在会自动创建，没有默认值。

- <append>：如果是 true，日志被追加到文件结尾，如果是 false，清空现存文件，默认是true。

- <encoder>：对记录事件进行格式化。（具体参数稍后讲解 ）

- <prudent>：如果是 true，日志会被安全的写入文件，即使其他的FileAppender也在向此文件做写入操作，效率低，默认是 false。

例如：

```xml
<configuration>
	<appender name="FILE" class="ch.qos.logback.core.FileAppender">
		<file>testFile.log</file>
		<append>true</append>
		<encoder>
			<pattern>%-4relative [%thread] %-5level %logger{35} - %msg%n</pattern>
		</encoder>
	</appender>

	<root level="DEBUG">
		<appender-ref ref="FILE" />
	</root>
</configuration>
```

3. RollingFileAppender:

滚动记录文件，先将日志记录到指定文件，当符合某个条件时，将日志记录到其他文件。有以下子节点：

<file>：被写入的文件名，可以是相对目录，也可以是绝对目录，如果上级目录不存在会自动创建，没有默认值。

<append>：如果是 true，日志被追加到文件结尾，如果是 false，清空现存文件，默认是true。

<encoder>：对记录事件进行格式化。（具体参数稍后讲解 ）

<rollingPolicy>:当发生滚动时，决定 RollingFileAppender 的行为，涉及文件移动和重命名。

<triggeringPolicy >: 告知 RollingFileAppender 合适激活滚动。

<prudent>：当为true时，不支持FixedWindowRollingPolicy。支持TimeBasedRollingPolicy，但是有两个限制，1不支持也不允许文件压缩，2不能设置file属性，必须留空。

 

rollingPolicy：

TimeBasedRollingPolicy： 最常用的滚动策略，它根据时间来制定滚动策略，既负责滚动也负责出发滚动。有以下子节点：

<fileNamePattern>:必要节点，包含文件名及“%d”转换符， “%d”可以包含一个java.text.SimpleDateFormat指定的时间格式，如：%d{yyyy-MM}。如果直接使用 %d，默认格式是 yyyy-MM-dd。RollingFileAppender 的file字节点可有可无，通过设置file，可以为活动文件和归档文件指定不同位置，当前日志总是记录到file指定的文件（活动文件），活动文件的名字不会改变；如果没设置file，活动文件的名字会根据fileNamePattern 的值，每隔一段时间改变一次。“/”或者“\”会被当做目录分隔符。

<maxHistory>:可选节点，控制保留的归档文件的最大数量，超出数量就删除旧文件。假设设置每个月滚动，且<maxHistory>是6，则只保存最近6个月的文件，删除之前的旧文件。注意，删除旧文件是，那些为了归档而创建的目录也会被删除。

FixedWindowRollingPolicy： 根据固定窗口算法重命名文件的滚动策略。有以下子节点：

<minIndex>:窗口索引最小值

<maxIndex>:窗口索引最大值，当用户指定的窗口过大时，会自动将窗口设置为12。

<fileNamePattern >:必须包含“%i”例如，假设最小值和最大值分别为1和2，命名模式为 mylog%i.log,会产生归档文件mylog1.log和mylog2.log。还可以指定文件压缩选项，例如，mylog%i.log.gz 或者 没有log%i.log.zip

triggeringPolicy:

SizeBasedTriggeringPolicy： 查看当前活动文件的大小，如果超过指定大小会告知RollingFileAppender 触发当前活动文件滚动。只有一个节点:<maxFileSize>:这是活动文件的大小，默认值是10MB。

 例如：每天生成一个日志文件，保存30天的日志文件。


```xml
<configuration>
	<appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">

		<rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
			<fileNamePattern>logFile.%d{yyyy-MM-dd}.log</fileNamePattern>
			<maxHistory>30</maxHistory>
		</rollingPolicy>

		<encoder>
			<pattern>%-4relative [%thread] %-5level %logger{35} - %msg%n</pattern>
		</encoder>
	</appender>

	<root level="DEBUG">
		<appender-ref ref="FILE" />
	</root>
</configuration>
```

例如：按照固定窗口模式生成日志文件，当文件大于20MB时，生成新的日志文件。窗口大小是1到3，当保存了3个归档文件后，将覆盖最早的日志。

```xml
<configuration>
	<appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
		<file>test.log</file>

		<rollingPolicy class="ch.qos.logback.core.rolling.FixedWindowRollingPolicy">
			<fileNamePattern>tests.%i.log.zip</fileNamePattern>
			<minIndex>1</minIndex>
			<maxIndex>3</maxIndex>
		</rollingPolicy>

		<triggeringPolicy class="ch.qos.logback.core.rolling.SizeBasedTriggeringPolicy">
			<maxFileSize>5MB</maxFileSize>
		</triggeringPolicy>
		<encoder>
			<pattern>%-4relative [%thread] %-5level %logger{35} - %msg%n</pattern>
		</encoder>
	</appender>

	<root level="DEBUG">
		<appender-ref ref="FILE" />
	</root>
</configuration>
```

4. 其他Appender

另外还有SocketAppender、SMTPAppender、DBAppender、SyslogAppender、SiftingAppender，并不常用，这些就不在这里讲解了，大家可以参考官方文档。当然大家可以编写自己的Appender。

<encoder>节点详解：

负责两件事，一是把日志信息转换成字节数组，二是把字节数组写入到输出流。

目前PatternLayoutEncoder 是唯一有用的且默认的encoder ，有一个<pattern>节点，用来设置日志的输入格式。使用“%”加“转换符”方式，如果要输出“%”，则必须用“\”对“\%”进行转义。

例如：

```xml
<encoder>   
   <pattern>%-4relative [%thread] %-5level %logger{35} - %msg%n</pattern>   
</encoder
```
  
<pattern>里面的转换符说明：

![image](http://ovi3ob9p4.bkt.clouddn.com/springboot/springboot009.jpg)

格式修饰符，与转换符共同使用：

可选的格式修饰符位于“%”和转换符之间。

第一个可选修饰符是左对齐 标志，符号是减号“-”；接着是可选的最小宽度 修饰符，用十进制数表示。如果字符小于最小宽度，则左填充或右填充，默认是左填充（即右对齐），填充符为空格。如果字符大于最小宽度，字符永远不会被截断。最大宽度 修饰符，符号是点号"."后面加十进制数。如果字符大于最大宽度，则从前面截断。点符号“.”后面加减号“-”在加数字，表示从尾部截断。

例如：%-4relative 表示，将输出从程序启动到创建日志记录的时间 进行左对齐 且最小宽度为4。

## appender节点的子节点filter详解
<filter>:

过滤器，执行一个过滤器会有返回个枚举值，即DENY，NEUTRAL，ACCEPT其中之一。返回DENY，日志将立即被抛弃不再经过其他过滤器；返回NEUTRAL，有序列表里的下个过滤器过接着处理日志；返回ACCEPT，日志会被立即处理，不再经过剩余过滤器。

过滤器被添加到<Appender> 中，为<Appender> 添加一个或多个过滤器后，可以用任意条件对日志进行过滤。<Appender> 有多个过滤器时，按照配置顺序执行。

 
下面是几个常用的过滤器：

- LevelFilter： 级别过滤器，根据日志级别进行过滤。如果日志级别等于配置级别，过滤器会根据onMath 和 onMismatch接收或拒绝日志。有以下子节点：

- <level>:设置过滤级别

- <onMatch>:用于配置符合过滤条件的操作

- <onMismatch>:用于配置不符合过滤条件的操作

例如：将过滤器的日志级别配置为INFO，所有INFO级别的日志交给appender处理，非INFO级别的日志，被过滤掉。

```xml
<configuration>
	<appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
		<filter class="ch.qos.logback.classic.filter.LevelFilter">
			<level>INFO</level>
			<onMatch>ACCEPT</onMatch>
			<onMismatch>DENY</onMismatch>
		</filter>
		<encoder>
			<pattern>
				%-4relative [%thread] %-5level %logger{30} - %msg%n
			</pattern>
		</encoder>
	</appender>
	<root level="DEBUG">
		<appender-ref ref="CONSOLE" />
	</root>
</configuration>
```
ThresholdFilter： 临界值过滤器，过滤掉低于指定临界值的日志。当日志级别等于或高于临界值时，过滤器返回NEUTRAL；当日志级别低于临界值时，日志会被拒绝。

例如：过滤掉所有低于INFO级别的日志。

```xml
<configuration>
	<appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
		<!-- 过滤掉 TRACE 和 DEBUG 级别的日志 -->
		<filter class="ch.qos.logback.classic.filter.ThresholdFilter">
			<level>INFO</level>
		</filter>
		<encoder>
			<pattern>
				%-4relative [%thread] %-5level %logger{30} - %msg%n
			</pattern>
		</encoder>
	</appender>
	<root level="DEBUG">
		<appender-ref ref="CONSOLE" />
	</root>
</configuration>
```

EvaluatorFilter： 求值过滤器，评估、鉴别日志是否符合指定条件。有一下子节点：

<evaluator>:鉴别器，常用的鉴别器是JaninoEventEvaluato，也是默认的鉴别器，它以任意的java布尔值表达式作为求值条件，求值条件在配置文件解释过成功被动态编译，布尔值表达式返回true就表示符合过滤条件。evaluator有个子标签<expression>，用于配置求值条件。

求值表达式作用于当前日志，logback向求值表达式暴露日志的各种字段：

![image](http://ovi3ob9p4.bkt.clouddn.com/springboot/springboot010.jpg)

 <onMatch>:用于配置符合过滤条件的操作

<onMismatch>:用于配置不符合过滤条件的操作

例如：过滤掉所有日志消息中不包含“billing”字符串的日志。

```xml
<configuration>

	<appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
		<filter class="ch.qos.logback.core.filter.EvaluatorFilter">
			<evaluator> <!-- 默认为 ch.qos.logback.classic.boolex.JaninoEventEvaluator -->
				<expression>return message.contains("billing");</expression>
			</evaluator>
			<OnMatch>ACCEPT </OnMatch>
			<OnMismatch>DENY</OnMismatch>
		</filter>
		<encoder>
			<pattern>
				%-4relative [%thread] %-5level %logger - %msg%n
			</pattern>
		</encoder>
	</appender>

	<root level="INFO">
		<appender-ref ref="STDOUT" />
	</root>
</configuration>
```
<matcher> ：匹配器，尽管可以使用String类的matches()方法进行模式匹配，但会导致每次调用过滤器时都会创建一个新的Pattern对象，为了消除这种开销，可以预定义一个或多个matcher对象，定以后就可以在求值表达式中重复引用。<matcher>是<evaluator>的子标签。<matcher>中包含两个子标签，一个是<name>，用于定义matcher的名字，求值表达式中使用这个名字来引用matcher；另一个是<regex>，用于配置匹配条件。

例如：

```xml
<configuration debug="true">

	<appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
		<filter class="ch.qos.logback.core.filter.EvaluatorFilter">
			<evaluator>
				<matcher>
					<Name>odd</Name>
					<!-- filter out odd numbered statements -->
					<regex>statement [13579]</regex>
				</matcher>

				<expression>odd.matches(formattedMessage)</expression>
			</evaluator>
			<OnMismatch>NEUTRAL</OnMismatch>
			<OnMatch>DENY</OnMatch>
		</filter>
		<encoder>
			<pattern>%-4relative [%thread] %-5level %logger - %msg%n</pattern>
		</encoder>
	</appender>

	<root level="DEBUG">
		<appender-ref ref="STDOUT" />
	</root>
</configuration>
```

其他Filter不太常用我这里就不讲了，大家可以参见官网。
