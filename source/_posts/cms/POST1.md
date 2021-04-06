---
title: CMS源码解读
tags:
  - cms
  - java
copyright: true
category: cms
abbrlink: 63673
date: 2017-12-06 21:34:36
---
![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0029.jpg)

CMS是"Content Management System"的缩写，意为"内容管理系统"。 内容管理系统是企业信息化建设和电子政务的新宠，也是一个相对较新的市场。对于内容管理，业界还没有一个统一的定义，不同的机构有不同的理解。
<!--more-->
· 基于java技术开发，继承其强大、稳定、安全、高效、跨平台等多方面的优点
· 采用SpringMVC3+Spring3+Hibernate3+Freemarker主流技术架构
· 懂html就能建站，提供最便利、合理的使用方式
· 强大、灵活的标签，用户自定义显示内容和显示方式
· 在设计上自身预先做了搜索引擎优化,增强对搜索引擎的友好性
· 完全生成全站静态页面,可自定义路径结构，全面提高页面访问速度
· 轻松建设大规模网站，可通过次级域名建立子站群，各子站后台管理权限分离，全站实现单点登录

## jeeCms源码安装时出现的问题及解决

### 装MySQL数据库装不上

因为以前本机上装过MySQL,而在卸载时有文件残留，故MySQL会安装失败，失败的表现有两种：一个是安装时未响应，一个是未弹出配置MySQL的界面。

#### 解决办法：

1. 你可以安装MySQL的时候在这一步时它默认的服务名是“MySQL” 只需要把这个名字改了就可以了。
2. ​
- [x] 卸载MySQL  
- [x] 删除安装目录及数据存放目录  
- [x] 在注册表(regedit)查询mysql，全部删除  
- [x] 在c盘查询MySQL，全部删除  
- [x] 重新安装就好了
    **注意的是注册表**
> cmd -> regedit，删除以下目录下的MySQL目录
> 　　 HKEY_LOCAL_MACHINE\SYSTEM\\\Eventlog\Application\MySQL 目录
> 　　 HKEY_LOCAL_MACHINE\SYSTEM\ControlSet002\Services\Eventlog\Application\MySQL 目录
> 　　 HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Eventlog\Application\MySQL\www.xuanzequan.com\目录
> 　　 HKEY_LOCAL_MACHINE\SYSTEM\CurrentControl001t\Services\MYSQL 目录
> 　　 HKEY_LOCAL_MACHINE\SYSTEM\CurrentControl002\Services\MYSQL 目录
> 　　 HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\MYSQL 目录
> 　　 删除C:\Documents and Settings\All Users\Application Data\MySQL 目录

### 数据库连接不上的问题

**错误提示**： An attempt by a client to checkout a Connection has timed out.

#### 原因：
- [x] 配置文件中目标数据库的名字错误 （区分大小写）
- [x] 配置文件中的目标数据库用户名和密码错误。
- [x] 缺少数据库的驱动jar包（如mysql-connector-5.1.8.jar 包）。缺这个包的原因是在解瘊MySQL连接不上的问题时，会在C盘中查找所有“mysql”的文件。而你的jeeCms源码包正好又放在桌面上，于是你会把所有带mySQL的文件删除，进而误删了源码包中的mySQL连接包（mysql-connector-5.1.8.jar）。当你在把页面上的源码包放到项目中时就少了这个包，故连接不上。

#### 解决办法：删除除mysql安装残留信息时一定要谨慎再谨慎

### src包中报错的问题：

**错误提示**：所有带HttpServletRequest或HttpServletResponse的类全部显示错误

#### 原因：没有找到servlet包。因为你安装源码包时，没有配置tomcat环境。

#### 解决办法：
安装源码包时，先在eclipse中配置tomcat环境，再建动态web项目，然后再在web项目中覆盖src和webContent文件夹。

## 源码解析之一 (web.xml)


