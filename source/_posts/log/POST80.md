---
title: 日志组件slf4j介绍及配置详解
tags: SLF4J
category: SLF4J
date: 2018-01-24 14:44:36
---
![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0101.jpg)

每一个Java程序员都知道日志对于任何一个Java应用程序尤其是服务端程序是至关重要的，而很多程序员也已经熟悉各种不同的日志库，如java.util.logging、Apache log4j、logback。但如果你还不知道SLF4J（Simple logging facade for Java）的话，那么是时候在你的项目中学习使用SLF4J了。
<!--more-->

### 基本介绍

SLF4J不同于其他日志类库，与其它日志类库有很大的不同。SLF4J(Simple logging Facade for Java)不是一个真正的日志实现，而是一个抽象层（ abstraction layer），它允许你在后台使用任意一个日志类库。如果是在编写供内外部都可以使用的API或者通用类库，那么你真不会希望使用你类库的客户端必须使用你选择的日志类库。

如果一个项目已经使用了log4j，而你加载了一个类库，比方说 Apache Active MQ——它依赖于于另外一个日志类库logback，那么你就需要把它也加载进去。但如果Apache Active MQ使用了SLF4J，你可以继续使用你的日志类库而无需忍受加载和维护一个新的日志框架的痛苦。

总的来说，SLF4J使你的代码独立于任意一个特定的日志API，这是对于API开发者的很好的思想。虽然抽象日志类库的思想已经不是新鲜的事物，而且Apache commons logging也已经在使用这种思想了，但SLF4J正迅速成为Java世界的日志标准。让我们再看几个使用SLF4J而不是log4j、logback或者java.util.logging的理由。

### SLF4J对比Log4J，logback和java.util.Logging的优势

正如我之前说的，在你的代码中使用SLF4J写日志语句的主要出发点是使得你的程序独立于任何特定的日志类库，依赖于特定类库可能需要使用不同于你已有的配置，并且导致更多维护的麻烦。除此之外，还有一个SLF4J API的特性是使得我坚持使用SLF4J而抛弃我长期间钟爱的Log4j的理由，是被称为占位符(place holder)，在代码中表示为“{}”的特性。占位符是一个非常类似于在String的format()方法中的%s，因为它会在运行时被某个提供的实际字符串所替换。这不仅降低了你代码中字符串连接次数，而且还节省了新建的String对象。通过使用SLF4J，你可以在运行时延迟字符串的建立，这意味着只有需要的String对象才被建立。而如果你已经使用log4j，那么你已经对于在if条件中使用debug语句这种变通方案十分熟悉了，但SLF4J的占位符就比这个好用得多。

这是你在Log4j中使用的方案，但这并不有趣，而且降低了代码可读性，因为它增加了不必要的繁琐重复代码(boiler-plate code)：

```java
if (logger.isDebugEnabled()) {
    logger.debug("Processing trade with id: " + id + " symbol: " + symbol);
}
```

另一方面，如果你使用SLF4J的话，你可以得到更简洁格式的结果，就像以下展示的一样：

```java
logger.debug("Processing trade with id: {} and symbol : {} ", id, symbol);
```

在SLF4J，我们不需要字符串连接而且不会导致暂时不需要的字符串消耗。取而代之，我们在一个以占位符和参数传递实际值构成的模板格式下写日志信息。你可能会在想万一我有很多个参数怎么办？嗯，那么你可以选择使用变量参数版本的日志方法或者以Object数组传递。这是一个相当方便和高效方法的打日志方法。记住，在生产最终日志信息字符串之前，这个方法会检查一个特定的日志级别是不是打开了，这不仅降低了内存消耗而且预先降低了CPU去处理字符串连接命令的时间。这里是使用SLF4J日志方法的代码，来自于slf4j-log4j12-1.6.1.jar中的Log4j的适配器类Log4jLoggerAdapter。

```java
public void debug(String format, Object arg1, Object arg2) {
    if (logger.isDebugEnabled()) {
        FormattingTuple ft = MessageFormatter.format(format, arg1, arg2);
        logger.log(FQCN, Level.DEBUG, ft.getMessage(), ft.getThrowable());
    }
}
```

同时，我们应该知道打日志是对应用程序性能有着很大影响，在生产环节上我们建议只进行必要的日志记录。

### 使用配置

#### maven依赖

```xml
<dependency>
  <groupId>org.slf4j</groupId>
  <artifactId>slf4j-api</artifactId>
  <version>1.7.21</version>
</dependency>
<dependency>
  <groupId>org.slf4j</groupId>
  <artifactId>slf4j-log4j12</artifactId>
  <version>1.7.21</version>
</dependency>
```

