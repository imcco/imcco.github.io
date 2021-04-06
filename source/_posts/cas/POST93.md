---
title: CAS单点登录(一)
tags: CAS
category: CAS
abbrlink: 34685
date: 2018-01-04 21:34:36
---
![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0066.jpg)

单点登录（Single Sign On），简称为SSO，是目前比较流行的企业业务整合的解决方案之一。SSO的定义是在多个应用系统中，用户只需要登录一次就可以访问所有相互信任的应用系统。
CAS（Central Authentication Service），中央认证服务。CAS(Central Authentication Service)是一款不错的针对 Web应用的单点登录框架。
<!--more-->

# CAS基本原理

讲解CAS之前先来学习两个基本术语
 
## 术语解释：


### Ticket Grangting Ticket(TGT) ：

TGT是CAS为用户签发的登录票据，拥有了TGT，用户就可以证明自己在CAS成功登录过。TGT封装了Cookie值以及此Cookie值对应的用户信息。用户在CAS认证成功后，CAS生成cookie（叫TGC），写入浏览器，同时生成一个TGT对象，放入自己的缓存，TGT对象的ID就是cookie的值。当HTTP再次请求到来时，如果传过来的有CAS生成的cookie，则CAS以此cookie值为key查询缓存中有无TGT，如果有的话，则说明用户之前登录过，如果没有，则用户需要重新登录。

### Ticket-granting cookie(TGC)：

存放用户身份认证凭证的cookie，在浏览器和CAS Server间通讯时使用，并且只能基于安全通道传输（Https），是CASServer用来明确用户身份的凭证。

### Service ticket(ST) ：

服务票据，服务的惟一标识码 , 由 CASServer 发出（ Http 传送），用户访问Service时，service发现用户没有ST，则要求用户去CAS获取ST.用户向CAS发出获取ST的请求，CAS发现用户有TGT，则签发一个ST，返回给用户。用户拿着ST去访问service，service拿ST去CAS验证，验证通过后，允许用户访问资源

## 深入CAS

 
#### 从结构上看，CAS包含两个部分：

### CAS Server

CASServer 负责完成对用户的认证工作 , 需要独立部署 , CAS Server 会处理用户名 /密码等凭证 (Credentials) 。

### CAS Client

负责处理对客户端受保护资源的访问请求，需要对请求方进行身份认证时，重定向到 CAS Server 进行认证。（原则上，客户端应用不再接受任何的用户名密码等 Credentials ）。

CASClient 与受保护的客户端应用部署在一起，以 Filter 方式保护受保护的资源。

 
#### CAS最基本的协议过程：

![image](http://ovi3ob9p4.bkt.clouddn.com/cas/CAS001.jpg)

CAS Client 与受保护的客户端应用部署在一起，以Filter方式保护 Web应用的受保护资源，过滤从客户端过来的每一个 Web 请求


1.  （step 1）Web浏览器访问CAS Client，无session并且无票据(ST)，定向到CASServer（step 2），又因为浏览器中并没有cookie，故服务端拿不到TGC，因此需要重新登录


2. （Step 3）是用户认证过程，如果用户提供了正确的CAS Server 会处理用户名 / 密码等凭证 (Credentials) ，认证成功后，CAS生成cookie（叫TGC），写入浏览器，同时生成一个TGT对象，再根据TGT发放票据ST，并且重定向用户到CAS Client（附带刚才产生的ServiceTicket）， Service Ticket 是不可以伪造的（step4）

注：ST前半部分为登录url，后半部分为我客户端要访问的页面地址，只有当登录成功才会直接转向客户端访问的页面

3. （Step 5）拿着ST去 CAS Server验证一下，验证成功返回用户信息（step6）

注：收到ST后，为什么还要验证呢？

因为CAS知道这个用户已经登录过了，但是对于这个项目来说，我并不知道这个用户已经登录过了，故需要验证

4. 当用户访问另一个应用的服务再次被重定向到 CAS Server 的时候， CAS Server 会主动获到这个 TGC cookie ，然后做下面的事情：

1)如果 User 持有 TGC 且其还没失效，那么就走基础协议图的 Step4 ，达到了 SSO 的效果；

2)如果 TGC 失效，那么用户还是要重新认证 ( 走基础协议图的 Step3) 。

CAS 请求认证时序图如下：