```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app id="WebApp_ID" version="2.4" xmlns="http://java.sun.com/xml/ns/j2ee" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee http://java.sun.com/xml/ns/j2ee/web-app_2_4.xsd">
	<display-name>JeeCmsV7</display-name>
	<context-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>
			<!-- 数据库相关配置并加载了hbm.xml文件 -->
			/WEB-INF/config/application-context.xml
			<!-- 数据缓存相关配置 -->
			/WEB-INF/config/cache-context.xml
			<!-- 验证码相关配置 --> 
			/WEB-INF/config/captcha-context.xml
			 <!-- 图片，密码，文件上传等配置 --> 
			/WEB-INF/config/jeecms/jeecore-context.xml
			<!-- spring bean相关配置，如dao层的配置、图片处理，密码加密 -->
			/WEB-INF/config/jeecms/jeecms-context.xml
			<!-- 配置权限管理shiro-context.xml框架 -->
			/WEB-INF/config/shiro-context.xml
			<!-- 配置其它插件文件，暂时没有 -->
			/WEB-INF/config/plug/**/*-context.xml
			<!-- 配置定时任务 -->
			/WEB-INF/config/quartz-task.xml
		</param-value>
	</context-param>
	<!-- 打印执行时间与访问路径过滤器 -->
	<filter>
		<filter-name>processTime</filter-name>
		<filter-class>com.jeecms.common.web.ProcessTimeFilter</filter-class>
	</filter>
	<!-- 编码设置 -->  
	<filter>
		<filter-name>encoding</filter-name>
		<filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
		<init-param>
			<param-name>encoding</param-name>
			<param-value>UTF-8</param-value>
		</init-param>
	</filter>
	<!-- 解决Hibernate session延迟加载的问题 --> 
	<filter>
		<filter-name>osivFilter</filter-name>
		<filter-class>org.springframework.orm.hibernate3.support.OpenSessionInViewFilter</filter-class>
	</filter>
	<filter>
		<filter-name>shiroFilter</filter-name>
		<filter-class>org.springframework.web.filter.DelegatingFilterProxy</filter-class>
		<init-param>
			<param-name>targetFilterLifecycle</param-name>
			<param-value>true</param-value>
		</init-param>
	</filter>
	<!--@分隔-->
	<filter>
		<filter-name>XssFilter</filter-name>
		<filter-class>com.jeecms.common.web.XssFilter</filter-class>
		<init-param>
			<param-name>excludeUrls</param-name>
			<param-value>/member/contribute@/jeeadmin/jeecms@/flow_statistic</param-value>
    	</init-param>
    	<init-param>
			<param-name>SplitChar</param-name>
			<param-value>@</param-value>
    	</init-param>
    	<init-param>
			<param-name>FilterChar</param-name>
			<param-value>'@"@\@#@:@%@></param-value>
    	</init-param>
		<init-param>
			<param-name>ReplaceChar</param-name>
			<param-value>‘@“@＼@＃@：@％@＞</param-value>
    	</init-param>
	</filter>
	<filter-mapping>
		<filter-name>processTime</filter-name>
		<url-pattern>*.do</url-pattern>
	</filter-mapping>
	<filter-mapping>
		<filter-name>encoding</filter-name>
		<url-pattern>*.do</url-pattern>
	</filter-mapping>
	<filter-mapping>
		<filter-name>osivFilter</filter-name>
		<url-pattern>*.do</url-pattern>
	</filter-mapping>
	<filter-mapping>
		<filter-name>processTime</filter-name>
		<url-pattern>*.jspx</url-pattern>
	</filter-mapping>
	<filter-mapping>
		<filter-name>encoding</filter-name>
		<url-pattern>*.jspx</url-pattern>
	</filter-mapping>
	<filter-mapping>
		<filter-name>osivFilter</filter-name>
		<url-pattern>*.jspx</url-pattern>
	</filter-mapping>
	<filter-mapping>
		<filter-name>processTime</filter-name>
		<url-pattern>*.jhtml</url-pattern>
	</filter-mapping>
	<filter-mapping>
		<filter-name>encoding</filter-name>
		<url-pattern>*.jhtml</url-pattern>
	</filter-mapping>
	<filter-mapping>
		<filter-name>osivFilter</filter-name>
		<url-pattern>*.jhtml</url-pattern>
	</filter-mapping>
	<filter-mapping>
		<filter-name>processTime</filter-name>
		<url-pattern>*.htm</url-pattern>
	</filter-mapping>
	<filter-mapping>
		<filter-name>encoding</filter-name>
		<url-pattern>*.htm</url-pattern>
	</filter-mapping>
	<filter-mapping>
		<filter-name>osivFilter</filter-name>
		<url-pattern>*.htm</url-pattern>
	</filter-mapping>
	<filter-mapping>
		<filter-name>processTime</filter-name>
		<url-pattern>*.jsp</url-pattern>
	</filter-mapping>
	<filter-mapping>
		<filter-name>encoding</filter-name>
		<url-pattern>*.jsp</url-pattern>
	</filter-mapping>
	<filter-mapping>
		<filter-name>osivFilter</filter-name>
		<url-pattern>*.jsp</url-pattern>
	</filter-mapping>
	<filter-mapping>
		<filter-name>osivFilter</filter-name>
		<url-pattern>/</url-pattern>
	</filter-mapping>
	<filter-mapping> 
	   <filter-name>shiroFilter</filter-name> 
	   <url-pattern>/*</url-pattern> 
 	</filter-mapping>
	<filter-mapping>
		<filter-name>XssFilter</filter-name>
		<url-pattern>/*</url-pattern>
	</filter-mapping>
	        <!--  
            后台控制  
            DispatcherServlet是前端控制器设计模式的实现，提供Spring Web MVC的集中访问点，  
            而且负责职责的分派，而且与Spring IoC容器无缝集成，从而可以获得Spring的所有好处  
            DispatcherServlet主要用作职责调度工作，本身主要用于控制流程，主要职责如下：  
            1、文件上传解析，如果请求类型是multipart将通过MultipartResolver进行文件上传解析；  
            2、通过HandlerMapping，将请求映射到处理器（返回一个HandlerExecutionChain，它包括一个处理器、多个HandlerInterceptor拦截器）；  
            3、通过HandlerAdapter支持多种类型的处理器(HandlerExecutionChain中的处理器)；  
            4、通过ViewResolver解析逻辑视图名到具体视图实现；  
            5、本地化解析；  
            6、渲染具体的视图等；  
            7、如果执行过程中遇到异常将交给HandlerExceptionResolver来解析。  
         --> 
	<servlet>
		<servlet-name>JeeCmsAdmin</servlet-name>
		<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
		<init-param>
			<param-name>contextConfigLocation</param-name>
			<param-value>
				/WEB-INF/config/jeecms-servlet-admin.xml
				/WEB-INF/config/plug/**/*-servlet-admin-action.xml
			</param-value>
		</init-param>
		<load-on-startup>1</load-on-startup>
	</servlet>
	<!-- 前台控制 --> 
	<servlet>
		<servlet-name>JeeCmsFront</servlet-name>
		<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
		<init-param>
			<param-name>contextConfigLocation</param-name>
			<param-value>
				/WEB-INF/config/jeecms-servlet-front.xml
				/WEB-INF/config/plug/**/*-servlet-front-action.xml
			</param-value>
		</init-param>
		<load-on-startup>2</load-on-startup>
	</servlet>
	<servlet>
		<servlet-name>Jcaptcha</servlet-name>
		<servlet-class>com.jeecms.common.captcha.JcaptchaServlet</servlet-class>
	</servlet>
	<!-- 验证码图片 --> 
	<servlet-mapping>
		<servlet-name>Jcaptcha</servlet-name>
		<url-pattern>/captcha.svl</url-pattern>
	</servlet-mapping>
	<servlet>
		<servlet-name>DbFile</servlet-name>
		<servlet-class>com.jeecms.core.action.front.DbFileServlet</servlet-class>
	</servlet>
	<servlet>
		<servlet-name>SnapScreen</servlet-name>
		<servlet-class>com.jeecms.core.action.front.SnapScreenServlet</servlet-class>
	</servlet>
	<servlet-mapping>
		<servlet-name>SnapScreen</servlet-name>
		<url-pattern>/snapscreen.svl</url-pattern>
	</servlet-mapping>
	<servlet-mapping>
		<servlet-name>JeeCmsAdmin</servlet-name>
		<url-pattern>/jeeadmin/jeecms/*</url-pattern>
	</servlet-mapping>
	<servlet-mapping>
		<servlet-name>JeeCmsFront</servlet-name>
		<url-pattern>*.jhtml</url-pattern>
	</servlet-mapping>
	<servlet-mapping>
		<servlet-name>JeeCmsFront</servlet-name>
		<url-pattern>*.jspx</url-pattern>
	</servlet-mapping>
	<servlet-mapping>
		<servlet-name>JeeCmsFront</servlet-name>
		<url-pattern>*.jsp</url-pattern>
	</servlet-mapping>
	<servlet-mapping>
		<servlet-name>JeeCmsFront</servlet-name>
		<url-pattern>*.htm</url-pattern>
	</servlet-mapping>
	<servlet-mapping>
		<servlet-name>DbFile</servlet-name>
		<url-pattern>/dbfile.svl</url-pattern>
	</servlet-mapping>
	        <!--   
            ContextLoaderListener的作用就是启动Web容器时，自动装配ApplicationContext的配置信息。  
            因为它实现了ServletContextListener这个接口，在web.xml配置这个监听器，  
            启动容器时，就会默认执行它实现的方法。  
         -->  
	<listener>
		<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
	</listener>
	      <!--   
            它是一个在web应用关闭的时候,清除JavaBeans Introspector的监听器.  
            在web.xml中注册这个listener.可以保证在web 应用关闭的时候释放与掉这个web 应用相关的class loader 和由它管理的类   
            防止内存泄露  
         --> 
	<listener>
		<listener-class>org.springframework.web.util.IntrospectorCleanupListener</listener-class>
	</listener>
	<session-config>
		<session-timeout>20</session-timeout>
	</session-config>
	<welcome-file-list>
		<welcome-file>index.html</welcome-file>
		<welcome-file>index.shtml</welcome-file>
		<welcome-file>index.jhtml</welcome-file>
	</welcome-file-list>
	<error-page>
		<error-code>403</error-code>
		<location>/WEB-INF/error/403.html</location>
	</error-page>
	<error-page>
		<error-code>404</error-code>
		<location>/404.html</location>
	</error-page>
	<error-page>
	  <error-code>500</error-code>
	  <location>/WEB-INF/error/500.html</location>
	</error-page>
	<mime-mapping>
		<extension>rar</extension>
		<mime-type>application/zip</mime-type>
	</mime-mapping>
	<mime-mapping>
		<extension>doc</extension>
		<mime-type>application/zip</mime-type>
	</mime-mapping>
	<mime-mapping>
		<extension>wps</extension>
		<mime-type>application/zip</mime-type>
	</mime-mapping>
	<mime-mapping>
		<extension>et</extension>
		<mime-type>application/zip</mime-type>
	</mime-mapping>
</web-app>
```
## 源码解析之二（application-context.xml）


