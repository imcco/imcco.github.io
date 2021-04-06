---
title: CAS Client集群环境的问题及解决方案 
tags: CAS
category: CAS
date: 2018-01-20 23:34:36
---
![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0092.jpg)

为了描述方便，假设有如下一个单点登录系统。一套CASServer，两套CAS Client系统。为了描述的方便，省略CAS Server调用用户系统完成登录，以及CASClient从用户系统读取用户详细信息的过程。
<!--more-->

# **1 单点登录的过程**

为了描述方便，假设有如下一个单点登录系统。一套CASServer，两套CAS Client系统。为了描述的方便，省略CAS Server调用用户系统完成登录，以及CASClient从用户系统读取用户详细信息的过程。

## **1.1 多应用情况下Session信息**

假定有两个CAS Client应用，一个CAS Server。应用的部署，可能在不同的服务器，也可能有不同的访问IP或域名，即使是同一个浏览器，在各个应用中的Session信息也是不相同的。

浏览器中，每个应用有一个独立的JSESSIONIDCookie。某一个应用，不可能读取到浏览器在其他应用中的Cookie信息。

假定用户首先访问CAS Client 01，系统提醒用户进行一次登录；然后用户访问CAS Client2，不会再提示登录而是直接登录成功。

## **1.2 第一次访问CAS Client 01**

用户打开浏览器后第一次访问，重定向到单点登录后，会提示用户输入账号密码登录。登录成功之后，再跳转回CAS Client。

## **1.3 第一次访问CAS Client 02**

当用户浏览器已经登录系统，切换到另一个CASClient时，跟第一次访问有所不同，因为已经登录成功，就不会再提醒输入账号密码登录了。

## **1.4 再次访问CAS Clients**

当用户已经访问过CAS Client后，当用户再次访问，系统不会再跳转到CAS Server做认证。

## **1.5 CASClient配置**

为了实现前述的单点登录过程，以Java WEB项目为例，需要在 web.xml 中进行相应的配置。（为了排版，没有填写Filter的完整class名，请自行查阅补充。）

```xml
<filter>
  <filter-name>CAS AuthenticationFilter</filter-name>
  <filter-class>*.AuthenticationFilter</filter-class>
</filter>
<filter>
  <filter-name>CAS Validation Filter</filter-name>
  <filter-class>*.Cas10TicketValidationFilter</filter-class>
</filter>
<filter>
  <filter-name>CAS HttpServletRequest WrapperFilter</filter-name>
  <filter-class>*.HttpServletRequestWrapperFilter</filter-class>
</filter>
<filter-mapping>
  <filter-name>CAS Validation Filter</filter-name>
  <url-pattern>/*</url-pattern>
</filter-mapping>
<filter-mapping>
  <filter-name>CAS AuthenticationFilter</filter-name>
  <url-pattern>/*</url-pattern>
</filter-mapping>
<filter-mapping>
         <filter-name>CAS HttpServletRequest WrapperFilter</filter-name>
         <url-pattern>/*</url-pattern>
</filter-mapping>
```

仔细看一下配置过滤器可以发现，三个过滤器正好对应流程图中三次访问CAS Client。

- Authentication Filter：负责将未登录用户跳转到登录界面
- Authentication Filter：负责验证Service Ticket
- HttpServletRequest WrapperFilter：负责将用户信息封装到request和session中。

# **2 统一注销的过程**

## **2.1 不能实现统一注销会有什么问题**

当用户访问系统后从系统注销，如何能够从每个应用中都注销？注意前面1.4部分的描述，如果用户注销时，并没有注销CASClient 02中的会话信息，如果用户在浏览器中直接访问这个应用，因为Session存在，并不会提醒用户重新登录。

这会带来两个潜在的隐患：

1. 用户注销user1后换账号user2重新登录，进入CAS Client 02之后，当前身份其实还是user1，并没有如用户预期一样使用user2身份。
2. 用户user1点击注销后离开，没有关闭浏览器。这时候其他用户直接打开CAS Client 02，能够直接盗用user1的身份进行操作。