#### 日志系统配置

假设现有如下程序：

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
public class Main {
    private static final Logger logger = LoggerFactory.getLogger(Main.class);
    public static void main(String[] args) {
        int status = 0;
        if (status == 0) {
            logger.info("status:{}", status);
        } else {
            logger.info("status:{}", status);
        }
        logger.info("end!");
    }
}
```

可以使用以下两种方式对日志系统的输出格式、记录级别、输出方式等进行配置。

##### properties文件方式

log4j.properties:

```properties
log4j.rootLogger=info, ServerDailyRollingFile, stdout
log4j.appender.ServerDailyRollingFile=org.apache.log4j.DailyRollingFileAppender
log4j.appender.ServerDailyRollingFile.DatePattern='.'yyyy-MM-dd
log4j.appender.ServerDailyRollingFile.File=logs/notify-subscription.log
log4j.appender.ServerDailyRollingFile.layout=org.apache.log4j.PatternLayout
log4j.appender.ServerDailyRollingFile.layout.ConversionPattern=%d - %m%n
log4j.appender.ServerDailyRollingFile.Append=true
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%d{yyyy-MM-dd HH\:mm\:ss} %p [%c] %m%n
```

输出结果为：

```xml
2016-05-12 16:08:21 INFO [club.chuxing.learn.Main] status:0 
2016-05-12 16:08:21 INFO [club.chuxing.learn.Main] end!
```

##### xml文件方式

首先pom中添加如下依赖：

```xml
<dependency>
  <groupId>ch.qos.logback</groupId>
  <artifactId>logback-core</artifactId>
  <version>1.1.7</version>
</dependency>
<dependency>
  <groupId>ch.qos.logback</groupId>
  <artifactId>logback-classic</artifactId>
  <version>1.1.7</version>
</dependency>
```

logback.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration debug="true">
    <!-- 应用名称 -->
    <property name="APP_NAME" value="logtest" />
    <!--日志文件的保存路径,首先查找系统属性-Dlog.dir,如果存在就使用其；否则，在当前目录下创建名为logs目录做日志存放的目录 -->
    <property name="LOG_HOME" value="${log.dir:-logs}/${APP_NAME}" />
    <!-- 日志输出格式 -->
    <property name="ENCODER_PATTERN"
              value="%d{yyyy-MM-dd  HH:mm:ss.SSS} [%thread] %-5level %logger{80} - %msg%n" />
    <contextName>${APP_NAME}</contextName>
    
    <!-- 控制台日志：输出全部日志到控制台 -->
    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <Pattern>${ENCODER_PATTERN}</Pattern>
        </encoder>
    </appender>
    
    <!-- 文件日志：输出全部日志到文件 -->
    <appender name="FILE"
              class="ch.qos.logback.core.rolling.RollingFileAppender">
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>${LOG_HOME}/output.%d{yyyy-MM-dd}.log</fileNamePattern>
            <maxHistory>7</maxHistory>
        </rollingPolicy>
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <pattern>${ENCODER_PATTERN}</pattern>
        </encoder>
    </appender>
    
    <!-- 错误日志：用于将错误日志输出到独立文件 -->
    <appender name="ERROR_FILE"
              class="ch.qos.logback.core.rolling.RollingFileAppender">
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>${LOG_HOME}/error.%d{yyyy-MM-dd}.log</fileNamePattern>
            <maxHistory>7</maxHistory>
        </rollingPolicy>
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <pattern>${ENCODER_PATTERN}</pattern>
        </encoder>
        <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
            <level>WARN</level>
        </filter>
    </appender>
    
    <!-- 独立输出的同步日志 -->
    <appender name="SYNC_FILE"  class="ch.qos.logback.core.rolling.RollingFileAppender">
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>${LOG_HOME}/sync.%d{yyyy-MM-dd}.log</fileNamePattern>
            <maxHistory>7</maxHistory>
        </rollingPolicy>
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <pattern>${ENCODER_PATTERN}</pattern>
        </encoder>
    </appender>
    
    <logger name="log.sync" level="DEBUG" addtivity="true">
        <appender-ref ref="SYNC_FILE" />
    </logger>
    
    <root>
        <level value="DEBUG" />
        <appender-ref ref="STDOUT" />
        <appender-ref ref="FILE" />
        <appender-ref ref="ERROR_FILE" />
    </root>
</configuration>
```