```xml
<!--xml进入路径web.xml/application-context.xml-->
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:jee="http://www.springframework.org/schema/jee" xmlns:tx="http://www.springframework.org/schema/tx"
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-3.2.xsd
	http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-3.2.xsd
	http://www.springframework.org/schema/jee http://www.springframework.org/schema/jee/spring-jee-3.2.xsd
	http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-3.2.xsd"
	default-lazy-init="true">

	<!-- 加载数据库属性文件，以便动态获取 -->
	<bean id="propertyConfigurer" class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
		<property name="locations">
			<list>
				<value>/WEB-INF/config/jdbc.properties</value>
			</list>
		</property>
	</bean>
	<!-- 加载其它属性文件 -->
	<bean id="properties" class="org.springframework.beans.factory.config.PropertiesFactoryBean">
		<property name="locations">
			<list>
				<!-- 其中包话数据库连接，hbm文件和连接池信息 -->
				<value>/WEB-INF/config/jdbc.properties</value>
				<!-- 暂时未知 -->
				<value>/WEB-INF/config/jeecms/jeecms.properties</value>
				<!-- 暂时未知 -->
				<value>/WEB-INF/config/plug/**/*.properties</value>
			</list>
		</property>
		<qualifier value="main"/>
	</bean>
	<!-- 通过PropertyUtils包操作properties属性文件中的属性，以便动态获取 -->
	<bean id="propertyUtils" class="com.jeecms.common.util.PropertyUtils">
		<property name="properties" ref="properties"/>
	</bean>
	<!-- 根据已加载的数据库属性文件，连接数据库 -->
	<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
		<property name="driverClass" value="${jdbc.driverClassName}" />
		<property name="jdbcUrl" value="${jdbc.url}" />
		<property name="user" value="${jdbc.username}" />
		<property name="password" value="${jdbc.password}" />
		 <!--连接关闭时默认将所有未提交的操作回滚。Default: false -->   
		<property name="autoCommitOnClose" value="true"/>
		 <!--当连接池用完时客户端调用getConnection()后等待获取新连接的时间，  
           	 超时后将抛出 SQLException,如设为0则无限期等待。单位毫秒。Default: 0 -->  
		<property name="checkoutTimeout" value="${cpool.checkoutTimeout}"/>
		<!--初始化时获取三个连接，取值应在minPoolSize与maxPoolSize之间。Default: 3 --> 
		<property name="initialPoolSize" value="${cpool.minPoolSize}"/>
		<!--连接池中保留的最小连接数。-->  
		<property name="minPoolSize" value="${cpool.minPoolSize}"/>
		<!--连接池中保留的最大连接数。Default: 15 --> 
		<property name="maxPoolSize" value="${cpool.maxPoolSize}"/>
		 <!--最大空闲时间,maxIdleTime秒内未使用则连 接被丢弃。若为0则永不丢弃。Default: 0 -->
		<property name="maxIdleTime" value="${cpool.maxIdleTime}"/>
		<!--当连接池中的连接耗尽的时候c3p0一次同时获取的连接数。Default: 3 -->   
		<property name="acquireIncrement" value="${cpool.acquireIncrement}"/>
		 <!--  
            default : 0 单位 s  
            这个配置主要是为了减轻连接池的负载，比如连接池中连接数因为某次数据访问高峰导致创建了很多数据连接  
            但是后面的时间段需要的数据库连接数很少，则此时连接池完全没有必要维护那么多的连接，所以有必要将  
            断开丢弃掉一些连接来减轻负载，必须小于maxIdleTime。配置不为0，则会将连接池中的连接数量保持到minPoolSize。  
            为0则不处理。   
         -->  
		<property name="maxIdleTimeExcessConnections" value="${cpool.maxIdleTimeExcessConnections}"/>
	</bean>
	<!-- 设置数据库session工厂 -->
	<bean id="sessionFactory" class="org.springframework.orm.hibernate3.LocalSessionFactoryBean">
		<property name="dataSource" ref="dataSource"/>
		<!-- 动态地通过properties文件加载映射的hbm文件 -->
		<property name="mappingLocations" value="#{propertyUtils.getList('hibernate.hbm')}"/>	
		<!-- 配置hibernate属性 -->
		<property name="hibernateProperties">
			<value>
			<!-- 指定数据库方言 --> 
			hibernate.dialect=${hibernate.dialect}
			<!-- 输出所有SQL语句到控制台 -->  
			hibernate.show_sql=false
			<!-- 格式化输出sql语句 --> 
			hibernate.format_sql=false
			 <!--   
                这个配置意思是当你在Hibernate里面输入true的时候，Hibernate会转化为0插入数据库，  
                当你在Hibernate里面输入false的时候，Hibernate会转化为1插入数据库，  
             --> 
			hibernate.query.substitutions=true 1, false 0
			<!-- 指定每次提交SQL的数量。参数值越大，读数据库的次数越少，速度越快。 --> 
			hibernate.jdbc.batch_size=20
			 <!--   
                允许查询缓存,对经常使用的List查询方式，只有在使用查询缓存时，  
                才会从缓存中通过id去get缓存的值；查询缓存一般缓存查询语句和查询结果的id --> 
			hibernate.cache.use_query_cache=true
			</value>
		</property>
		<property name="entityInterceptor">   
			<ref local="treeInterceptor"/>
		</property>
		<property name="cacheProvider">
			<ref local="cacheProvider"/>
		</property>
		<property name="lobHandler">
			<ref bean="lobHandler" />
		</property>
	</bean>
	<!-- 处理 LOB 数据,CLOB 类型,BLOB 类型 --> 
	<bean id="lobHandler" class="org.springframework.jdbc.support.lob.DefaultLobHandler" lazy-init="true"/>
	<!-- 缓存 -->  
	<bean id="cacheProvider" class="com.jeecms.common.hibernate3.SpringEhCacheProvider">
		<property name="configLocation">
			<value>/WEB-INF/config/ehcache-hibernate.xml</value>
		</property>
		<property name="diskStoreLocation">
			<value>/WEB-INF/cache/hibernate</value>
		</property>
	</bean>
	<!-- 栏目等树形结构 -->
	<bean id="treeInterceptor" class="com.jeecms.common.hibernate3.TreeIntercptor"/>
	<!-- 定义事务管理器（声明式的事务） -->
	<bean id="transactionManager" class="org.springframework.orm.hibernate3.HibernateTransactionManager">
		<property name="sessionFactory" ref="sessionFactory" />
	</bean>
	<!-- 是隐式地向 Spring 容器注入 -->  
	<context:annotation-config/>
	<tx:annotation-driven transaction-manager="transactionManager" />
</beans>
```
## 源码解析之三（ehcache-hibernate.xml）