## **2.2基本概念：Service、TGT和ST**

CAS已经考虑到统一注销的问题。

这里有三个重要的概念TGT、ST和Service，需要着重介绍一下，因为它们同后续统一注销的方案息息相关。

### **2.2.1  Service**

这是用户第一次访问CAS Client的URL。假设一个CAS Client应用部署在域名oa.company.com，使用HTTP协议，应用首页是index.htm。当用户第一次访问这个应用时，对应的URL地址是 http://oa.company.com/index.htm 。这个URL，对CAS Server来说，就是一个service。

当用户第一次跳转到CAS Server的时候，可以看到传了一个参数service，就是这个值。当CASServer生成Ticket重定向到CAS Client的时候，实际就是在这个service 中添加了一个参数 ticket 。

### **2.2.2   TGT：Ticket Grangting Ticket**

TGT是CAS Server为每一个登录用户创建的登录令牌。在CASServer上拥有了TGT，用户就可以证明自己在CASServer成功登录过。TGT封装了SessionCookie值以及此Cookie值对应的用户信息。当HTTP请求到来时，CAS以此Cookie值为key查询缓存中有无TGT ，如果有的话，则相信用户已登录过。

### **2.2.3  ST：Service Ticket**

ST是CAS Server为用户签发的访问某一service的认证令牌。用户访问service时，service发现用户没有ST，浏览器会跳转到CASServer去获取ST。CAS Server发现用户有TGT，则签发一个ST，返回给用户。用户使用ST作为ticket参数去访问service，service拿ST去CAS Server验证，验证通过后，得到当前登录用户的登录名。

注意TGT和ST，是一对多的关系。一个TGT会维护一个 services 列表，每当为用户创建一个ST并认证通过后，会将这个ST添加到TGT的services列表中。这样，在CASServer端，这个services列表实际维护了一个用户登录过的所有CASClient。这就为实现统一注销打下了基础。

## **2.3 CAS Client的统一注销配置**

CAS Client，为了实现统一注销，除了第一张介绍的三个登录过程的过滤器之外，还需要添加一个统一注销过滤器。

```xml
<filter>
 <filter-name>CAS Single Sign OutFilter</filter-name>
 <filter-class>*.SingleSignOutFilter</filter-class>
</filter>
<filter-mapping>
 <filter-name>CAS Single Sign OutFilter</filter-name>
 <url-pattern>/*</url-pattern>
</filter-mapping>
<listener>
 <listener-class>*.SingleSignOutHttpSessionListener</listener-class>
</listener>

```



## **2.4 CAS Server注销过程**

用户在浏览器中点击“注销”链接，实际浏览器会访问CASServer的注销页面。收到注销请求后，CAS Server会读取到TGT，并检查当前用户登录过的所有service，并依次发送注销请求。

## **2.5 CAS Client注销过程**

CAS Client的注销，核心代码是SingleSignOutFilter，它的关键代码

```java
public voiddoFilter(servletRequest, servletResponse, filterChain){
         HttpServletRequest request =(HttpServletRequest)servletRequest;
         if (handler.isTokenRequest(request)) {
                   handler.recordSession(request);
         } else if (handler.isLogoutRequest(request)) {
                   handler.destroySession(request);
                   return;
         }
         filterChain.doFilter(servletRequest, servletResponse);
}
```

其中handler是SingleSignOutHandler的实例，这个对象完成用户在CASClient端登录信息的维护和注销工作。

至此，CAS完整的登录和注销过程就完成。

## **2.6 思考：什么情况统一注销会失败**

统一注销的实现，需要CAS Server通过HttpClient访问CAS Client的service。如果这个访问过程失败，就会导致统一注销失败。列了几种情况，不详述。

1. 开发调试阶段，使用localhost访问CAS Client。
2. CAS Server部署在外网，CAS Client部署在内网。
3. 网络安全设置，不允许CASServer访问CAS Client。

# **3 CAS Client集群的影响**