输出结果为：

```
2016-05-12 17:08:32.105 [main] INFO club.chuxing.learn.Main - status:0 
2016-05-12 17:08:32.114 [main] INFO club.chuxing.learn.Main - end!
```

#### 日志系统配置说明

##### 输出级别的种类

1. ERROR 为严重错误 主要是程序的错误
2. WARN 为一般警告，比如session丢失
3. INFO 为一般要显示的信息，比如登录登出
4. DEBUG 为程序的调试信息

##### 配置日志信息输出目的地
> log4j.appender.appenderName=??
>
> org.apache.log4j.ConsoleAppender（控制台）
> org.apache.log4j.FileAppender（文件）
> org.apache.log4j.DailyRollingFileAppender（每天产生一个日志文件）
> org.apache.log4j.RollingFileAppender（文件大小到达指定尺寸的时候产生一个新的文件）
> org.apache.log4j.WriterAppender（将日志信息以流格式发送到任意指定的地方）

##### 配置日志信息的格式
> log4j.appender.appenderName.layout = ??
>
> org.apache.log4j.HTMLLayout（以HTML表格形式布局）
> org.apache.log4j.PatternLayout（可以灵活地指定布局模式）
> org.apache.log4j.SimpleLayout（包含日志信息的级别和信息字符串）
> org.apache.log4j.TTCCLayout（包含日志产生的时间、线程、类别等等信息）

##### ConsoleAppender选项
> Threshold=DEBUG:指定日志消息的输出最低层次。 
> ImmediateFlush=true:默认值是true,意谓着所有的消息都会被立即输出。 
> Target=System.err:默认情况下是System.out,指定输出控制台
>

##### FileAppender 选项
> Threshold=DEBUG:指定日志消息的输出最低层次。 
> ImmediateFlush=true:默认值是true,意谓着所有的消息都会被立即输出。 
> File=mylog.txt:指定消息输出到mylog.txt文件。 
> Append=false:默认值是true,即将消息增加到指定文件中，false指将消息覆盖指定的文件内容。
>

##### RollingFileAppender 选项
> Threshold=DEBUG:指定日志消息的输出最低层次。 
> ImmediateFlush=true:默认值是true,意谓着所有的消息都会被立即输出。 
> File=mylog.txt:指定消息输出到mylog.txt文件。 
> Append=false:默认值是true,即将消息增加到指定文件中，false指将消息覆盖指定的文件内容。 
> MaxFileSize=100KB: 后缀可以是KB, MB 或者是 GB. 在日志文件到达该大小时，将会自动滚动，即将原来的内容移到mylog.log.1文件。 
> MaxBackupIndex=2:指定可以产生的滚动文件的最大数。
>

##### 日志信息格式中几个符号所代表的含义
> -X号: X信息输出时左对齐； 
> %p: 输出日志信息优先级，即DEBUG，INFO，WARN，ERROR，FATAL, 
> %d: 输出日志时间点的日期或时间，默认格式为ISO8601，也可以在其后指定格式，比如：%d{yyy MMM dd HH:mm:ss,SSS}，输出类似：2002年10月18日 22：10：28，921 
> %r: 输出自应用启动到输出该log信息耗费的毫秒数 
> %c: 输出日志信息所属的类目，通常就是所在类的全名 
> %t: 输出产生该日志事件的线程名 
> %l: 输出日志事件的发生位置，相当于%C.%M(%F:%L)的组合,包括类目名、发生的线程，以及在代码中的行数。举例：Testlog4.main (TestLog4.java:10) 
> %x: 输出和当前线程相关联的NDC(嵌套诊断环境),尤其用到像java servlets这样的多客户多线程的应用中。 
> %%: 输出一个”%”字符 
> %F: 输出日志消息产生时所在的文件名称 
> %L: 输出代码中的行号 
> %m: 输出代码中指定的消息,产生的日志具体信息 
> %n: 输出一个回车换行符，Windows平台为”\r\n”，Unix平台为”\n”输出日志信息换行
>

一个示例配置文件