```xml
<!-- 其为hibernate 二级缓存的配置 xml进入路径web.xml/application-context.xml/ehcache-hibernate.xml -->

<?xml version="1.0" encoding="UTF-8"?>
<ehcache xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:noNamespaceSchemaLocation="http://ehcache.org/ehcache.xsd">
	<!-- 磁盘存储:将缓存中暂时不使用的对象,转移到硬盘,类似于Windows系统的虚拟内存 path:指定在硬盘上存储对象的路径 -->
	<!--<diskStore path="java.io.tmpdir/jeecms/hibernate"/> -->
	<!-- defaultCache:默认的缓存配置信息,如果不加特殊说明,则所有对象按照此配置项处理 maxElementsInMemory:设置了缓存的上限,最多存储多少个记录对象 
		eternal:代表对象是否永不过期 timeToIdleSeconds:最大的发呆时间 timeToLiveSeconds:最大的存活时间 overflowToDisk:是否允许对象被写入到磁盘 
		diskSpoolBufferSizeMB：磁盘缓冲区的大小 diskExpiryThreadIntervalSeconds:清理过期缓存120秒 -->
	<defaultCache maxElementsInMemory="10000" eternal="false"
		timeToIdleSeconds="120" timeToLiveSeconds="120" overflowToDisk="true"
		diskSpoolBufferSizeMB="30" maxElementsOnDisk="10000000"
		diskPersistent="false" diskExpiryThreadIntervalSeconds="120" />
	<!-- cache:为指定名称的对象进行缓存的特殊配置 name:指定对象的完整名 -->
	<cache name="org.hibernate.cache.StandardQueryCache"
		maxElementsInMemory="50" eternal="false" timeToIdleSeconds="3600"
		timeToLiveSeconds="7200" overflowToDisk="true" />
	<cache name="org.hibernate.cache.UpdateTimestampsCache"
		maxElementsInMemory="5000" eternal="true" overflowToDisk="true" />


	<cache name="com.jeecms.core.entity.Sys" maxElementsInMemory="20"
		eternal="false" timeToIdleSeconds="600" timeToLiveSeconds="7200"
		overflowToDisk="true" />
	<cache name="com.jeecms.core.entity.DbTpl" maxElementsInMemory="100"
		eternal="false" timeToIdleSeconds="600" timeToLiveSeconds="7200"
		overflowToDisk="true" />
	<cache name="com.jeecms.core.entity.Ftp" maxElementsInMemory="100"
		eternal="false" timeToIdleSeconds="600" timeToLiveSeconds="7200"
		overflowToDisk="true" />


	<cache name="com.jeecms.cms.entity.main.ContentExt"
		maxElementsInMemory="100" eternal="false" timeToIdleSeconds="600"
		timeToLiveSeconds="7200" overflowToDisk="true" />
	<cache name="com.jeecms.cms.entity.main.Content"
		maxElementsInMemory="100" eternal="false" timeToIdleSeconds="600"
		timeToLiveSeconds="7200" overflowToDisk="true" />
	<cache name="com.jeecms.cms.entity.main.ContentType"
		maxElementsInMemory="100" eternal="false" timeToIdleSeconds="600"
		timeToLiveSeconds="7200" overflowToDisk="true" />
	<cache name="com.jeecms.cms.entity.main.CmsUserSite"
		maxElementsInMemory="100" eternal="false" timeToIdleSeconds="600"
		timeToLiveSeconds="7200" overflowToDisk="true" />
	<cache name="com.jeecms.cms.entity.main.CmsUserExt"
		maxElementsInMemory="100" eternal="false" timeToIdleSeconds="600"
		timeToLiveSeconds="7200" overflowToDisk="true" />
	<cache name="com.jeecms.cms.entity.main.ChannelTxt"
		maxElementsInMemory="100" eternal="false" timeToIdleSeconds="600"
		timeToLiveSeconds="7200" overflowToDisk="true" />
	<cache name="com.jeecms.cms.entity.main.ContentTag"
		maxElementsInMemory="100" eternal="false" timeToIdleSeconds="600"
		timeToLiveSeconds="7200" overflowToDisk="true" />
	<cache name="com.jeecms.cms.entity.main.CmsUser"
		maxElementsInMemory="100" eternal="false" timeToIdleSeconds="600"
		timeToLiveSeconds="7200" overflowToDisk="true" />
	<cache name="com.jeecms.cms.entity.main.ContentTxt"
		maxElementsInMemory="100" eternal="false" timeToIdleSeconds="600"
		timeToLiveSeconds="7200" overflowToDisk="true" />
	<cache name="com.jeecms.cms.entity.main.Channel"
		maxElementsInMemory="100" eternal="false" timeToIdleSeconds="600"
		timeToLiveSeconds="7200" overflowToDisk="true" />
	<cache name="com.jeecms.cms.entity.main.CmsRole"
		maxElementsInMemory="100" eternal="false" timeToIdleSeconds="600"
		timeToLiveSeconds="7200" overflowToDisk="true" />
	<cache name="com.jeecms.cms.entity.main.CmsModel"
		maxElementsInMemory="100" eternal="false" timeToIdleSeconds="600"
		timeToLiveSeconds="7200" overflowToDisk="true" />
	<cache name="com.jeecms.cms.entity.main.CmsGroup"
		maxElementsInMemory="100" eternal="false" timeToIdleSeconds="600"
		timeToLiveSeconds="7200" overflowToDisk="true" />
	<cache name="com.jeecms.cms.entity.main.ContentCount"
		maxElementsInMemory="100" eternal="false" timeToIdleSeconds="600"
		timeToLiveSeconds="7200" overflowToDisk="true" />
	<cache name="com.jeecms.cms.entity.main.CmsSite"
		maxElementsInMemory="100" eternal="false" timeToIdleSeconds="600"
		timeToLiveSeconds="7200" overflowToDisk="true" />
	<cache name="com.jeecms.cms.entity.main.CmsSiteCompany"
		maxElementsInMemory="100" eternal="false" timeToIdleSeconds="600"
		timeToLiveSeconds="7200" overflowToDisk="true" />
	<cache name="com.jeecms.cms.entity.main.ChannelExt"
		maxElementsInMemory="100" eternal="false" timeToIdleSeconds="600"
		timeToLiveSeconds="7200" overflowToDisk="true" />
	<cache name="com.jeecms.cms.entity.main.ContentCheck"
		maxElementsInMemory="100" eternal="false" timeToIdleSeconds="600"
		timeToLiveSeconds="7200" overflowToDisk="true" />
	<cache name="com.jeecms.cms.entity.main.CmsConfig"
		maxElementsInMemory="100" eternal="false" timeToIdleSeconds="600"
		timeToLiveSeconds="7200" overflowToDisk="true" />
	<cache name="com.jeecms.cms.entity.main.Channel.viewGroups"
		maxElementsInMemory="100" eternal="false" timeToIdleSeconds="600"
		timeToLiveSeconds="7200" overflowToDisk="true" />
	<cache name="com.jeecms.cms.entity.main.CmsSite.attr"
		maxElementsInMemory="100" eternal="false" timeToIdleSeconds="600"
		timeToLiveSeconds="7200" overflowToDisk="true" />
	<cache name="com.jeecms.cms.entity.main.Content.attr"
		maxElementsInMemory="100" eternal="false" timeToIdleSeconds="600"
		timeToLiveSeconds="7200" overflowToDisk="true" />
	<cache name="com.jeecms.cms.entity.main.CmsGroup.contriChannels"
		maxElementsInMemory="100" eternal="false" timeToIdleSeconds="600"
		timeToLiveSeconds="7200" overflowToDisk="true" />
	<cache name="com.jeecms.cms.entity.main.CmsRole.perms"
		maxElementsInMemory="100" eternal="false" timeToIdleSeconds="600"
		timeToLiveSeconds="7200" overflowToDisk="true" />
	<cache name="com.jeecms.cms.entity.main.CmsUser.channels"
		maxElementsInMemory="100" eternal="false" timeToIdleSeconds="600"
		timeToLiveSeconds="7200" overflowToDisk="true" />
	<cache name="com.jeecms.cms.entity.main.Content.pictures"
		maxElementsInMemory="100" eternal="false" timeToIdleSeconds="600"
		timeToLiveSeconds="7200" overflowToDisk="true" />
	<cache name="com.jeecms.cms.entity.main.CmsSite.txt"
		maxElementsInMemory="100" eternal="false" timeToIdleSeconds="600"
		timeToLiveSeconds="7200" overflowToDisk="true" />
	<cache name="com.jeecms.cms.entity.main.Channel.child"
		maxElementsInMemory="100" eternal="false" timeToIdleSeconds="600"
		timeToLiveSeconds="7200" overflowToDisk="true" />
	<cache name="com.jeecms.cms.entity.main.CmsSite.cfg"
		maxElementsInMemory="100" eternal="false" timeToIdleSeconds="600"
		timeToLiveSeconds="7200" overflowToDisk="true" />
	<cache name="com.jeecms.cms.entity.main.Content.contentCheckSet"
		maxElementsInMemory="100" eternal="false" timeToIdleSeconds="600"
		timeToLiveSeconds="7200" overflowToDisk="true" />
	<cache name="com.jeecms.cms.entity.main.Channel.users"
		maxElementsInMemory="100" eternal="false" timeToIdleSeconds="600"
		timeToLiveSeconds="7200" overflowToDisk="true" />
	<cache name="com.jeecms.cms.entity.main.CmsUser.userExtSet"
		maxElementsInMemory="100" eternal="false" timeToIdleSeconds="600"
		timeToLiveSeconds="7200" overflowToDisk="true" />
	<cache name="com.jeecms.cms.entity.main.Channel.attr"
		maxElementsInMemory="100" eternal="false" timeToIdleSeconds="600"
		timeToLiveSeconds="7200" overflowToDisk="true" />
	<cache name="com.jeecms.cms.entity.main.Content.attachments"
		maxElementsInMemory="100" eternal="false" timeToIdleSeconds="600"
		timeToLiveSeconds="7200" overflowToDisk="true" />
	<cache name="com.jeecms.cms.entity.main.CmsGroup.viewChannels"
		maxElementsInMemory="100" eternal="false" timeToIdleSeconds="600"
		timeToLiveSeconds="7200" overflowToDisk="true" />
	<cache name="com.jeecms.cms.entity.main.Channel.contriGroups"
		maxElementsInMemory="100" eternal="false" timeToIdleSeconds="600"
		timeToLiveSeconds="7200" overflowToDisk="true" />
	<cache name="com.jeecms.cms.entity.main.Content.viewGroups"
		maxElementsInMemory="100" eternal="false" timeToIdleSeconds="600"
		timeToLiveSeconds="7200" overflowToDisk="true" />
	<cache name="com.jeecms.cms.entity.main.Content.tags"
		maxElementsInMemory="100" eternal="false" timeToIdleSeconds="600"
		timeToLiveSeconds="7200" overflowToDisk="true" />
	<cache name="com.jeecms.cms.entity.main.CmsConfig.attr"
		maxElementsInMemory="100" eternal="false" timeToIdleSeconds="600"
		timeToLiveSeconds="7200" overflowToDisk="true" />
	<cache name="com.jeecms.cms.entity.main.Content.contentTxtSet"
		maxElementsInMemory="100" eternal="false" timeToIdleSeconds="600"
		timeToLiveSeconds="7200" overflowToDisk="true" />
	<cache name="com.jeecms.cms.entity.main.Channel.channelTxtSet"
		maxElementsInMemory="100" eternal="false" timeToIdleSeconds="600"
		timeToLiveSeconds="7200" overflowToDisk="true" />


	<cache name="com.jeecms.cms.entity.assist.CmsVoteTopic.items"
		maxElementsInMemory="100" eternal="false" timeToIdleSeconds="600"
		timeToLiveSeconds="7200" overflowToDisk="true" />
	<cache name="com.jeecms.cms.entity.assist.CmsKeyword"
		maxElementsInMemory="100" eternal="false" timeToIdleSeconds="600"
		timeToLiveSeconds="7200" overflowToDisk="true" />
	<cache name="com.jeecms.cms.entity.assist.CmsCommentExt"
		maxElementsInMemory="100" eternal="false" timeToIdleSeconds="600"
		timeToLiveSeconds="7200" overflowToDisk="true" />
	<cache name="com.jeecms.cms.entity.assist.CmsGuestbookExt"
		maxElementsInMemory="100" eternal="false" timeToIdleSeconds="600"
		timeToLiveSeconds="7200" overflowToDisk="true" />
	<cache name="com.jeecms.cms.entity.assist.CmsSensitivity"
		maxElementsInMemory="100" eternal="false" timeToIdleSeconds="600"
		timeToLiveSeconds="7200" overflowToDisk="true" />
	<cache name="com.jeecms.cms.entity.assist.CmsVoteTopic"
		maxElementsInMemory="100" eternal="false" timeToIdleSeconds="600"
		timeToLiveSeconds="7200" overflowToDisk="true" />
	<cache name="com.jeecms.cms.entity.assist.CmsGuestbookCtg"
		maxElementsInMemory="100" eternal="false" timeToIdleSeconds="600"
		timeToLiveSeconds="7200" overflowToDisk="true" />
	<cache name="com.jeecms.cms.entity.assist.CmsVoteRecord"
		maxElementsInMemory="100" eternal="false" timeToIdleSeconds="600"
		timeToLiveSeconds="7200" overflowToDisk="true" />
	<cache name="com.jeecms.cms.entity.assist.CmsVoteItem"
		maxElementsInMemory="100" eternal="false" timeToIdleSeconds="600"
		timeToLiveSeconds="7200" overflowToDisk="true" />
	<cache name="com.jeecms.cms.entity.assist.CmsGuestbook"
		maxElementsInMemory="100" eternal="false" timeToIdleSeconds="600"
		timeToLiveSeconds="7200" overflowToDisk="true" />
	<cache name="com.jeecms.cms.entity.assist.CmsComment"
		maxElementsInMemory="100" eternal="false" timeToIdleSeconds="600"
		timeToLiveSeconds="7200" overflowToDisk="true" />
	<cache name="com.jeecms.cms.entity.assist.CmsJobApply"
		maxElementsInMemory="100" eternal="false" timeToIdleSeconds="600"
		timeToLiveSeconds="7200" overflowToDisk="true" />
```
## 源码解析之四（cache-context.xml）