前面的论述，一直假定所有的CAS Client都是单点部署，没有集群。如果集群，会有什么影响，应该如何来解决？

## **3.1 Client集群对登录的影响**

假设使用nginx做集群前端，后面部署两台CAS Client 01的实例。我们看看对登录过程会有什么影响。

为了描述方便，CAS Client登录过程会有三次请求（对应三个过滤器），我们依次命名为Authentication Request / Validation Request / Wrapper Request。

Nginx缺省的分发规则，并不是sticky模式，同一个浏览器的请求，会按照nginx自身某种规则进行分发。我们曾经测试过，在双点集群环境下，Authentication Request和ValidationRequest会恰好被分发到两台服务器，这就会导致登录过程死循环。

出现登录死循环的原因，主要在于nginx分发时，没有使用sticky策略，也就是同一个浏览器的请求，永远分发给同一台CAS Client实例。缺省nginx的分发策略，可以根据用户IP分发，实现的是同一个IP永远分发到同一台Client，这样就能解决死循环的问题。

## **3.2 Client集群对注销的影响**

当nginx实现了sitcky转发，同一个浏览器的访问会分发到同一个Client1实例，该用户的会话信息也一直保存在Client1实例中。

当用户统一注销时，由CAS Server向Client发送注销请求，这时候nginx无法确保按当前用户进行分发，因此可能会被分发到Client2。这时候，实际效果是注销失败。

这个问题，在我们当前的环境中真实存在，还没有合理的解决方法。初步分析，大概有几个修改方向。

### **3.2.1  修改nginx分发策略**

问题存在的原因，是因为nginx在分发注销策略时，不能准确分发。如果能在这个环节进行修改，系统代码和环境，基本不用做任何修改。

这里有两种分发方法：

- CAS Server发送的注销请求，分发给对应的后台服务器。


- CAS Server发送的注销请求，广播到所有的后台服务器。

初步结论：同架构组进行了沟通，这两种方案都很难实现，特别是广播的方案，没在网络上找到类似成功的案例。

### **3.2.2  集群的节点实现Session同步**

如果能实现集群Session的同步：同步创建、同步注销，主要在一个Client上实现了注销，其他Client也就同步注销。

这个会对Tomcat性能有影响。

### **3.2.3  集群节点使用redis保存会话信息**

即使是多个节点，它们的会话信息只有一份。一旦失效，则所有节点都失效。这只是一个设想，没有做技术调研，不知能够实现。

这有两种修改方法：

- 修改Tomcat的配置文件，使用redis保存Tomcat的会话信息。


- 修改代码而不是Tomcat，使用redis保存会话信息。

初步结论：架构组不允许修改生产环境的Tomcat，否定了第一种方法。我们只能尝试修改代码并利用redis保存会话。

### **3.2.4  每次请求验证用户是否注销**

首先，在CAS Server中实现一个接口，用于判断某一个ST对应的TGT是否还有效。

在SingleSignOutFilter中，每次访问都调用CAS Server的这个新接口，判断用户是否已经注销。如果已经注销，则立刻注销本实例中的会话信息。

这个方法是比较安全的解决办法，但每次请求都会调用CASServer接口，会对性能造成巨大影响。完全不建议用这种方案。

### **3.2.5  几种策略的初步调研**

对前面提到的几种方案做了初步调研之后：

- 技术实现困难，否定了方案1
- 性能考虑以及架构组的策略，否定方案2
- 架构组的策略，否定方案3中的第一种做法。
- 性能考虑，否定方案4。

因此，可能的做法是修改代码，使用redis保存会话信息。

# 4 使用redis保存会话

在目前的生产环境的限制下，我们只能采用修改代码来实现redis保存会话的实现方案。

## **4.1 Request和Session缺省怎么实现**

在Tomcat缺省的实现中，Session信息都是保存在JVM中，所以不能跨JVM共享。

要想将所有的session都保存到redis中，一种能想到的简单办法是自己写一个CustomSession，将会话信息保存到这个自定义的Session中，并且利用redis等进行保存。但这样做，会带来很大的代码改动，所有涉及到session读写操作的地方可能都需要修改。