![image](http://ovi3ob9p4.bkt.clouddn.com/cas/CAS002.jpg)


#### CAS服务端登录时处理：

第一步：cas往浏览器增加cookie（TGC） 

CAS向浏览器送回一个所谓的“内存cookie”。这种cookie并不是真的保存在内存中，而只是浏览器一关闭，cookie就自动过期。这个cookie称为“ticket-grantingcookie”，用来表明用户已经成功地登录。

这个Cookie是一个加密的Cookie，其中保存了用户登录的信息。用于以后其它应用客户端登录。

第二步：cas同时创建一个ticket(ST)重定向到原来的cas客户端

认证成功后，CAS服务器创建一个很长的、随机生成的字符串，称为“Ticket”。随后，CAS将这个ticket和成功登录的用户，以及服务联系在一起。这个ticket是一次性使用的一种凭证，它只对登录成功的用户及其服务使用一次。使用过以后立刻失效。
 
#### CAS 客户端应用Ａ的处理

第一步：收到ticket后，向cas提交验证ticket

第二步：ticket验证后创建session

以后登录此应用时，没有ticket，但IE能提供session,从session中取得CASReceipt,并验证如果有效说明已经在此应用认证过，允许访问此应用

到此为止，CAS会记录用户已在应用Ａ已经登录

#### 用户登录到应用Ｂ是如何处理

用户进入应用Ｂ时，首先仍然会重定向到CAS服务器。不过此时CAS服务器不再要求用户输入用户名和密码，而是首先自动寻找Cookie，根据Cookie中保存的信息，进行登录。然后，CAS同样给出新的ticket重定向应用Ｂ给cas验证（流程同应用Ａ验证方式），如果验证成功则应用Ｂ创建session记录CASReceipt信息到session中，以后凭此session登录应用Ｂ。

##### 原理：１个cookie+Ｎ个session

CAS创建cookie在所有应用中登录时cas使用,各应用通过在IE创建各自的session来标识应用是否已经登录。

Cookie:在cas为各应用登录时使用，实现了只须一次录入用户密码

Session:各应用会创建自己的session表示是否登录

#### 具体描述一下客户端消息流程

1. 第一次访问http://localhost:8080/a,

CLIENT：没票据且SESSION中没有消息所以跳转至CAS

CAS：拿不到TGC故要求用户登录

2. 认证成功后回跳

CAS：通过TGT生成ST发给客户端，客户端保存TGC，并重定向到http://localhost:8080/a

CLIENT：带有票据(ST)所以不跳转只是后台发给CAS验证票据（浏览器中无法看到这一过程）

3. 第一次访问http://localhost:8080/b

CLIENT：没票据且SESSION中没有消息所以跳转至CAS

CAS：从客户端取出TGC，如果TGC有效则给用户ST并后台验证ST，从而SSO。【如果失效重登录或注销时，怎么通知其它系统更新SESSION信息呢？？TicketGrantingTicketImpl类grantServiceTicket方法里this.services.put(id,service);可见CAS端已经记录了当前登录的子系统】

单点退出：

![image](http://ovi3ob9p4.bkt.clouddn.com/cas/CAS003.jpg)

4. 再次访问http://localhost:8080/a

CLIENT：没票据但是SESSION中有消息故不跳转也不用发CAS验证票据，允许用户访问
 
# 实例
 
## 域名地址的修改：

根据演示需求，用修改hosts文件的方法添加域名最简单方便（这个非常重要），在文件 C:\Windows\System32\drivers\etc\hosts 文件中添加三条

```
127.0.0.1    demo.micmiu.com

127.0.0.1    app1.micmiu.com

127.0.0.1    app2.micmiu.com
```

•demo.micmiu.com  =>> 对应部署cas server的tomcat，这个虚拟域名还用于证书生成

•app1.micmiu.com  =>> 对应部署app1 的tomcat

•app2.micmiu.com   =>> 对应部署app2 的tomcat

注：可选配置

 
## 端口号的修改：

修改tomcat的启动端口（共计5处），在文件conf/server.xml文件找到如下内容：
   
```xml
<Server port="8005" shutdown="SHUTDOWN">  
    <Connector port="8080" protocol="HTTP/1.1"   
       connectionTimeout="20000"   
       redirectPort="8443" />  
    <Connector port="8009" protocol="AJP/1.3" redirectPort="8443" />
```

修改成：
   
```xml
<Server port="18005" shutdown="SHUTDOWN">  
    <Connector port="18080" protocol="HTTP/1.1"   
       connectionTimeout="20000"   
       redirectPort="18443" />  
      
    <Connector port="18009" protocol="AJP/1.3" redirectPort="18443" />
```

注：由于需要在同一个机器上演示，故需要修改端口号

## 安全证书配置：

CAS默认使用的是HTTPS协议，如果对安全要求不高，可使用HTTP协议。 

### 修改deployerConfigContext.xml 

(cas/WEB-INF)增加参数p:requireSecure="false"，是否需要安全验证，即HTTPS，false为不采用。
    
```xml
<bean class="org.jasig.cas.authentication.handler.support.HttpBasedServiceCredentialsAuthenticationHandler" p:httpClient-ref="httpClient"   
    p:requireSecure="false" />
```
### 修改 ticketGrantingTicketCookieGenerator.xml
     
(cas/WEB-INF/spring-configuration/ticketGrantingTicketCookieGenerator.xml) 中ticketGrantingTicketCookieGenerator p:cookieSecure 属性修改为 false。

```xml
<bean id="ticketGrantingTicketCookieGenerator" class="org.jasig.cas.web.support.CookieRetrievingCookieGenerator"  
    :cookieSecure="false" p:cookieMaxAge="-1" p:cookieName="CASTGC" p:cookiePath="/cas" />
```
 
## 配置服务端

CAS-Server下载地址：http://www.jasig.org/cas/download

我们以cas-server-3.4.11-release.zip为例，解压提取cas-server-3.4.11/modules/cas-server-webapp-3.4.11.war文件，把该文件copy到G:\sso\tomcat-cas\webapps\ 目录下，并重命名为：cas.war.

启动tomcat-cas，在浏览器地址栏输入：https://demo.micmiu.com:8080/cas/login，回车

CAS-server的默认验证规则：只要用户名和密码相同就认证通过（仅仅用于测试，生成环境需要根据实际情况修改），输入admin/admin点击登录，就可以看到登录成功的页面：

输入用户名admin和密码admin登录则会出现

![image](http://ovi3ob9p4.bkt.clouddn.com/cas/CAS004.jpg)

看到上述页面表示CAS-Server已经部署成功。

## 配置客户端

Cas-Client 下载

CAS-Client下载地址：http://downloads.jasig.org/cas-clients/

以cas-client-3.2.1-release.zip为例，解压提取cas-client-3.2.1/modules/cas-client-core-3.2.1.jar

借以tomcat默认自带的webapps\examples 作为演示的简单web项目

启动tomcat-app1，浏览器输入http://app1.micmiu.com:18080/examples/servlets/回车：

注：端口号修改
   
```xml
<Connector port="18080" protocol="HTTP/1.1"  
                   connectionTimeout="20000"  
                   redirectPort="18443" />  
    <Connector port="18009" protocol="AJP/1.3" redirectPort="18443" />
```

看到上述界面表示tomcat-app1的基本安装配置已经成功。

接下来复制client的lib包cas-client-core-3.2.1.jar和commons-logging-1.1.jar到tomcat-app1\webapps\examples\WEB-INF\lib\目录下，在tomcat-app1\webapps\examples\WEB-INF\web.xml文件中增加如下内容：

```xml
<!-- 用于单点退出，该过滤器用于实现单点登出功能，可选配置-->  
            <listener>  
                <listener-class>org.jasig.cas.client.session.SingleSignOutHttpSessionListener</listener-class>  
            </listener>  
       
            <!-- 该过滤器用于实现单点登出功能，可选配置。 -->  
            <filter>  
                <filter-name>CAS Single Sign Out Filter</filter-name>  
                <filter-class>org.jasig.cas.client.session.SingleSignOutFilter</filter-class>  
            </filter>  
            <filter-mapping>  
                <filter-name>CAS Single Sign Out Filter</filter-name>  
                <url-pattern>/*</url-pattern>  
            </filter-mapping>  
                 <!-- 该过滤器负责用户的认证工作，必须启用它 -->  
            <filter>  
                <filter-name>CAS Filter</filter-name>  
                <filter-class>org.jasig.cas.client.authentication.AuthenticationFilter</filter-class>  
                <init-param>  
                    <param-name>casServerLoginUrl</param-name>  
                    <param-value>https://demo.micmiu.com:8080/cas/login</param-value>  
                    <!--这里的server是服务端的IP -->  
                      
                </init-param>  
                <init-param>  
                    <param-name>serverName</param-name>  
                    <param-value>http://app1.micmiu.com:18080</param-value>  
                </init-param>  
            </filter>  
            <filter-mapping>  
                <filter-name>CAS Filter</filter-name>  
                <url-pattern>/*</url-pattern>  
            </filter-mapping>  
            <!-- 该过滤器负责对Ticket的校验工作，必须启用它 -->  
            <filter>  
                <filter-name>CAS Validation Filter</filter-name>  
                <filter-class>  
                    org.jasig.cas.client.validation.Cas20ProxyReceivingTicketValidationFilter</filter-class>  
                <init-param>  
                    <param-name>casServerUrlPrefix</param-name>  
                    <param-value>https://demo.micmiu.com:8080/cas</param-value>  
                </init-param>  
                <init-param>  
                    <param-name>serverName</param-name>  
                    <param-value>http://app1.micmiu.com:18080</param-value>  
                </init-param>  
            </filter>  
            <filter-mapping>  
                <filter-name>CAS Validation Filter</filter-name>  
                <url-pattern>/*</url-pattern>  
            </filter-mapping>  
       
            <!--  
                该过滤器负责实现HttpServletRequest请求的包裹，  
                比如允许开发者通过HttpServletRequest的getRemoteUser()方法获得SSO登录用户的登录名，可选配置。  
            -->  
            <filter>  
                <filter-name>CAS HttpServletRequest Wrapper Filter</filter-name>  
                <filter-class>  
                    org.jasig.cas.client.util.HttpServletRequestWrapperFilter</filter-class>  
            </filter>  
            <filter-mapping>  
                <filter-name>CAS HttpServletRequest Wrapper Filter</filter-name>  
                <url-pattern>/*</url-pattern>  
            </filter-mapping>  
       
                 <!--  
            该过滤器使得开发者可以通过org.jasig.cas.client.util.AssertionHolder来获取用户的登录名。  
            比如AssertionHolder.getAssertion().getPrincipal().getName()。  
            -->  
            <filter>  
                <filter-name>CAS Assertion Thread Local Filter</filter-name>  
                <filter-class>org.jasig.cas.client.util.AssertionThreadLocalFilter</filter-class>  
            </filter>  
            <filter-mapping>  
                <filter-name>CAS Assertion Thread Local Filter</filter-name>  
                <url-pattern>/*</url-pattern>  
            </filter-mapping>  
       
            <!-- ======================== 单点登录结束 ======================== -->
```

第二个examples类似配置。

启动之前配置好的三个tomcat分别为：tomcat-cas、tomcat-app1、tomcat-app2.

## 基本的测试

预期流程： 打开app1 url —->跳转cas server 验证 —-> 显示app1的应用 —-> 打开app2 url —-> 显示app2应用 —-> 注销casserver —-> 打开app1/app2 url —-> 重新跳转到cas server 验证.

打开浏览器地址栏中输入：http://app1.micmiu.com:18080/examples/servlets/servlet/HelloWorldExample，回车：

出现CAS登录页

验证通过后显示出 hello world.

教程：http://www.micmiu.com/enterprise-app/sso/sso-cas-sample/#viewSource

# 数据库查询认证机制-xml方式

前面服务端认证机制采用的是默认配置即CAS Servier默认用户名和密码一致即可登录成功，下面侧重于应用方面，真正通过查询用户名密码来进程验证用户是否可以登录。
 
## CAS Server添加相关的jar包 

需要在web项目的lib下添加两个包：cas-server-support-jdbc-x.x.x.jar和 mysql-connector-java-x.x.x-bin.jar（具体版本号根据情况而定）

## 修改CAS Server的配置 

在%tomcat_cas%/webapps/cas/WEB_INF/deployerConfigContext.xml找到如下信息：

```xml
<bean class="org.jasig.cas.authentication.handler.support.SimpleTestUsernamePasswordAuthenticationHandler" />
```

修改成如下：

```xml
<bean class="org.jasig.cas.adaptors.jdbc.QueryDatabaseAuthenticationHandler">  
            <property name="dataSource" ref="dataSource" ></property>  
            <property name="sql" value="select password from t_user where login_name=?" ></property>  
            <!--<property name="passwordEncoder" ref="MD5PasswordEncoder" ></property>-->  
    </bean>
```


同时增加datasource和加密处理两个bean的定义：

```xml
<bean id="dataSource"  
            class="org.springframework.jdbc.datasource.DriverManagerDataSource">  
            <property name="driverClassName" value="com.mysql.jdbc.Driver" />  
            <property name="url" value="jdbc:mysql://localhost/test" />  
            <property name="username" value="root" />  
            <property name="password" value="root" />  
    </bean>  
    <bean id="MD5PasswordEncoder"  
            class="org.jasig.cas.authentication.handler.DefaultPasswordEncoder">  
            <constructor-arg index="0" value="MD5" />  
    </bean>
```

修改后的效果：

![image](http://ovi3ob9p4.bkt.clouddn.com/cas/CAS005.jpg)

本地数据库中添加用户数据信息

登录。

### 注意:

   1. 密码加密过程，如果不用，注释掉即可。

   2. QueryDatabaseAuthenticationHandler是cas-server-support-jdbc提供的查询接口其中一个是通过配置一个SQL 语句查出密码，与所给密码匹配；

   3. sql语句就是查询哪一张表，本例根据t_user表的login_name字段查询密码，CAS会匹配用户输入的密码，如果匹配则通过；

   4. passwordEncoder这个是处理密码的加密，如果想要你的应用中数据库保存的是加密过的，比如本例是使用MD5加密的，所以配置了MD5PasswordEncoder这个Handler，cas内置了MD5的功能所以只需要配置一下就可以了；如果在实际应用中使用的是公司自己的加密算法那么就需要自己写一个Handler来处理密码，实现方式也比较简单，创建一个类继承org.jasig.cas.authentication.handler.PasswordEncoder然后在encode方法中加密用户输入的密码然后返回即可。

 
## 配置多个数据库

如果需要配置多个数据库，可以配置多个QueryDatabaseAuthenticationHandler和多个datasource

假如a_user中有一个用户：auser，b_user中有一个用户buser，这样你无论用哪一个用户登录，CAS就会先查a_user，如果用户名密码都正确，那么就通过，如果a_user中验证失败，那么CAS就会再查b_user，用户名密码都正确就算通过了，此时不正确，就算这次登录验证没通过。

# 数据库查询认证机制-自定义编码方式

通过xml配置方式实现数据库查询认证，的确简单但是不够灵活。但是如果登录验证逻辑稍微复杂些，可能通过这种配置方式就不能满足需求了，比如：当用户登录时，需要判断该用户是否绑定了邮箱，如果未绑定，拒绝登录并给出提示信息。 

遇到类似的情况，就需要使用自定义登录来完成，并且给出的提示信息也需要是自定义的。 
自定义登录验证（默认实现QueryDatabaseAuthenticationHandler）

CAS内置了一些AuthenticationHandler实现类，如下图所示，在cas-server-support-jdbc包中提供了基于jdbc的用户认证类。

 如果需要实现自定义登录，只需要实现org.jasig.cas.authentication.handler.AuthenticationHandler接口即可，当然也可以利用已有的实现，比如创建一个继承自org.jasig.cas.adaptors.jdbc.AbstractJdbcUsernamePasswordAuthenticationHandler的类，实现方法可以参考org.jasig.cas.adaptors.jdbc.QueryDatabaseAuthenticationHandler类：
 
 ![image](http://ovi3ob9p4.bkt.clouddn.com/cas/CAS006.jpg)
 
修改authenticateUsernamePasswordInternal方法中的代码为自己的认证逻辑即可。

## 操作步骤：

1. Eclipse中引入cas-server-webapp项目，并在lib下添加两个jar包

```
cas-server-webapp-3.5.2.war
cas-server-support-jdbc-x.x.x.jar
mysql-connector-java-x.x.x-bin.jar
```
2. 自定义一个类，这个类的内容可以是复制QueryDatabaseAuthenticationHandler

类中的核心方法：

```java
 protected final boolean authenticateUsernamePasswordInternal(final UsernamePasswordCredentials credentials) throws AuthenticationException {  
        //获取前台传递过来的值，用户名和密码  
        final String username = getPrincipalNameTransformer().transform(credentials.getUsername());  
        final String password = credentials.getPassword();  
        final String encryptedPassword = this.getPasswordEncoder().encode(  
            password);  
          
        try {  
            /** 
             * 以下的代码为CAS的数据库认证默认实现，如果想编写自己的实现方式，可以删除一下代码实现自己的登录认证 
             */           
            //sql为配置文件中配置的sql语句       
            final String dbPassword = getJdbcTemplate().queryForObject(this.sql, String.class, username);  
            return dbPassword.equals(encryptedPassword);  
                          
            } catch (final IncorrectResultSizeDataAccessException e) {  
            // this means the username was not found.  
            return false;  
        }  
    }  

```

3. 根据业务需求编写自己的自定义登录方法，修改如下代码即可
 
```java
final String dbPassword = getJdbcTemplate().queryForObject(this.sql, String.class, username);  
    return dbPassword.equals(encryptedPassword);
```

当然也可以访问进行

http://localhost:8080/cas-server-webapp/login 调试。

4. 配置使自定义登录认证生效

```xml
<!-- 注释掉默认的配置，使用自定义类配置 -->  
                    <!-- <bean class="org.jasig.cas.adaptors.jdbc.QueryDatabaseAuthenticationHandler">  
                        <property name="dataSource" ref="dataSource" ></property>  
                        <property name="sql" value="select password from t_user where login_name=?" ></property>  
                        <property name="passwordEncoder" ref="MD5PasswordEncoder" ></property>  
                    </bean> -->  
                    <bean class="com.tgb.handler.CustomQueryDBHandler">  
                        <property name="dataSource" ref="dataSource" ></property>  
                        <property name="sql" value="select password from t_user where login_name=?" ></property>  
                        <!-- <property name="passwordEncoder" ref="MD5PasswordEncoder" ></property> -->  
                    </bean>
```

完成以上步骤，自定义登录即可实现！

自定义错误提示消息（默认实现IncorrectResultSizeDataAccessException继承自RuntimeException）

CAS AuthenticationException结构如下图，CAS已经内置了一些异常，比如用户名密码错误、未知的用户名错误等。

当用户名输入正确，而密码错误时提示“密码错误”

只需要在自定义的AuthenticationHandler类的验证方法中，验证失败的地方抛出异常即可。

密码错误的异常类：

 ![image](http://ovi3ob9p4.bkt.clouddn.com/cas/CAS007.jpg)
 
请注意代码中的CODE私有属性，该属性定义了一个本地化资源文件中的键，通过该键获取本地化资源中对应语言的文字，这里只实现中文错误消息提示，修改WEB-INF/classes/messages_zh_CN.properties文件，添加CODE定义的键值对，如下示例：

error.authentication.credentials.bad.usernameorpassword.password=\u5bc6\u7801\u9519\u8bef

后面面的文字是使用jdk自带的native2ascii编码工具：native2ascii转换成utf-8格式。

接下来只需要在自定义的AuthenticationHandler类的验证方法中，验证失败的地方抛出异常即可。
 
自定义AuthenticationHandler示例代码如下：

```java
@Override  
        protected final boolean authenticateUsernamePasswordInternal(final UsernamePasswordCredentials credentials) throws AuthenticationException {  
            //获取前台传递过来的值，用户名和密码  
            final String username = getPrincipalNameTransformer().transform(credentials.getUsername());  
            final String password = credentials.getPassword();  
            final String encryptedPassword = this.getPasswordEncoder().encode(  
                password);  
              
            try {  
                  final String dbPassword = null;  
                if (dbPassword == null || dbPassword == "") {  
                    throw new BadPasswordAuthenticationException();  
                }  
                return dbPassword.equals(encryptedPassword);  
            } catch (final IncorrectResultSizeDataAccessException e) {  
                // this means the username was not found.  
                return false;  
            }
```
配置使自定义错误提示生效 同上（配置自定义登录认证）！

# 配置转换器返回更多用户信息

从cas server登录成功后，默认只能从cas server得到用户名。但程序中也可能遇到需要得到更多如姓名，手机号，email等更多用户信息的情况。

cas client拿到用户名后再到数据库中查询，的确可以得到关于该用户的更多信息。

但是如果用户登录成功后，直接从cas server返回给cas client用户的详细信息，这也是一个不错的做法。这个好处，尤其是在分布式中得以彰显，cas server可以把用户信息传递给各个应用系统，如果是上面那种做法，那么各个系统得到用户名后，都得去数据库中查询一遍，无疑是一件重复性工作。

## 首先需要配置属性attributeRepository

首先，你需要到WEB-INF目录找到 deployerConfigContext.xml文件，同时配置 attributeRepository 如下：
   
```xml
<bean  class="org.jasig.services.persondir.support.jdbc.SingleRowJdbcPersonAttributeDao" id="attributeRepository">  
            <constructor-arg index="0" ref="dataSource"/>  
            <constructor-arg index="1" value="select * from t_user where {0}"/>  
            <property name="queryAttributeMapping">  
                <map>  
                    <!--这里的key需写username和登录页面一致，value对应数据库用户名字段-->  
                    <entry key="username" value="loginname"/>   
                       
                </map>  
            </property>  
            <property name="resultAttributeMapping">  
                <map>  
                    <!--key为对应的数据库字段名称，value为提供给客户端获取的属性名字，系统会自动填充值-->    
                    <entry key="Id" value="Id"/>                  
                    <entry key="password" value="password"/>  
                    <entry key="age" value="age"/>   
                </map>  
            </property>  
           <!--  <property name="queryType">  
                <value>OR</value>  
          </property> -->  
              
        </bean>
```

其中：

切记：查询出来的字段名中间不能使用 _ (下划线)，否则获取不到数据，如 cell_phone 需要 设置别名为 cellPhone.

queryAttributeMapping是组装sql用的查询条件属性，上述配置后，结合封装成查询sql就是 select* from userinfo where loginname=#username#

resultAttributeMapping是sql执行完毕后返回的结构属性， key对应数据库字段，value对应客户端获取参数。

如果要组装多个查询条件，需要加上下面这个，默认为AND


```xml
<property name="queryType">

<value>OR</value>

     </property>
```

## 配置用户认证凭据转化的解析器

也是在 deployerConfigContext.xml中，为 UsernamePasswordCredentialsToPrincipalResolver注入 attributeRepository，那么 attributeRepository就会被触发并通过此类进行解析，红色为新添部分。
    
```xml
<property name="credentialsToPrincipalResolvers">  
                <list>  
                    <bean class="org.jasig.cas.authentication.principal.UsernamePasswordCredentialsToPrincipalResolver" >  
                        <property name="attributeRepository" ref="attributeRepository" />           
                    </bean>  
                    <bean  
                        class="org.jasig.cas.authentication.principal.HttpBasedServiceCredentialsToPrincipalResolver" />  
                </list>  
            </property>
```

## 修改 deployerConfigContext.xml

deployerConfigContext.xml中的 org.jasig.cas.services.InMemoryServiceRegistryDaoImpl的属性 registeredServices

修改 registeredServices  列表中的每个协议中的 allowedAttributes属性的值。列出的每个值，在客户端就可以访问了

   
```xml
<bean  
            id="serviceRegistryDao"  
            class="org.jasig.cas.services.InMemoryServiceRegistryDaoImpl">  
                <property name="registeredServices">  
                    <list>  
                        <bean class="org.jasig.cas.services.RegexRegisteredService">  
                            <property name="id" value="0" />  
                            <property name="name" value="HTTP and IMAP" />  
                            <property name="description" value="Allows HTTP(S) and IMAP(S) protocols" />  
                            <property name="serviceId" value="^(https?|imaps?)://.*" />  
                            <property name="evaluationOrder" value="10000001" />                        
                              
                               <property name="allowedAttributes">  
                                    <list>  
                                            <value>Id</value>  
                                            <value>password</value>  
                                             <value>age</value>   
                                    </list>  
                            </property>  
      
                        </bean>
```


此步骤灰常重要，可以看看 org.jasig.cas.services.RegexRegisteredService的源码，其中的 allowedAttributes是关键

【提示】网上说此bean中的ignoreAttributes属性默认是不添加用户信息，查看了 CAS 3.5.2版本的 AbstractRegisteredService 源码后，发现其默认值就是 false，即：添加属性后，客户端就可见了

## 修改casServiceValidationSuccess.jsp

```
WEB-INF/view/jsp/protocol/2.0/casServiceValidationSuccess.jsp
```

在server验证成功后，这个页面负责生成与客户端交互的xml信息，在默认的casServiceValidationSuccess.jsp中，只包括用户名，并不提供其他的属性信息，因此需要对页面进行扩展，如下，红色为新添加部分 

    
```xml
<cas:serviceResponse xmlns:cas='http://www.yale.edu/tp/cas'>  
      <cas:authenticationSuccess>  
    <cas:user>${fn:escapeXml(assertion.chainedAuthentications[fn:length(assertion.chainedAuthentications)-1].principal.id)}</cas:user>  
      
    <c:if test="${fn:length(assertion.chainedAuthentications[fn:length(assertion.chainedAuthentications)-1].principal.attributes) > 0}">  
                <cas:attributes>  
                    <c:forEach var="attr" items="${assertion.chainedAuthentications[fn:length(assertion.chainedAuthentications)-1].principal.attributes}">  
                        <cas:${fn:escapeXml(attr.key)}>${fn:escapeXml(attr.value)}</cas:${fn:escapeXml(attr.key)}>  
                    </c:forEach>  
                </cas:attributes>  
            </c:if>  
      
    <c:if test="${not empty pgtIou}">  
       <cas:proxyGrantingTicket>${pgtIou}</cas:proxyGrantingTicket>  
    </c:if>  
    <c:if test="${fn:length(assertion.chainedAuthentications) > 1}">  
    <cas:proxies>  
    <c:forEach var="proxy" items="${assertion.chainedAuthentications}" varStatus="loopStatus" begin="0" end="${fn:length(assertion.chainedAuthentications)-2}" step="1">  
        <cas:proxy>${fn:escapeXml(proxy.principal.id)}</cas:proxy>  
    </c:forEach>  
    </cas:proxies>  
    </c:if>  
     </cas:authenticationSuccess>  
    </cas:serviceResponse>
```

通过完成上面四个步骤的配置后，CAS Server端的工作就完成了，那么如何在客户端获取这些信息呢？下面进行说明：

cas client获取用户信息：
  
```java
AttributePrincipal principal = (AttributePrincipal) request.getUserPrincipal();  
    Map attributes = principal.getAttributes();  
      
    String email=attributes .get("age");
```

## 补充：
 
### cas_client项目：

#### web.XML


```xml
    <?xml version="1.0" encoding="UTF-8"?>  
    <web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://java.sun.com/xml/ns/javaee" xmlns:web="http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd" xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd" id="WebApp_ID" version="2.5">  
      <display-name>cas_client</display-name>  
      <welcome-file-list>  
        <welcome-file>userInfoView.jsp</welcome-file>  
      </welcome-file-list>  
        
     <!-- ======================== 单点登录开始 ======================== -->  
                     <!-- 用于单点退出，该过滤器用于实现单点登出功能，可选配置-->  
                    <listener>  
                            <listener-class>org.jasig.cas.client.session.SingleSignOutHttpSessionListener</listener-class>  
                    </listener>  
                    <filter>  
                            <filter-name>CAS Single Sign Out Filter</filter-name>  
                            <filter-class>org.jasig.cas.client.session.SingleSignOutFilter</filter-class>  
                    </filter>  
                    <filter-mapping>  
                            <filter-name>CAS Single Sign Out Filter</filter-name>  
                            <url-pattern>/*</url-pattern>  
                    </filter-mapping>  
                      
                      
                    <filter>  
                            <filter-name>CAS Filter</filter-name>  
                            <filter-class>org.jasig.cas.client.authentication.AuthenticationFilter</filter-class>  
                            <init-param>  
                                    <param-name>casServerLoginUrl</param-name>  
                                    <param-value>http://localhost:8080/cas</param-value>  
                            </init-param>  
                            <init-param>  
                                    <param-name>serverName</param-name>  
                                    <param-value>http://localhost:18080</param-value>  
                            </init-param>  
                    </filter>  
                    <filter-mapping>  
                            <filter-name>CAS Filter</filter-name>  
                            <url-pattern>/*</url-pattern>  
                    </filter-mapping>  
                      
                      <!-- 该过滤器负责对Ticket的校验工作，必须启用它 -->  
                    <filter>  
                            <filter-name>CAS Validation Filter</filter-name>  
                            <filter-class>  
                                    org.jasig.cas.client.validation.Cas20ProxyReceivingTicketValidationFilter</filter-class>  
                            <init-param>  
                                    <param-name>casServerUrlPrefix</param-name>  
                                    <param-value>http://localhost:8080/cas</param-value>  
                            </init-param>  
                            <init-param>  
                                    <param-name>serverName</param-name>  
                                    <param-value>http://localhost:18080</param-value>  
                            </init-param>  
                    </filter>  
                    <filter-mapping>  
                            <filter-name>CAS Validation Filter</filter-name>  
                            <url-pattern>/*</url-pattern>  
                    </filter-mapping>  
                      
                  
                   <!--   该过滤器负责实现HttpServletRequest请求的包裹， -->  
                   <!--  比如允许开发者通过HttpServletRequest的getRemoteUser()方法获得SSO登录用户的登录名，可选配置。 -->  
                      
      
                    <filter>  
                            <filter-name>CAS HttpServletRequest Wrapper Filter</filter-name>  
                            <filter-class>  
                                    org.jasig.cas.client.util.HttpServletRequestWrapperFilter</filter-class>  
                    </filter>  
                    <filter-mapping>  
                            <filter-name>CAS HttpServletRequest Wrapper Filter</filter-name>  
                            <url-pattern>/*</url-pattern>  
                    </filter-mapping>  
                      
      
                 <!--  该过滤器使得开发者可以通过org.jasig.cas.client.util.AssertionHolder来获取用户的登录名。 -->  
                 <!--  比如AssertionHolder.getAssertion().getPrincipal().getName()。根据客户端获取的方式可以选择使用这两种 -->  
                      
      
                   <!--  <filter>  
                            <filter-name>CAS Assertion Thread Local Filter</filter-name>  
                            <filter-class>org.jasig.cas.client.util.AssertionThreadLocalFilter</filter-class>  
                    </filter>  
                    <filter-mapping>  
                            <filter-name>CAS Assertion Thread Local Filter</filter-name>  
                            <url-pattern>/*</url-pattern>  
                    </filter-mapping> -->  
                      
    <!-- ======================== 单点登录结束 ======================== -->  
      
       <servlet>  
           <servlet-name>HelloWorldExample</servlet-name>  
           <servlet-class>com.tgb.cas.client.HelloWorldExample</servlet-class>  
       </servlet>  
         
       <servlet-mapping>  
          <servlet-name>HelloWorldExample</servlet-name>  
          <url-pattern>/servlet/HelloWorldExample</url-pattern>  
      </servlet-mapping>  
    </web-app>  

```
#### HelloWorldExample：


```java
    public class HelloWorldExample extends HttpServlet {  
        private static final long serialVersionUID = 1L;  
      
        public void doGet(HttpServletRequest request, HttpServletResponse response)  
                throws IOException, ServletException {  
            response.setContentType("text/html");  
            PrintWriter out = response.getWriter();  
      
            out.println("<html>");  
            out.println("<head>");  
      
            String title = "Hello";  
      
            out.println("<title>" + title + "</title>");  
            out.println("</head>");  
            out.println("<body bgcolor=\"white\">");  
      
      
            out.println("<a href=\"../helloworld.html\">");  
            out.println("<img src=\"../images/code.gif\" height=24 "  
                    + "width=24 align=right border=0 alt=\"view code\"></a>");  
            out.println("<a href=\"../index.html\">");  
            out.println("<img src=\"../images/return.gif\" height=24 "  
                    + "width=24 align=right border=0 alt=\"return\"></a>");  
            out.println("<h1>" + title + "</h1>");  
            //以下是两种获取用户信息的两种方式，分别与Web.XML中的配置相对应，大家结合理解  
              
            // 通过 CAS HttpServletRequest Wrapper Filter 获取用户信息  
            String userNameString = request.getRemoteUser();  
              
            AttributePrincipal principal = (AttributePrincipal) request.getUserPrincipal();  
              
            if (null != principal) {  
                Map<String, Object> attMap = principal.getAttributes();  
                out.println(" Log | getAttributes Map size = " + attMap.size() + "<br>");  
                for (Entry<String, Object> entry : attMap.entrySet()) {  
                    out.println("     | " + entry.getKey() + "=:" + entry.getValue() + "<br>");  
                }         
                String username = null;  
                out.print(" Log | UserName:");  
                if (null != principal) {  
                    username = principal.getName();  
                    out.println("<span style='color:red;'>" + username + "</span><br>");  
                }  
            }  
              
              
              
            // 通过CAS Assertion Thread Local Filter  获取用户信息，共两种方式  
    //      Assertion assertion = (Assertion) request.getSession().getAttribute(  
    //              AbstractCasFilter.CONST_CAS_ASSERTION);  
    //      if (null != assertion) {  
    //            
    //          Map<String, Object> attMap = assertion.getPrincipal().getAttributes();  
    //          out.println(" Log | getAttributes Map size = " + attMap.size() + "<br>");  
    //          for (Entry<String, Object> entry : attMap.entrySet()) {  
    //              out.println("     | " + entry.getKey() + "=:" + entry.getValue() + "<br>");  
    //          }  
    //            
    //          AttributePrincipal principal = assertion.getPrincipal();  
    //          // AttributePrincipal principal = (AttributePrincipal) request  
    //          // .getUserPrincipal();  
    //          String username = null;  
    //          out.print(" Log | UserName:");  
    //          if (null != principal) {  
    //              username = principal.getName();  
    //              out.println("<span style='color:red;'>" + username + "</span><br>");  
    //          }  
    //      }  
      
            out.println("</body>");  
            out.println("</html>");  
        }  
    }  

```
#### userInfoView.jsp（未使用）：


```jsp
    <%@ page language="java" contentType="text/html; charset=UTF-8"  
        pageEncoding="UTF-8"%>  
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">  
    <html>  
    <head>  
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">  
    <title>Insert title here</title>  
    </head>  
    <body>  
        <a href="servlet/HelloWorldExample">获取Server端用户信息</a>  
        <br>  
        <a href="http://localhost:8080/cas/logout?service=http://localhost:18080/cas_client/servlet/HelloWorldExample">单点退出</a>  
    </body>  
    </html>  

```
访问：http://localhost:18080/cas_client/servlet/HelloWorldExample 查看效果

其中cas服务端对应的代码是官网中提供的cas-server-webapp-3.5.2.war，而cas_client客户端代码是我们自己编写的，参照tomcat中的example项目。

# 附录：

## JS实现cas登录成功跳转其他页面 

```JS
<!-- 根据url跳转到不同系统的登录界面. -->  
<script language="javascript"  type="text/javascript">   
  
window.onload=function()//用window的onload事件，窗体加载完毕的时候  
{  
   //do something  
   var result = location.search.match(new RegExp("[\?\&]" + 'systemId'+ "=([^\&]+)","i"));    
    if(result ==  || result.length < 1){    
     //return "";    
     result ="";  
    }    
  //alert(result[1]);  
 //return result[1];  
  
    if("security"==result[1]){  
        //alert("dfdfsdfasdfasdfasdf");  
        window.location.href="https://www.xx.com:8443/cas/login?service=http%3A%2F%2F172.16.3.199%3A8080%2Fsecurity%2F";  
          
    }else{  
        window.location.href="https://www.xx.com:8443/cas/login?service=http%3A%2F%2F172.16.3.199%3A8080%2Fvms2.0%2Fuser%2FtoMain%2F";  
    }  
}  
<!--封装为函数,也可以直接调用.-->   
 function getQueryStringByName(name) {    
       
    var result = location.search.match(new RegExp("[\?\&]" + name+ "=([^\&]+)","i"));    
    if(result ==  || result.length < 1){    
     return "";    
    }    
 alert(result[1]);  
 return result[1];    
 }  
</script>  
```