```xml
<pre name="code" class="java"><?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-3.2.xsd"
	default-lazy-init="true">
	<!--缓存-->
	<bean id="cacheManager" class="com.jeecms.common.web.WebEhCacheManagerFacotryBean">
		<property name="configLocation">
			<value>/WEB-INF/config/ehcache-application.xml</value>
		</property>
		<property name="diskStoreLocation">
			<value>/WEB-INF/cache/application</value>
		</property>
	</bean>
	<!--SESSION缓存-->
	<bean id="ehSessionCache" class="org.springframework.cache.ehcache.EhCacheFactoryBean">
		<property name="cacheManager">
			<ref local="cacheManager"/>
		</property>
		<property name="cacheName">
			<value>com.jeecms.common.web.Session</value>
		</property>
		<qualifier value="session"/>
	</bean>
	<!--内容计数缓存-->
	<bean id="ehContentCountCache" class="org.springframework.cache.ehcache.EhCacheFactoryBean">
		<property name="cacheManager">
			<ref local="cacheManager"/>
		</property>
		<property name="cacheName">
			<value>com.jeecms.cms.front.ContentCount</value>
		</property>		
		<qualifier value="contentCount"/>
	</bean>
	<!--栏目计数缓存-->
	<bean id="ehChannelCountCache" class="org.springframework.cache.ehcache.EhCacheFactoryBean">
		<property name="cacheManager">
			<ref local="cacheManager"/>
		</property>
		<property name="cacheName">
			<value>com.jeecms.cms.front.ChannelCount</value>
		</property>		
		<qualifier value="channelCount"/>
	</bean>
	<!--微信token缓存-->
	<bean id="ehWeixinTokenCache" class="org.springframework.cache.ehcache.EhCacheFactoryBean">
		<property name="cacheManager">
			<ref local="cacheManager"/>
		</property>
		<property name="cacheName">
			<value>com.jeecms.cms.weixin.token</value>
		</property>		
		<qualifier value="tokenCache"/>
	</bean>
	
	<!--站点流量缓存-->
	<bean id="ehCmsSiteFlowCache" class="org.springframework.cache.ehcache.EhCacheFactoryBean">
		<property name="cacheManager">
			<ref local="cacheManager"/>
		</property>
		<property name="cacheName">
			<value>com.jeecms.cms.front.CmsSiteFlow</value>
		</property>		
		<qualifier value="cmsSiteFlow"/>
	</bean>
	<!--一次会话访问缓存-->
	<bean id="ehCmsSiteAccessCache" class="org.springframework.cache.ehcache.EhCacheFactoryBean">
		<property name="cacheManager">
			<ref local="cacheManager"/>
		</property>
		<property name="cacheName">
			<value>com.jeecms.cms.front.CmsAccess</value>
		</property>		
		<qualifier value="cmsAccessCache"/>
	</bean>
	<!--最新会话访问缓存-->
	<bean id="ehCmsSiteLastAccessCache" class="org.springframework.cache.ehcache.EhCacheFactoryBean">
		<property name="cacheManager">
			<ref local="cacheManager"/>
		</property>
		<property name="cacheName">
			<value>com.jeecms.cms.front.CmsLastAccess</value>
		</property>		
		<qualifier value="cmsLastAccessCache"/>
	</bean>
	<!--每次访问页面缓存-->
	<bean id="ehCmsSiteAccessPagesCache" class="org.springframework.cache.ehcache.EhCacheFactoryBean">
		<property name="cacheManager">
			<ref local="cacheManager"/>
		</property>
		<property name="cacheName">
			<value>com.jeecms.cms.front.CmsAccessPage</value>
		</property>		
		<qualifier value="cmsAccessPageCache"/>
	</bean>
	<!--pv总量计数缓存-->
	<bean id="ehPvTotalCache" class="org.springframework.cache.ehcache.EhCacheFactoryBean">
		<property name="cacheManager">
			<ref local="cacheManager"/>
		</property>
		<property name="cacheName">
			<value>com.jeecms.cms.front.CmsSitePvTotal</value>
		</property>		
		<qualifier value="cmsPvTotalCache"/>
	</bean>
	<!--访问者总量计数缓存-->
	<bean id="ehVisitorTotalCache" class="org.springframework.cache.ehcache.EhCacheFactoryBean">
		<property name="cacheManager">
			<ref local="cacheManager"/>
		</property>
		<property name="cacheName">
			<value>com.jeecms.cms.front.CmsSiteVisitorTotal</value>
		</property>		
		<qualifier value="cmsVisitorTotalCache"/>
	</bean>
		
	<!--搜索词汇缓存-->
	<bean id="ehSearchWordsCache" class="org.springframework.cache.ehcache.EhCacheFactoryBean">
		<property name="cacheManager">
			<ref local="cacheManager"/>
		</property>
		<property name="cacheName">
			<value>com.jeecms.cms.front.CmsSearchWords</value>
		</property>		
		<qualifier value="cmsSearchWords"/>
	</bean>
	
	<!--shiro缓存
	<bean id="ehShiroCache" class="org.springframework.cache.ehcache.EhCacheFactoryBean">
		<property name="cacheManager">
			<ref local="cacheManager"/>
		</property>
		<property name="cacheName">
			<value>com.jeecms.cms.shiro.shiroCache</value>
		</property>		
		<qualifier value="shiroCache"/>
	</bean>
	-->
</beans>
```
## 源码解析之五（Spring + FreeMarker的集成）
共四步

### 加载属性文件  application-context.xml下的jeecms.properties，会在第四步用到。

```xml
<web-app id="WebApp_ID" version="2.4" xmlns="http://java.sun.com/xml/ns/j2ee" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee http://java.sun.com/xml/ns/j2ee/web-app_2_4.xsd">  
    <display-name>JeeCmsV7</display-name>  
    <context-param>  
        <param-name>contextConfigLocation</param-name>  
        <param-value>  
            <!-- 数据库相关配置并加载了hbm.xml文件 (Spring)-->  
            /WEB-INF/config/application-context.xml  
            <!-- 数据缓存相关配置 -->  
            /WEB-INF/config/cache-context.xml  
            <!-- 验证码相关配置 -->   
            /WEB-INF/config/captcha-context.xml  
             <!-- 图片，密码，文件上传等配置 -->   
            /WEB-INF/config/jeecms/jeecore-context.xml  
            <!-- spring bean相关配置，如dao层的配置、图片处理，密码加密 -->  
            /WEB-INF/config/jeecms/jeecms-context.xml  
            <!-- 配置权限管理shiro-context.xml框架 -->  
            /WEB-INF/config/shiro-context.xml  
            <!-- 配置其它插件文件，暂时没有 -->  
            /WEB-INF/config/plug/**/*-context.xml  
            <!-- 配置定时任务 -->  
            /WEB-INF/config/quartz-task.xml  
        </param-value>  
    </context-param>  
...  
</web-app> 
```