我们希望找到更优雅的解决方案，能够修改更少的代码。

## **4.2 WEB请求的执行过程**

Request 和Session什么时候创建？如何传递？

Filter的调用入口函数是doFilter，传入的主要参数是request和response。在此之前，Tomcat已经创建好request。通常情况下，业务代码不需要关心request和session等对象如何创建的问题，只需要使用即可。每个过滤器的实现，当需要继续流程的时候，只需要将得到的request和response传递给下一个filter就行。

但这仅仅是缺省做法，并不表示我们不能修改或重写一个request对象。我们想修改Session的保存位置，如果能在所有的Filter之前插入一个自定义过滤器，定义一个新的Request传递给后面的Filter，并且让后面的Filter和Servlet感受不到变化，就可以实现这个目标。

## **4.3 如何定制Request**

### **4.3.1  增加过滤器**

在所有的Filter之前，插入一个新的Filter。

HttpServletRequest可以重写吗？

### **4.3.2  Tomcat的Request实现**

### **4.3.3  改写之后的Request实现**

在Session重写一个RedisSessionRequest，继承自HttpServletRequestWrapper，并包含原request(RequestFacade)的引用。但需要读取Form参数时，直接调用oriRequest取值。当需要拿到Session对象进行会话信息访问时，调用重载后的函数。

这样就实现了request的封装，在后续的filter和servlet中通过request获取到的session，都是放在redis中的会话数据，不再是缺省保存在JVM中的数据。

### **4.3.4  集群环境的session读写**

当nginx将同一个浏览器的请求分发给不同的Tomcat时，都会根据SessionId从redis中读取Session。因为同一个浏览器发送请求的SessionID相同，所以在不同的Tomcat实例中，会读取到同一个Session对象。

## **4.4 使用Spring Session实现**

根据前面的分析，在项目中自定义Request，就可以实现需求。Spring Session已经是一个成熟的开源实现，并且后端实现了将会话保存在redis、mongodb、jdbc等多种实现，我们没必要自己发明轮子。

Spring提供的例子代码很简洁，跟我们已经实现的业务系统稍微有点不同。在现有系统中，已经定义了bean jedisConnectionFactory，可以直接使用。

### **4.4.1  修改pom.xml**

在pom.xml文件中，添加代码

```xml
<dependency>
         <groupId>org.springframework.session</groupId>
         <artifactId>spring-session-data-redis</artifactId>
         <version>1.2.0.RELEASE</version>
</dependency>
```

### **4.4.2  修改redis配置文件**

在项目中已经有redis配置文件spring-redis.xml，在其中添加内容

```xml
<context:annotation-config/><beans:beanclass="org.springframework.session.data.redis.config.annotation.web.http.RedisHttpSessionConfiguration"/>
```



### **4.4.3  修改web.xml**

在所有的过滤器前面添加一个新的过滤器

```xml
<filter>
 	<filter-name>springSessionRepositoryFilter</filter-name>
 	<filter-class>org.springframework.web.filter.DelegatingFilterProxy</filter-class>
</filter>
<filter-mapping>
 <filter-name>springSessionRepositoryFilter</filter-name>
  	<url-pattern>/*</url-pattern>
  	<dispatcher>REQUEST</dispatcher>
  	<dispatcher>ERROR</dispatcher>
</filter-mapping>
```

## **4.5 测试实现效果**

集成Spring Session后，经过初步测试，能够达到预想效果。（感谢同事瑞钊的实际测试并提供截图）

### **4.5.1  Session信息已经保存到redis中**

用户登录后查看redis中的数据，可以看到这些Session信息。

### **4.5.2  删除redis中会话的影响**

用户登录后继续访问系统，不会切换到CAS登录页面。

如果手工删掉redis中的session，重新访问，可以看到需要重新做一个CAS认证的过程。

### **4.5.3  统一用户注销的测试**