```properties
log4j.debug=true   
log4j.rootLogger=DEBUG,D,E
log4j.appender.E = org.apache.log4j.DailyRollingFileAppender
log4j.appender.E.File = logs/logs.log
log4j.appender.E.Append = true
log4j.appender.E.Threshold = DEBUG
log4j.appender.E.layout = org.apache.log4j.PatternLayout
log4j.appender.E.layout.ConversionPattern = %-d{yyyy-MM-dd HH:mm:ss}  [ %t:%r ] - [ %p ]  %m%n
log4j.appender.D = org.apache.log4j.DailyRollingFileAppender
log4j.appender.D.File = logs/error.log
log4j.appender.D.Append = true
log4j.appender.D.Threshold = ERROR
log4j.appender.D.layout = org.apache.log4j.PatternLayout
log4j.appender.D.layout.ConversionPattern = %-d{yyyy-MM-dd HH:mm:ss}  [ %t:%r ] - [ %p ]  %m%n

```

### 过滤器 

过滤器，执行一个过滤器会有返回个枚举值，即DENY，NEUTRAL，ACCEPT其中之一。返回DENY，日志将立即被抛弃不再经过其他过滤器；返回NEUTRAL，有序列表里的下个过滤器过接着处理日志；返回ACCEPT，日志会被立即处理，不再经过剩余过滤器。 
过滤器被添加到<Appender> 中，为<Appender> 添加一个或多个过滤器后，可以用任意条件对日志进行过滤。<Appender> 有多个过滤器时，按照配置顺序执行。

#### 常用的过滤器：

1. LevelFilter： 级别过滤器，根据日志级别进行过滤。如果日志级别等于配置级别，过滤器会根据onMath 和 onMismatch接收或拒绝日志。有以下子节点： 

<level>:设置过滤级别 
<onMatch>:用于配置符合过滤条件的操作 
<onMismatch>:用于配置不符合过滤条件的操作

2. ThresholdFilter： 临界值过滤器，过滤掉低于指定临界值的日志。当日志级别等于或高于临界值时，过滤器返回NEUTRAL；当日志级别低于临界值时，日志会被拒绝。 

例如：过滤掉所有低于INFO级别的日志。

3. EvaluatorFilter： 求值过滤器，评估、鉴别日志是否符合指定条件。有一下子节点： 

<evaluator>: 
鉴别器，常用的鉴别器是JaninoEventEvaluato，也是默认的鉴别器，它以任意的Java布尔值表达式作为求值条件，求值条件在配置文件解释过成功被动态编译，布尔值表达式返回true就表示符合过滤条件。evaluator有个子标签<expression>，用于配置求值条件。 

求值表达式作用于当前日志，logback向求值表达式暴露日志的各种字段：
Name	Type	Description
event	LoggingEvent	与记录请求相关联的原始记录事件，下面所有变量都来自event

例如，event.getMessage()返回下面”message”相同的字符串

message	String	日志的原始消息

例如，设有logger mylogger，”name”的值是”AUB”，对于 mylogger.info(“Hello {}”,name); “Hello {}”就是原始消息。
formatedMessage	String	日志被各式话的消息，

例如，设有logger mylogger，”name”的值是”AUB”，对于 mylogger.info(“Hello {}”,name); “Hello Aub”就是格式化后的消息。
logger	String	logger 名。

loggerContext	LoggerContextVO	日志所属的logger上下文。

level	int	级别对应的整数值，所以 level > INFO 是正确的表达式。

timeStamp	long	创建日志的时间戳。

marker	Marker	与日志请求相关联的Marker对象，注意“Marker”有可能为null，所以你要确保它不能是null。

mdc	Map	包含创建日志期间的MDC所有值得map。访问方法是：mdc.get(“myKey”) 。

mdc.get()返回的是Object不是String，要想调用String的方法就要强转

例如，((String) mdc.get(“k”)).contains(“val”) .MDC可能为null，调用时注意。

throwable	java.lang.Throwable	如果没有异常与日志关联”throwable” 变量为 null. 不幸的是， “throwable” 不能被序列化。在远程系统上永远为null，对于与位置无关的表达式请使用下面的变量throwableProxy

throwableProxy	IThrowableProxy	与日志事件关联的异常代理。如果没有异常与日志事件关联，则变量”throwableProxy” 为 null. 当异常被关联到日志事件时，”throwableProxy” 在远程系统上不会为null

<onMatch>:用于配置符合过滤条件的操作 

<onMismatch>:用于配置不符合过滤条件的操作 

例如：过滤掉所有日志消息中不包含“billing”字符串的日志。

#### 参考来源： 
1. http://www.importnew.com/7450.html#comment-204549 
2. http://www.tuicool.com/articles/6VRnui 
3. http://blog.csdn.net/linwei_1029/article/details/8844939 
4. http://blog.csdn.net/xuanjiewu/article/details/7587586 
5. http://blog.csdn.net/haidage/article/details/6794540