### 前台配置：

#### 配置FreeMarkerConfigurer

```xml
<!-- 配置freemaker -->  
    <bean id="freemarkerConfig" class="org.springframework.web.servlet.view.freemarker.FreeMarkerConfigurer">  
        <!--获取标签配置-->  
        <property name="freemarkerVariables" value="#{propertyUtils.getBeanMap('directive.')}"/>  
        <property name="templateLoaderPath" value=""/>  
        <property name="freemarkerSettings">  
            <props>  
                <prop key="tag_syntax">auto_detect</prop>  
                <prop key="template_update_delay">5</prop>  
                <prop key="defaultEncoding">UTF-8</prop>  
                <prop key="url_escaping_charset">UTF-8</prop>  
                <prop key="locale">zh_CN</prop>  
                <prop key="boolean_format">true,false</prop>  
                <prop key="datetime_format">yyyy-MM-dd HH:mm:ss</prop>  
                <prop key="date_format">yyyy-MM-dd</prop>  
                <prop key="time_format">HH:mm:ss</prop>  
                <prop key="number_format">0.######</prop>  
                <prop key="whitespace_stripping">true</prop>  
                <!--空值处理<prop key="classic_compatible">true</prop>-->  
                <!--定义FreeMaker引入文件，index.ftl前缀为p,spring.ftl的前缀为s，这样在饮用ftl模版的宏定义时，可以简略使用-->  
                <prop key="auto_import">/WEB-INF/ftl/jeecms/index.ftl as p,/WEB-INF/ftl/spring.ftl as s</prop>  
            </props>  
        </property>  
    </bean>
```

#### 解析 FreeMarker 视图

```xml
<!-- 解析freemaker视图 -->  
    <bean id="freemarkerViewResolver" class="com.jeecms.common.web.springmvc.SimpleFreeMarkerViewResolver">  
        <property name="contentType" value="text/html; charset=UTF-8"/>  
        <!-- 请求属性不暴露给freemaker使用 -->  
        <property name="exposeRequestAttributes" value="false"/>  
        <!-- 会话属性不暴露给freemaker使用 -->  
        <property name="exposeSessionAttributes" value="false"/>  
        <!-- 使用宏 -->  
        <property name="exposeSpringMacroHelpers" value="true"/>  
    </bean>  
```
### 后台配置

#### 配置FreeMarkerConfigurer

```xml
<bean id="freemarkerConfig" class="org.springframework.web.servlet.view.freemarker.FreeMarkerConfigurer">  
        <property name="templateLoaderPath" value="/WEB-INF"/>  
        <property name="freemarkerVariables">  
            <map>  
                <!--在FCK编辑器中需要用到appBase，以确定connector路径。-->  
                <entry key="appBase" value="/jeeadmin/jeecms"/>  
                <!--后台管理权限控制-->  
                <entry key="cms_perm" value-ref="cms_perm"/>  
                <entry key="text_cut" value-ref="text_cut"/>  
                <entry key="html_cut" value-ref="html_cut"/>  
                <entry key="cms_content_list" value-ref="cms_content_list"/>  
                <entry key="cms_content_page" value-ref="cms_content_page"/>  
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
                <!--定义FreeMaker引入文件，index.ftl前缀为p,spring.ftl的前缀为s，这样在饮用ftl模版的宏定义时，可以简略使用-->  
                <prop key="auto_import">/ftl/jeecms/index.ftl as p,/ftl/spring.ftl as s</prop>  
            </props>  
        </property>  
    </bean> 
```

#### 解析 FreeMarker 视图

```xml
<!-- 视图解析器 -->  
    <bean id="freemarkerViewResolver" class="com.jeecms.common.web.springmvc.RichFreeMarkerViewResolver">  
        <!-- 前缀 -->  
        <property name="prefix" value="/jeecms_sys/"/>  
        <!-- 后缀 -->  
        <property name="suffix" value=".html"/>  
        <property name="contentType" value="text/html; charset=UTF-8"/>  
        <!-- 请求属性不暴露给freemaker使用 -->  
        <property name="exposeRequestAttributes" value="false"/>  
        <!-- 会话属性不暴露给freemaker使用 -->  
        <property name="exposeSessionAttributes" value="false"/>  
        <!-- 使用宏 -->  
        <property name="exposeSpringMacroHelpers" value="true"/>  
    </bean>  
```
### 注入freeMarkerConfigurer（jeecms-context.xml）

```xml
<bean id="staticPageSvc" class="com.jeecms.cms.staticpage.StaticPageSvcImpl">  
        <property name="tplMessageSource" ref="tplMessageSource"/>  
        <property name="freeMarkerConfigurer">  
            <bean class="org.springframework.web.servlet.view.freemarker.FreeMarkerConfigurer">  
                <!-- 从jeecms.properties中获取标签：directive.前缀的 -->  
                <property name="freemarkerVariables" value="#{propertyUtils.getBeanMap('directive.')}"/>  
                <property name="templateLoaderPath" value=""/>  
                <property name="freemarkerSettings">  
                    <props>  
                        <prop key="tag_syntax">auto_detect</prop>  
                        <prop key="template_update_delay">5</prop>  
                        <prop key="defaultEncoding">UTF-8</prop>  
                        <prop key="url_escaping_charset">UTF-8</prop>  
                        <prop key="locale">zh_CN</prop>  
                        <prop key="boolean_format">true,false</prop>  
                        <prop key="datetime_format">yyyy-MM-dd HH:mm:ss</prop>  
                        <prop key="date_format">yyyy-MM-dd</prop>  
                        <prop key="time_format">HH:mm:ss</prop>  
                        <prop key="number_format">0.######</prop>  
                        <prop key="whitespace_stripping">true</prop>  
                        <prop key="auto_import">/WEB-INF/ftl/jeecms/index.ftl as p,/WEB-INF/ftl/spring.ftl as s</prop>  
                    </props>  
                </property>  
            </bean>  
        </property>  
    </bean>  
```
## 源码解析之六（前台页面初始化）

### 项目启动，初始化配置文件

```xml
<context-param>  
        <param-name>contextConfigLocation</param-name>  
        <param-value>  
            <!-- 数据库相关配置并加载了hbm.xml文件 (Spring)-->  
            /WEB-INF/config/application-context.xml  
            <!-- 数据缓存相关配置 -->  
            /WEB-INF/config/cache-context.xml  
            <!-- 验证码相关配置 -->   
            /WEB-INF/config/captcha-context.xml  
             <!-- 图片，密码，文件上传等配置 -->   
            /WEB-INF/config/jeecms/jeecore-context.xml  
            <!-- spring bean相关配置，如dao层的配置、图片处理，密码加密 ，freemaker的使用-->  
            /WEB-INF/config/jeecms/jeecms-context.xml  
            <!-- 配置权限管理shiro-context.xml框架 -->  
            /WEB-INF/config/shiro-context.xml  
            <!-- 配置其它插件文件，暂时没有 -->  
            /WEB-INF/config/plug/**/*-context.xml  
            <!-- 配置定时任务 -->  
            /WEB-INF/config/quartz-task.xml  
        </param-value>  
    </context-param>  
```
### 浏览器输入http://localhost:8070/jeeCms/

```xml
<bean id="dynamicAct" class="com.jeecms.cms.action.front.DynamicPageAct"/>
```

### 通过过滤器进入

```java
/**  
 * TOMCAT的默认路径  
 *   
 * @param request  
 * @param model  
 * @return  
 */  
@RequestMapping(value = "/", method = RequestMethod.GET)  
public String index(HttpServletRequest request,HttpServletResponse response, ModelMap model) {  
    CmsSite site = CmsUtils.getSite(request);  
    FrontUtils.frontData(request, model, site);  
    //带有其他路径则是非法请求  
    String uri=URLHelper.getURI(request);  
    if(StringUtils.isNotBlank(uri)&&!uri.equals("/")){  
        return FrontUtils.pageNotFound(request, response, model);  
    }  
    //使用静态首页而且静态首页存在  
    if(site.getStaticIndex()&&new File(realPathResolver.get(site.getStaticDir()+INDEX)).exists()){  
        return FrontUtils.getTplPath("", site.getStaticDir(), INDEX);  
    }else{  
        return site.getTplIndexOrDef();  
    }  
}
```
### 返回一个url并 进入主页。