后续需要部署一套生产环境的集群环境，验证统一注销的效果。

## 5 问题：

问题在于，Cas Client中有一个实现类HashMapBackedSessionMappingStorage，这个类的作用，在于存储tiket和sessionId的映射
注销的时候，cas服务器会发来一个tiket，退出过滤器需要根据这个ticket找到对应的sessionId来清除session 而HashMapBackedSessionMappingStorage是存储在Map里的，也就是内存里的，而不是session里

如果能保证nginx转发的路径是固定的，也就是一个访问者 固定访问一个tomcat，那固然没问题。
所以我觉得 你这个方案测试应该是没问题的。

但是，如果这个tomcat挂掉了呢？

靠谱的方式应该是把这个映射关系 也存在redis里。也就是自己实现一个RedisBackedSessionMappingStorage

#### 链接：https://yq.aliyun.com/articles/49871

casclient源代码下载链接：https://github.com/apereo/java-cas-client

cas官网链接：https://www.apereo.org/projects/cas

1. 上面一篇引用别人的分析方案介绍，来描述了下项目中遇到的问题，现在介绍本人怎么解决的
2. 本人项目中用的是改造了tomcat 做的session 共享
3. 所以客户端请求退出，服务端根据TGT查看对应的ST进行请求客户端，通过nginx负载均衡，可能对应到另一台客户端服务器，但是我们的session是存入rediscluster，任意客户端可以根据sessionid取到这个，进行删除，这样session就没了。即在客户端配置的SingleSignOutFilter，需要在HashMapBackedSessionMappingStorage进行删除存入redis的session数据，这样就可以退出了，就是这个原理来处理这个退出登录问题

### 代码依赖jar包

spring-data-redis-1.7.4.RELEASE.jar；jedis-2.9.0.jar；fastjson-1.2.31.jar；注意jar包版本

### 改造的HashMapBackedSessionMappingStorage类代码

```java
package org.jasig.cas.client.session;
/*
- agreements. See the NOTICE file distributed with this work
- for additional information regarding copyright ownership.
- Jasig licenses this file to you under the Apache License,
- Version 2.0 (the "License"); you may not use this file
- except in compliance with the License.  You may obtain a
- copy of the License at the following location:
  *
- http://www.apache.org/licenses/LICENSE-2.0
  *
- Unless required by applicable law or agreed to in writing,
- software distributed under the License is distributed on an
- "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
- KIND, either express or implied.  See the License for the
- specific language governing permissions and limitations
- under the License.
  */

import java.util.HashMap;
import java.util.Map;
import java.util.concurrent.TimeUnit;
import javax.annotation.Resource;
import javax.servlet.http.HttpSession;
import org.jasig.cas.client.session.SessionMappingStorage;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.EnableMBeanExport;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.stereotype.Component;

/**
- HashMap backed implementation of SessionMappingStorage.
- @author Scott Battaglia
- @version Revision Date
- @since 3.1
   */
  public final class HashMapBackedSessionMappingStorage implements SessionMappingStorage {
    protected final transient Logger logger = LoggerFactory.getLogger(getClass());
   private final static String CASCLIENT_PREFIX = "CASCLI:SESSIONID:";
   private final static String CASCLIENT_MAPID_PREFIX = "CASCLI:MAPID:";
   private int casTimeout=86400;
   private RedisTemplate redisTemplate=new RedisTemplate();
  public RedisTemplate getRedisTemplate() {
      return redisTemplate;
  }
    public void setRedisTemplate(RedisTemplate redisTemplate) {
    	this.redisTemplate = redisTemplate;
    }

    public  HashMapBackedSessionMappingStorage(){
    	ApplicationContext ac =new ClassPathXmlApplicationContext("classpath:schemeone/xml/spring-core.xml");
    	setRedisTemplate((RedisTemplate)ac.getBean("redisTemplate"));
     }

    @Override
    public synchronized void addSessionById(String mappingId, HttpSession session) {
            logger.debug("Adding ticket {}", session);
            try {
                String sessionRedisKey = this.getCasSessionRedisKey(session.getId());
                String mappingIdRedisKey = this.getCasMappingIdRedisKey(mappingId);
                this.redisTemplate.boundValueOps(sessionRedisKey).set(mappingId, casTimeout, TimeUnit.SECONDS);
                this.redisTemplate.boundValueOps(mappingIdRedisKey).set(session.getId(), casTimeout, TimeUnit.SECONDS);
            } catch (final Exception e) {
                logger.error("Failed Adding {}", session, e);
            }
    }
    
    @Override
    public synchronized void removeBySessionById(String sessionId) {
    	 logger.debug("Attempting to remove Session=[{}]", sessionId);
    
            final String key =(String) this.redisTemplate.boundValueOps(this.getCasSessionRedisKey(sessionId)).get(); 
    
            if (logger.isDebugEnabled()) {
                if (key != null) {
                    logger.debug("Found mapping for session.  Session Removed.");
                } else {
                    logger.debug("No mapping for session found.  Ignoring.");
                }
            }
            this.redisTemplate.delete(this.getCasMappingIdRedisKey(key));
            this.redisTemplate.delete(this.getCasSessionRedisKey(sessionId));
    }

    @Override
    public synchronized HttpSession removeSessionByMappingId(String mappingId) {
    	//先去取sessionid
    	final String sessionId=(String) this.redisTemplate.boundValueOps(this.getCasMappingIdRedisKey(mappingId)).get(); 
    	//final HttpSession session = (HttpSession) this.redisTemplate.boundValueOps(sessionId).get();
    	 this.redisTemplate.delete(sessionId);
//	        if (session != null) {
//	            removeBySessionById(session.getId());
//	        }
//	        return session;

    	 if (sessionId != null) {
                removeBySessionById(sessionId);
            }
            return null;
    }
    
      private String getCasSessionRedisKey( String sessionId) {
            return CASCLIENT_PREFIX + sessionId;
        }
      
      private String getCasMappingIdRedisKey(String mappingId) {
            return CASCLIENT_MAPID_PREFIX + mappingId;
        }
}

```