```xml
<!-- 当输入localhost:8080/jeeCms时，会先找第一个欢迎页，没有的话找第二个，依次类推，如果存在，就不找了 -->  
    <welcome-file-list>  
        <welcome-file>index.html</welcome-file>  
        <welcome-file>index.shtml</welcome-file>  
        <welcome-file>index.jhtml</welcome-file>  
    </welcome-file-list>
```
### 通过标签进入指定的类，并调用期execute()方法（以前台@cms_content_list为例）
#### 前台标签：


```html
<div class="cl">  
     <ul class="slideshow" id="slidesImgs">  
         [@<strong>cms_content_list</strong> count='5' orderBy='4' typeId='3' titLen='18' channelOption='1' channelId='1']  [#list tag_list as a]  
              <li>  
                                        <a href="${a.url}" target="_blank">  
                                        <img src="${a.typeImg!site.defImg}" alt="${a.title}" width="100%" />  
                                        </a>  
                                        <span class="title">  
                                        [@text_cut s=a.title len=titLen /]  
                                        </span>  
                                    </li>  
                                    [/#list]  
                                    [/@cms_content_list]  
                                    </ul>  
                                </div>
```

#### 在web.xml--->jeecms-contextxml 配置中找到相关的处理类

```xml
<bean id="cms_content_list" class="com.jeecms.cms.action.directive.ContentListDirective"/>
```
#### 调用其execute（）方法对其进行处理


```java
/*   
    *  模板名称  
     */  
    public static final String TPL_NAME = "content_list";  
  
    /** 
     * 输入参数，文章ID。允许多个文章ID，用","分开。排斥其他所有筛选参数。 
     */  
    public static final String PARAM_IDS = "ids";  
  
    @SuppressWarnings("unchecked")  
    public void execute(Environment env, Map params, TemplateModel[] loopVars,  
            TemplateDirectiveBody body) throws TemplateException, IOException {  
        //获取站点  
        CmsSite site = FrontUtils.getSite(env);  
        //获取内容列表，可通过此处进行更改，获取自己数据库中的数据  
        List<Content> list = getList(params, env);  
          
        Map<String, TemplateModel> paramWrap = new HashMap<String, TemplateModel>(params);  
        //OUT_LIST 的值是tag_list，在DirectiveUtils中声名，将内容列表放入其中  
        paramWrap.put(OUT_LIST, DEFAULT_WRAPPER.wrap(list));  
        //将params的值复制上到ariable中  
        Map<String, TemplateModel> origMap = DirectiveUtils.addParamsToVariable(env, paramWrap);  
        //获取的是参数PARAM_TPL，是否调用模板及调用的模板类型  
        InvokeType type = DirectiveUtils.getInvokeType(params);  
        //获取模板的传入参数，列表样式，根据不两只的参数获取不同的样式列表  
        String listStyle = DirectiveUtils.getString(PARAM_STYLE_LIST, params);  
        if (InvokeType.sysDefined == type) {  
            if (StringUtils.isBlank(listStyle)) {  
                throw new ParamsRequiredException(PARAM_STYLE_LIST);  
            }  
            //列表样式模板  
            env.include(TPL_STYLE_LIST + listStyle + TPL_SUFFIX, UTF8, true);  
        } else if (InvokeType.userDefined == type) {  
            if (StringUtils.isBlank(listStyle)) {  
                throw new ParamsRequiredException(PARAM_STYLE_LIST);  
            }  
            //列表样式模板的路径  
            FrontUtils.includeTpl(TPL_STYLE_LIST, site, env);  
        } else if (InvokeType.custom == type) {  
            //这个模板是自己声明的，即content_list.html,如果采用自定义模板的话，页面中可以只写上标签，并添加上标签内需要的几个参数不需要写标签的内容，会去自动调用模板中的标签体。  
            FrontUtils.includeTpl(TPL_NAME, site, params, env);  
        } else if (InvokeType.body == type) {  
            body.render(env.getOut());  
        } else {  
            throw new RuntimeException("invoke type not handled: " + type);  
        }  
        //将variable中的params值移除  
        DirectiveUtils.removeParamsFromVariable(env, paramWrap, origMap);  
    }  
  
    @SuppressWarnings("unchecked")  
    protected List<Content> getList(Map<String, TemplateModel> params,  
            Environment env) throws TemplateException {  
        Integer[] ids = DirectiveUtils.getIntArray(PARAM_IDS, params);  
        if (ids != ) {  
            //根据ID数组获取文章列表  
            return contentMng.getListByIdsForTag(ids, getOrderBy(params));  
        } else {  
            return (List<Content>) super.getData(params, env);  
        }  
    }
```

### 前台通过标签循环取出数据


```html
<ul class="slideshow" id="slidesImgs">  
                                    [@cms_content_list count='5' orderBy='4' typeId='3' titLen='18' channelOption='1' channelId='1']  
                                    <!-- 将OUT_LIST 即tag_list中的值遍历出来 -->  
                                    [#list tag_list as a]  
                                    <li>  
                                        <a href="${a.url}" target="_blank">  
                                        <img src="${a.typeImg!site.defImg}" alt="${a.title}" width="100%" />  
                                        </a>  
                                        <span class="title">  
                                        [@text_cut s=a.title len=titLen /]  
                                        </span>  
                                    </li>  
                                    [/#list]  
                                    [/@cms_content_list]  
                                 </ul>
```
## 源码解析之七（前台页面初始化是request赋值）

### 问题：
当输入http://localhost:8070/jeeCms/时，通过拦截器“/”后台时，request 中包含了很多值是哪里来的？

### 回答：

#### 加载web.xml

```xml
<!-- 前台控制 -->   
    <servlet>  
        <servlet-name>JeeCmsFront</servlet-name>  
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>  
        <init-param>  
            <param-name>contextConfigLocation</param-name>  
            <param-value>  
                /WEB-INF/config/jeecms-servlet-front.xml  
                /WEB-INF/config/plug/**/*-servlet-front-action.xml  
            </param-value>  
        </init-param>  
        <load-on-startup>2</load-on-startup>  
    </servlet>
```
#### 其初使化方法中 进入jeecms-servlet-front.xml，初使化：


```xml
<bean id="frontContextInterceptor" class="com.jeecms.cms.web.FrontContextInterceptor"/>
```

#### 为reques和中放入值


```java
/** 
 * CMS上下文信息拦截器 
 *  
 * 包括登录信息、权限信息、站点信息 
 */  
public class FrontContextInterceptor extends HandlerInterceptorAdapter {  
    public static final String SITE_COOKIE = "_site_id_cookie";  
    @Override  
    public boolean preHandle(HttpServletRequest request,  
            HttpServletResponse response, Object handler)  
            throws ServletException {  
        CmsSite site = ;  
        List<CmsSite> list = cmsSiteMng.getListFromCache();  
        int size = list.size();  
        if (size == 0) {  
            throw new RuntimeException("no site record in database!");  
        } else if (size == 1) {  
            site = list.get(0);  
        } else {  
            String server = request.getServerName();  
            String alias, redirect;  
            for (CmsSite s : list) {  
                // 检查域名  
                if (s.getDomain().equals(server)) {  
                    site = s;  
                    break;  
                }  
                // 检查域名别名  
                alias = s.getDomainAlias();  
                if (!StringUtils.isBlank(alias)) {  
                    for (String a : StringUtils.split(alias, ',')) {  
                        if (a.equals(server)) {  
                            site = s;  
                            break;  
                        }  
                    }  
                }  
                // 检查重定向  
                redirect = s.getDomainRedirect();  
                if (!StringUtils.isBlank(redirect)) {  
                    for (String r : StringUtils.split(redirect, ',')) {  
                        if (r.equals(server)) {  
                            try {  
                                response.sendRedirect(s.getUrl());  
                            } catch (IOException e) {  
                                throw new RuntimeException(e);  
                            }  
                            return false;  
                        }  
                    }  
                }  
            }  
            if (site == ) {  
                throw new SiteNotFoundException(server);  
            }  
        }  
          
        <span style="background-color: rgb(204, 153, 51);">CmsUtils.setSite(request, site);</span>  
        CmsThreadVariable.setSite(site);  
        Subject subject = SecurityUtils.getSubject();  
        if (subject.isAuthenticated()|| subject.isRemembered()) {  
            String username =  (String) subject.getPrincipal();  
            CmsUser user = cmsUserMng.findByUsername(username);  
            CmsUtils.setUser(request, user);  
            // Site加入线程变量  
            CmsThreadVariable.setUser(user);  
        }  
        createJsessionId(request, response, site);  
        return true;  
    }
```
## 源码解析之八（jeeCms整合webservice）
### 第一步：在jeeCms .添加jar包
### 第二步：增加webservice相关配置。

web.xml

```xml
<context-param>  
    <param-name>contextConfigLocation</param-name>  
    <param-value>  
        <!-- 数据库相关配置并加载了hbm.xml文件 (Spring)-->  
        /WEB-INF/config/application-context.xml  
        <!-- 数据缓存相关配置 -->  
        /WEB-INF/config/cache-context.xml  
        <!-- 验证码相关配置 -->   
        /WEB-INF/config/captcha-context.xml  
         <!-- 图片，密码，文件上传等配置 -->   
        /WEB-INF/config/jeecms/jeecore-context.xml  
        <!-- spring bean相关配置，如dao层的配置、图片处理，密码加密 ，freemaker的使用-->  
        /WEB-INF/config/jeecms/jeecms-context.xml  
        <!-- 配置权限管理shiro-context.xml框架 -->  
        /WEB-INF/config/shiro-context.xml  
        <!-- 配置其它插件文件，暂时没有 -->  
        /WEB-INF/config/plug/**/*-context.xml  
        <!-- 配置定时任务 -->  
        /WEB-INF/config/quartz-task.xml  
        <!-- 配置webservice -->  
      <span style="color:#ff6666;">      /WEB-INF/config/webservice/cxf-service.xml</span>  
    </param-value>  
</context-param>
```

```xml
<!-- CXF webservice -->  
   <servlet>    
    <servlet-name>CXFServlet</servlet-name>    
    <servlet-class>org.apache.cxf.transport.servlet.CXFServlet</servlet-class>    
<load-on-startup>2</load-on-startup>    
    </servlet>  
    <servlet-mapping>    
    <servlet-name>CXFServlet</servlet-name>    
    <url-pattern>/ws/*</url-pattern>    
    </servlet-mapping>
```
cxf-service.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>  
<beans xmlns="http://www.springframework.org/schema/beans"  
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  
    xmlns:tx="http://www.springframework.org/schema/tx"  
    xmlns:aop="http://www.springframework.org/schema/aop"  
    xmlns:jee="http://www.springframework.org/schema/jee"  
    xmlns:jaxws="http://cxf.apache.org/jaxws"  
    xmlns:http-conf="http://cxf.apache.org/transports/http/configuration"  
    xsi:schemaLocation="  
    http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd  
    http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd  
    http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd  
    http://www.springframework.org/schema/jee http://www.springframework.org/schema/jee/spring-jee.xsd  
    http://cxf.apache.org/jaxws http://cxf.apache.org/schemas/jaxws.xsd  
    http://cxf.apache.org/transports/http/configuration http://cxf.apache.org/schemas/configuration/http-conf.xsd">      
      
    <http-conf:conduit name="*.http-conduit">       
        <http-conf:client ConnectionTimeout="60000" ReceiveTimeout="60000"/>      
    </http-conf:conduit>  
    <!-- 这两个xml文件来源cxf jar包 -->  
    <import resource="classpath:META-INF/cxf/cxf.xml" />  
    <import resource="classpath:META-INF/cxf/cxf-servlet.xml" />  
      
    <!-- 这个文件中用来配置webservice服务器 -->  
    <import resource="action-cxf.xml" />  
  
</beans>
```

action-cxf.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:tx="http://www.springframework.org/schema/tx"
	xmlns:aop="http://www.springframework.org/schema/aop" xmlns:jee="http://www.springframework.org/schema/jee"
	xmlns:jaxws="http://cxf.apache.org/jaxws"
	xsi:schemaLocation="
	http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
	http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd
	http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd
	http://www.springframework.org/schema/jee http://www.springframework.org/schema/jee/spring-jee.xsd
	http://cxf.apache.org/jaxws      
	http://cxf.apache.org/schemas/jaxws.xsd">
	<!-- 
	<jaxws:endpoint id="ActPromWebService" implementor="#bizActPromWebService" address="/ActPromWebService" />
	 -->
</beans>
```
### 第三步 创建webService 接口类，用来存放调用webService 的方法

```java
package com.jeecms.cms.action.directive;  
  
import javax.jws.WebMethod;  
import javax.jws.WebParam;  
import javax.jws.WebService;  
  
@WebService(targetNamespace="http://webservice.cus.biz.cbp.cj.com/")  
public interface countInfoInterfaceNpl {  
      
    public static final String SERVICE_NAME = "FindAllCustWebSerrvice";  
  
    /** 
     * 根据客户ID查询客户的详细信息 
     *  
     * @param traT 
     * @param custB 
     * @return 
     */  
    @WebMethod  
    String findByIdCustB(@WebParam(name = "partnerId") String partnerId, @WebParam(name = "traT") String traT, @WebParam(name = "custB") String custB,  
            @WebParam(name = "orderId") String orderId,@WebParam(name = "verifyCode") String verifyCode);  
  
}
```

### 第四步 java类中调用webservice 

```java
package com.jeecms.cms.action.directive;  
  
import static com.jeecms.common.web.freemarker.DirectiveUtils.OUT_BEAN;  
import static freemarker.template.ObjectWrapper.DEFAULT_WRAPPER;  
  
import java.io.IOException;  
import java.lang.reflect.InvocationTargetException;  
import java.lang.reflect.Method;  
import java.util.HashMap;  
import java.util.Map;  
  
import javax.jws.WebService;  
  
import org.apache.cxf.interceptor.LoggingInInterceptor;  
import org.apache.cxf.interceptor.LoggingOutInterceptor;  
import org.apache.cxf.jaxws.JaxWsProxyFactoryBean;  
  
import com.jeecms.common.web.freemarker.DirectiveUtils;  
  
import freemarker.core.Environment;  
import freemarker.template.TemplateDirectiveBody;  
import freemarker.template.TemplateDirectiveModel;  
import freemarker.template.TemplateException;  
import freemarker.template.TemplateModel;  
   
/** 
 * 总计信息 
 */  
public class CountInfoNpl implements TemplateDirectiveModel {  
  
    @SuppressWarnings("unchecked")  
    public void execute(Environment env, Map params, TemplateModel[] loopVars,  
            TemplateDirectiveBody body) throws TemplateException, IOException {  
        try {  
        int i=1;  
        if(i==1){  
             JaxWsProxyFactoryBean factory = new JaxWsProxyFactoryBean();  
             factory.getInInterceptors().add(new LoggingInInterceptor());  
             factory.getOutInterceptors().add(new LoggingOutInterceptor());  
             factory.setServiceClass(countInfoInterfaceNpl.class);//调用的接口类  
             factory.setAddress("http://127.0.0.1:8060/ws/FindAllCustWebSerrvice");  
             Object cInstance = factory.create();  
             Method invokeMethod = ;  
            //循环接口类的所有方法，并与实际调用的方法比较，若不存在，则给出错误信息  
             for(Method m : (countInfoInterfaceNpl.class).getDeclaredMethods()){  
                 if(!m.getName().equalsIgnoreCase("")){  
                     invokeMethod = m;  
                     break;  
                 }  
             }  
             if(invokeMethod == )  
                 throw new Exception("ERROR:method not found");  
            //为webService添加参数  
            Object[] params1 = new Object[5];  
            params1[0] = ;  
            params1[1] = "aaaa";  
            params1[2] = "bbnn";  
            params1[3] = ;  
            params1[4] = ;  
            //调用webService接口，并用CustNpl接收返回值  
            CustBNpl res = (CustBNpl) invokeMethod.invoke(cInstance, params1);  
            System.out.println("aaa");  
        }else{  
            Map<String,String> cmsVoteInfo=new HashMap<String,String>();  
            cmsVoteInfo.put("publicInfo", "加薪宝涨息");  
            cmsVoteInfo.put("totalCaptial","20000001");  
            cmsVoteInfo.put("goldCaptial","5555555");  
            cmsVoteInfo.put("getMoney","6666666");  
              
            Map<String, TemplateModel> paramWrap = new HashMap<String, TemplateModel>(  
                    params);  
            paramWrap.put(OUT_BEAN, DEFAULT_WRAPPER.wrap(cmsVoteInfo));  
            Map<String, TemplateModel> origMap = DirectiveUtils.addParamsToVariable(env, paramWrap);  
            body.render(env.getOut());  
            DirectiveUtils.removeParamsFromVariable(env, paramWrap, origMap);  
        }  
        } catch (Exception e) {  
            // TODO Auto-generated catch block  
            e.printStackTrace();  
        }  
          
    }  
}
```