### redistemplteBean配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:aop="http://www.springframework.org/schema/aop"
xmlns:context="http://www.springframework.org/schema/context"
xmlns:util="http://www.springframework.org/schema/util"
xsi:schemaLocation="
      http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
      http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd
      http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
      http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util.xsd">
  <!-- 扫描注解Bean -->
<context:component-scan base-package="com.hivescm" />
<aop:config proxy-target-class="true" />
<!-- 开启AOP监听 只对当前配置文件有效 -->
<aop:aspectj-autoproxy expose-proxy="true" />
	<bean id="propertyConfigurer"
	class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
	<property name="locations">
		<list>
          <!--<value>classpath:schemeone/properties/common/*.properties</value> -->
    				<value>classpath:schemeone/properties/common/redis.cluster.properties</value>
    		</list>
    	</property>
    </bean>
<!--     jedis 配置 -->
    <bean id="poolConfig" class="redis.clients.jedis.JedisPoolConfig" >
<!--         最大空闲数 -->
        <property name="maxIdle" value="${redis.maxIdle}" />
<!--         最大建立连接等待时间 -->
        <property name="maxWaitMillis" value="${redis.maxWait}" />
<!--         是否在从池中取出连接前进行检验,如果检验失败,则从池中去除连接并尝试取出另一个 -->
        <property name="testOnBorrow" value="${redis.testOnBorrow}" />
    </bean>
<!--     配置文件加载 -->
    <bean id="resourcePropertySource" class="org.springframework.core.io.support.ResourcePropertySource">
        <constructor-arg name="name" value="redis.cluster.properties"/>
        <constructor-arg name="resource" value="classpath:schemeone/properties/common/redis.cluster.properties"/>
    </bean>
<!--     redisCluster配置 -->
    <bean id="redisClusterConfiguration" class="org.springframework.data.redis.connection.RedisClusterConfiguration">
        <constructor-arg name="propertySource" ref="resourcePropertySource"/>
    </bean>
<!--     redis服务器中心 -->
    <bean id="connectionFactory"  class="org.springframework.data.redis.connection.jedis.JedisConnectionFactory" >
        <constructor-arg name="clusterConfig" ref="redisClusterConfiguration"/>
        <constructor-arg name="poolConfig" ref="poolConfig"/>
        <property name="password" value="${redis.password}" />
        <property name="timeout" value="${redis.timeout}" ></property>
    </bean >
    <bean id="redisTemplate" class="org.springframework.data.redis.core.RedisTemplate" >
        <property name="connectionFactory" ref="connectionFactory" />
<!--         如果不配置Serializer，那么存储的时候缺省使用String，如果用User类型存储，那么会提示错误User can't cast to String！！  -->
        <property name="keySerializer" >
            <bean class="org.springframework.data.redis.serializer.StringRedisSerializer" />
        </property>
        <property name="valueSerializer" >
         <bean class="org.springframework.data.redis.serializer.JdkSerializationRedisSerializer" />
        </property>
        <property name="hashKeySerializer">
            <bean class="org.springframework.data.redis.serializer.StringRedisSerializer"/>
        </property>
        <property name="hashValueSerializer">
            <bean class="org.springframework.data.redis.serializer.JdkSerializationRedisSerializer"/>
        </property>
    </bean >
</beans>
```

### redis.cluster.properties　

```properties
#redis\u4E2D\u5FC3
#redis\u7684\u670D\u52A1\u5668\u5730\u5740
redis.host=192.168.103.158
#redis\u7684\u670D\u52A1\u7AEF\u53E3
redis.port=6379
#\u5BC6\u7801
redis.password=
#\u6700\u5927\u7A7A\u95F2\u6570
redis.maxIdle=100
#\u6700\u5927\u8FDE\u63A5\u6570
redis.maxActive=300
#\u6700\u5927\u5EFA\u7ACB\u8FDE\u63A5\u7B49\u5F85\u65F6\u95F4
redis.maxWait=1000
#\u5BA2\u6237\u7AEF\u8D85\u65F6\u65F6\u95F4\u5355\u4F4D\u662F\u6BEB\u79D2
redis.timeout=100000
redis.maxTotal=1000
redis.minIdle=8
#\u660E\u662F\u5426\u5728\u4ECE\u6C60\u4E2D\u53D6\u51FA\u8FDE\u63A5\u524D\u8FDB\u884C\u68C0\u9A8C,\u5982\u679C\u68C0\u9A8C\u5931\u8D25,\u5219\u4ECE\u6C60\u4E2D\u53BB\u9664\u8FDE\u63A5\u5E76\u5C1D\u8BD5\u53D6\u51FA\u53E6\u4E00\u4E2A
redis.testOnBorrow=true

#sentinel
#spring.redis.sentinel.node1.host=127.0.0.1
#spring.redis.sentinel.node2.host=127.0.0.1
#spring.redis.sentinel.node3.host=127.0.0.1
#spring.redis.sentinel.node1.port=26379
#spring.redis.sentinel.node2.port=26479
#spring.redis.sentinel.node3.port=26579
#sentinel

#jediscluster
#cluster1.host.port=127.0.0.1:7000
#cluster2.host.port=127.0.0.1:7001
#cluster3.host.port=127.0.0.1:7002
#cluster4.host.port=127.0.0.1:7003
#cluster5.host.port=127.0.0.1:7004
#cluster6.host.port=127.0.0.1:7005
#cluster7.host.port=127.0.0.1:7006
#cluster8.host.port=127.0.0.1:7007
#jediscluster

#rediscluster
#spring.redis.cluster.nodes=192.168.103.158:6379
spring.redis.cluster.nodes=192.168.103.174:6379,192.168.103.174:6389,192.168.103.174:6399,192.168.103.173:6379,192.168.103.173:6389,192.168.103.173:6399
spring.redis.cluster.max-redirects=3
```
