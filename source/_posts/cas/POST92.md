---
title: CAS单点登录（二）
tags: CAS
category: CAS
abbrlink: 54220
date: 2018-01-09 21:34:36
---
![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0082.jpg)

Cas的全称是Centeral Authentication Service，是对单点登录SSO（Single Sign On）的一种实现。其由Cas Server和Cas Client两部分组成，Cas Server是核心，而Cas Client通常就对应于我们的应用。一个Cas Server可以对应于多个Cas Client。它允许我们在一个Client进行登录以后无需再让用户输入用户名和密码进行认证即可访问其它Client应用。
<!--more-->
# CAS简介

Cas Server的主要作用是通过发行和验证Ticket（票）来对用户进行认证和授权访问Client应用，用于认证的凭证信息都是由Cas Server管理的。而Cas Client就对应于我们真正的应用，当然其中会使用到Cas相关的类，用于与Cas Server进行交互。官网有两张图最能体现Cas的架构和原理。
 
![image](http://ovi3ob9p4.bkt.clouddn.com/cas/CAS008.jpg)
 
![image](http://ovi3ob9p4.bkt.clouddn.com/cas/CAS009.jpg)

如你所见，在第一次访问应用app1时，由于没有登录会直接跳转到Cas Server去进行登录认证，此时将附带查询参数service在Cas Server的登录地址上，表示登录成功后将要跳转的地址。此时Cas Server检查到没有之前成功登录后生成的SSO Session信息，那么就会引导用户到登录页面进行登录。用户输入信息提交登录请求，Cas Server认证成功后将生成对应的SSO Session，以及名为CASTGC的cookie，该cookie包含用来确定用户SSO Session的Ticket Granting Ticket（TGT）。之后会生成一个Service Ticket（ST），并将以ticket作为查询参数名，以该ST作为查询参数值跳转到登录时service对应的URL。如：

 http(s)://domain/app1?ticket=ST-2-59fS6KxvmykibRXyoPJE

之后的操作对用户来说都是透明的，即不可见的。app1之后将以service和ticket作为查询参数请求Cas Server对service进行验证，验证通过后Cas Server将返回当前用户的用户名等信息。app1就会给当前用户生成其自身的Session，以后该用户以该Session都可以成功的访问app1，而不需要再去请求Cas Server进行认证。当该用户再去访问app2的时候，由于其在app2上没有对应的Session信息，将会跳转到Cas Server的登录地址，Cas Server此时发现其包含名为CASTGC的cookie，将获取其中包含的TGT来获取对应的SSO Session，然后会将用户重定向到app2对应的地址，以Service Ticket作为查询参数。之后app2会向Cas Server发送请求校验该Service Ticket，校验成功后app2将建立该用户对应的Session信息，以后该用户以该Session就可以自由的访问app2了。

综上所述，我们知道，各系统之间的单点登录是通过Cas Server生成的SSO Session来交流的，而用户与实际的应用系统进行交互的时候，各应用系统将建立单独的Session，以满足用户与该系统的交互需求。

（注：本文是基于cas 3.5.2所写）

# 部署Cas Server

Cas应用都需要有一个Cas Server。Cas Server是基于Java Servlet实现的，其要求部署在Servlet2.4以上版本的Web容器中。在此笔者将其部署到tomcat7中。Cas Server是要求使用https协议进行访问的，所以如果你的Web容器没有开启https通道，则需先开启。

## 开启tomcat7的https通道
官方文档在这里http://tomcat.apache.org/tomcat-7.0-doc/ssl-howto.html。简单来说具体步骤如下：

1. 利用Java的keytool工具创建一个keystore文件，该文件保存了服务端的证书和私钥。打开命令窗口敲入如下命令：
      
```
%JAVA_HOME%\bin\keytool -genkey -alias tomcat -keyalg RSA
```
2. 根据提示输入密码等信息，笔者选择的是“password”，该密码稍后将用于配置tomcat。

3. 打开tomcat安装目录conf/server.xml文件，将如下语句的注释打开，并加上属性keystorePass，密码为我们在建立keystore时设立的密码。这样我们就可以使用https来访问tomcat了。默认其会到${user.home}/.keystore寻找keystore文件，我们之前建立的keystore文件默认就在这个位置。

```XML
<Connector port="8443" protocol="org.apache.coyote.http11.Http11Protocol" SSLEnabled="true"  
           maxThreads="150" scheme="https" secure="true"  
           clientAuth="false" sslProtocol="TLS"  
                    keystorePass="password"/>
```

## 部署Cas Server
从官网下载的cas server中会含有一个cas server对应的war包，对应路径为cas-server-3.5.2\modules\cas-server-webapp-3.5.2.war，我们可以把该war包重命名为cas.war，然后丢到tomcat的webapps目录下。之后我们就可以启动tomcat，然后访问https://localhost:8443/cas/login就可以看到Cas的登录页面了。

这些UI如果不想用，都是可以进行修改，自定义的。敲入用户名和密码就可以进行登录了。默认使用的AuthenticationHandler的处理逻辑是，只要用户名和密码一致就可以登录成功。

# Cas Server中各配置文件介绍

 Cas Server中所有的配置文件都是放在WEB-INF目录及其子目录下的。

## 在WEB-INF/classes下的配置文件有：

-  cas-theme-default.properties：该文件存放的是css文件的路径，用户可以在这里指定使用的css文件为自定义的css文件，也可以更改配置文件中对应的css文件已更改默认的样式。
-  系列messages_*.properties：国际化支持对应的资源文件。
-  default_views.properties和protocol_views.properties：Cas Server中使用到的视图配置文件，用于定义对应视图的处理类及其文件路径。
-  log4j.xml：log4j的配置文件。

## 在WEB-INF根目录下的配置文件有：

-  web.xml：这个文件就是大伙熟悉的web.xml文件。
-  cas-servlet.xml：该文件是Cas Server使用到的Spring MVC的配置文件，里面定义了一些Controller及View相关的内容。
-  cas.properties：该文件是Cas Server配置的主要文件。与Spring相关的所有配置使用的外部属性都定义在这个文件中。
-  deployerConfigContext.xml：该文件是专为Cas Server部署人员抽取出来的一个文件，其中的内容通常都是Cas Server部署人员需要进行更改的内容。
-  login-webflow.xml：登录使用到的Spring Web Flow配置文件。
-  restlet-servlet.xml：使用到的Restful API的相关配置。
-  WEB-INF/spring-configuration：该目录中存放的是Cas Server加载的Spring相关的配置文件。
-  WEB-INF/unused-spring-configuration：该目录中存放的是没有用到，但是有需要时可以将它们copy到spring-configuration目录下的文件。

## 在WEB-INF/spring-configuration目录下的配置文件是Cas Server中使用到的所有与Spring相关的配置文件，具体有：

-  applicationContext.xml：该文件中定义了主要使用的bean，通常不需要修改。
-  argumentExtractorsConfiguration.xml：该文件中定义了需要使用的协议，默认只有CAS和SAML是可用的。
-  log4jConfiguration.xml：log4j初始化对应的配置。
-  propertyFileConfigure.xml：用于指定外部属性文件的位置，默认指定的就是前面提到的cas.properties文件。
-  securityContext.xml：这是Cas Server中使用到的Spring Security相关内容的配置文件，默认不需要进行修改。
-  ticketExpirationPolicies.xml：用于指定TicketGranting Ticket和Service Ticket的过期策略，一般不需要修改。
-  ticketGrantingTicketCookieGenerator.xml：TicketGranting Ticket对应的CookieGenerator的配置。
-  ticketRegistry.xml：DefaultTicketRegistry对应的配置。
-  uniqueIdGenerators.xml：唯一主键生成相关的配置。
-  warnCookieGenerator.xml：用于生成警告的CookieGenerator对应配置。

# Cas（04）——更改认证方式

在Cas Server的WEB-INF目录下有一个deployerConfigContext.xml文件，该文件是基于Spring的配置文件，里面存放的内容常常是部署人员需要修改的内容。其中认证方式也是定义在这个文件中的，id为authenticationManager的bean的authenticationHandlers即定义了需要使用的AuthenticationHandler列表。默认使用了两个AuthenticationHandler，第一个是用来确保当前使用的是https协议的HttpBasedServiceCredentialsAuthenticationHandler，第二个是我们需要改的，其简单认证用户名与密码相等的SimpleTestUsernamePasswordAuthenticationHandler。我们只需要更改这里的SimpleTestUsernamePasswordAuthenticationHandler就行了。Cas中已经为我们提供了不少AuthenticationHandler的实现，包括基于数据库认证的实现，当然用户也可以实现自己的AuthenticationHandler。下面将以使用数据库进行认证为例讲解如何更改认证方式。

Cas的各个模块都是基于Maven开发的，Cas Server也不例外。所以官方推荐我们使用Maven的War覆盖机制来修改Cas Server的配置文件。Maven的War覆盖机制是指当一个package类型为war的Maven项目A中引入了一个package类型为war的项目B作为依赖时，最终项目A打包的war包中不仅会包含项目A的内容，还会包含项目B的内容，且相同位置的文件项目A中的会覆盖项目B中的，即当项目A和项目B在WEB-INF下都拥有一个web.xml文件时，最终生成的war包中将使用项目A在WEB-INF下的web.xml文件；而当项目A在WEB-INF下没有web.xml文件，而项目B在WEB-INF下拥有web.xml文件时最终生成的war包中将使用项目B在WEB-INF下的web.xml文件。所以如果我们要修改Cas Server的配置文件，我们可以建立一个自己的基于Maven的Web项目，然后引入Cas Server作为依赖，并在自己的项目中建立对应的deployerConfigContext.xml文件。下面来看详细步骤。

 
1. 建立基于Maven的Web项目，取名为myCasServer。
2. 打开pom.xml文件，删除默认的依赖项，添加如下内容。

```xml
<build>
      <plugins>
         <plugin>
            <artifactId>maven-war-plugin</artifactId>
            <configuration>
                <warName>cas</warName>
            </configuration>
         </plugin>
      </plugins>
   </build>
   <dependencies>
      <dependency>
         <groupId>org.jasig.cas</groupId>
         <artifactId>cas-server-webapp</artifactId>
         <version>${cas.version}</version>
         <type>war</type>
         <scope>runtime</scope>
      </dependency>
   </dependencies>
   <properties>
      <cas.version>3.5.2</cas.version>
   </properties>
   <repositories>
      <repository>
         <id>ja-sig</id>
         <url>http://oss.sonatype.org/content/repositories/releases/ </url>
      </repository>
   </repositories>
```
3. 删除myCasServer项目中src/main/webapp下的index.jsp文件和src/main/webapp/WEB-INF下的web.xml文件，因为在cas-server-webapp中都存在对应的文件，不删除的话打包后的结果将是myCasServer中的覆盖cas-server-webapp中的。如果这个时候使用Maven进行打包的话你将得到一个和cas-server-webapp一模一样的war包。

4. 使用数据库进行认证的话还需要添加对应的依赖，打开myCasServer的pom.xml文件，添加如下依赖。

```xml
<dependency>
         <groupId>org.jasig.cas</groupId>
         <artifactId>cas-server-support-jdbc</artifactId>
         <version>${cas.version}</version>
         <scope>runtime</scope>
      </dependency>
```

5. 将cas-server-webapp/WEB-INF下的deployerConfigContext.xml文件copy到myCasServer的src/main/webapp/WEB-INF目录下。
6. 基于数据库的AuthenticationHandler有多种，这里以QueryDatabaseAuthenticationHandler为例。QueryDatabaseAuthenticationHandler需要配置两个参数，dataSource和sql。dataSource就是数据源，表示从哪个数据源进行查询。sql是对应的查询语句，其会接收username作为参数，然后查询出对应的密码，之后QueryDatabaseAuthenticationHandler会将查询出来的密码与用户提交的密码进行匹配。所以这里我们打开复制到myCasServer中的deployerConfigContext.xml文件，找到id为authenticationManager的bean的authenticationHandlers属性定义，将最后一个AuthenticationHandler替换成我们想要的QueryDatabaseAuthenticationHandler。

 替换前：

```xml
<property name="authenticationHandlers">
         <list>
            <beanclass="org.jasig.cas.authentication.handler.support.HttpBasedServiceCredentialsAuthenticationHandler"
                p:httpClient-ref="httpClient" />
            <bean
           class="org.jasig.cas.authentication.handler.support.SimpleTestUsernamePasswordAuthenticationHandler"/>
         </list>
      </property>
```

 替换后：

```xml
<property name="authenticationHandlers">
         <list>
            <beanclass="org.jasig.cas.authentication.handler.support.HttpBasedServiceCredentialsAuthenticationHandler"
                p:httpClient-ref="httpClient" />
            <beanclass="org.jasig.cas.adaptors.jdbc.QueryDatabaseAuthenticationHandler">
                <property name="dataSource" ref="dataSource"/>
                <property name="sql" value="select password from t_user where username = ?"/>
            </bean>
         </list>
      </property>
```

像dataSource的定义及其需要使用到的依赖包我就不贴了，比较常用。

打包以后生成的war包中使用的AuthenticationHandler就会是我们在myCasServer的src/main/webapp/WEB-INF目录下的deployerConfigContext.xml文件中定义的QueryDatabaseAuthenticationHandler了。以后需要修改Cas Server中的其它内容也可以依照此种方式进行修改。
 
# 修改Cas Server的其它配置

## 修改host.name

host.name是定义在cas.properties文件中的一个属性。该属性将被定义在uniqueIdGenerators.xml文件中的各种UniqueTicketIdGenerator用来生成TGT、ST等ticket。默认在生成这些ticket时会将host.name作为对应ticket的后缀。host.name的设置在集群环境下将非常有用，其值对于每个节点来说都必须是唯一的，这样在整个集群环境下生成出的各种ticket也必定是唯一的。当然，如果只是在单机环境下使用，我们也可以不修改它。

## 修改SSO Session的超时策略

SSO Session的超时策略是由TicketGrantingTicketExpirationPolicy来描述的，其定义在ticketExpirationPolicies.xml文件中的。以下是其定义：

    
```xml
<!-- Provides both idle and hard timeouts, for instance 2 hour sliding window with an 8 hour max lifetime -->
    <bean id="grantingTicketExpirationPolicy"class="org.jasig.cas.ticket.support.TicketGrantingTicketExpirationPolicy"
          p:maxTimeToLiveInSeconds="${tgt.maxTimeToLiveInSeconds:28800}"
          p:timeToKillInSeconds="${tgt.timeToKillInSeconds:7200}"/>
```

我们可以看到它需要指定两个参数，maxTimeToLiveInSeconds和timeToKillInSeconds，它们的单位都是秒。其中timeToKillInSeconds表示用户在多久不进行操作的情况下将超时，maxTimeToLiveInSeconds表示SSO Session的最大有效时间，从生成到指定时间后就将超时。timeToKillInSeconds默认是7200秒，即2小时；maxTimeToLiveInSeconds默认是28800秒，即8小时。用户如果需要修改的话可以之前介绍的Maven War覆盖的方式在ticketExpirationPolicies.xml文件中修改，也可以在cas.properties文件中通过对应的属性进行修改。

## 修改允许管理service的角色

有的时候我们需要查看或管理Cas Server端的service注册情况。这就需要我们在cas.properties文件中指定用户访问service管理页面需要的角色，这是通过cas.securityContext.serviceProperties.adminRoles来指定的，默认是ROLE_ADMIN。之后我们就可以通过访问“/services/manage.html”来对Cas Server上注册的service进行管理了。在访问该URL时可能会出现国际化时某信息在zh_CN中找不到的问题，解决方法可以将对应的信息在messages_zh_CN.properties中加上，或者将messages_en.properties文件copy一份重命名为messages.properties，英文版本的messages相对而言应该是比较全的。Cas Server的权限控制也是基于Spring Security的，其主要配置文件是WEB-INF/spring目录下的securityContext.xml文件，关于权限相关的内容可以在这里查找。

## 修改logout后的重定向

默认情况下，Cas客户端应用通过Cas Server的logout登出后是会展示Cas Server的默认登出结果页面的。如果用户希望在Cas Server登出后能够跳转到自己的应用来，则可以在访问Cas Server的logout时通过参数service传递登出后需要跳转的地址。有一点需要注意的是指定的service需要是在Cas Server注册过的，我们可以通过访问Cas Server的“/services/manage.html”对service进行管理，包括增、删、改、查等。另外我们还需要启用Cas Server在logout后的重定向功能，该功能默认是不启用的。通过去掉cas.properties文件中属性cas.logout.followServiceRedirects=false前面的注释，并修改其值为true可以启用logout后的重定向了。

## 禁用logout后的回调

默认情况下在通过访问Cas Server的logout进行登出后Cas Server将依次回调其中注册的各个Cas Client应用，以进行单点登出。如果用户不希望Cas Server在logout后回调Cas Client应用，则可以通过设置cas.properties文件中的slo.callbacks.disabled=true来禁用其回调。

## 修改service ticket的超时时间

service ticket的默认有效时间是10秒。通常来说这已经足够了，因为从Cas Server生成service ticket返回给Cas client应用，到Cas client应用发送service ticket到Cas Server进行验证这个过程10秒已经足够。这也是为什么如果我们使用debug追踪Cas应用的认证过程时经常会失败的原因，因为追踪的时候service ticket已经过了10秒的有效期了。如果用户需要修改它可以通过cas .properties文件中的st.timeToKillInSeconds属性进行修改。

# 基于数据库的认证

Cas Server自身已经为我们实现了几种基于JDBC的AuthenticationHandler实现，但它们不包含在Cas Server的核心包里面，而是包含在cas-server-support-jdbc中，如果我们要使用Cas Server已经实现好的基于JDBC的AuthenticationHandler，我们必须先将cas-server-support-jdbc对应的jar包、相关数据库的驱动，以及所需要使用的数据源实现等jar包加入Cas Server的类路径中。如果是基于Maven的war覆盖机制来修改Cas Server的配置文件，则我们可以在自己的Maven项目的依赖中加入如下项（对应的驱动就没贴出来了）。

      
```xml
<dependency>
         <groupId>org.jasig.cas</groupId>
         <artifactId>cas-server-support-jdbc</artifactId>
         <version>${cas.version}</version>
         <scope>runtime</scope>
      </dependency>
```

Cas Server默认已经实现好的基于JDBC的AuthenticationHandler有三个，它们都继承自AbstractJdbcUsernamePasswordAuthenticationHandler，而且在认证过程中都需要一个DataSource。下面来对它们做一个简要的介绍。

## BindModeSearchDatabaseAuthenticationHandler

BindModeSearchDatabaseAuthenticationHandler将试图以传入的用户名和密码从配置的DataSource中建立一个连接，如果连接成功，则表示认证成功，否则就是认证失败。以下是BindModeSearchDatabaseAuthenticationHandler源码的一段主要代码，通过它我们可以明显的看清其逻辑：
    
```java
protected final boolean authenticateUsernamePasswordInternal(
        final UsernamePasswordCredentials credentials)
        throws AuthenticationException {
        final String username = credentials.getUsername();
        final String password = credentials.getPassword();
        try {
            final Connection c = this.getDataSource()
               .getConnection(username, password);
            DataSourceUtils.releaseConnection(c, this.getDataSource());
            returntrue;
        } catch (final SQLException e) {
            returnfalse;
       }
    }
```

当然，这种实现也需要你的DataSource支持getConnection(user,password)才行，否则将返回false。dbcp的BasicDataSource的不支持的，而c3p0的ComboPooledDataSource支持。

以下是一个使用BindModeSearchDatabaseAuthenticationHandler的配置示例：

   
```xml
<bean id="authenticationManager"
      class="org.jasig.cas.authentication.AuthenticationManagerImpl">
      ...
      <property name="authenticationHandlers">
         <list>
            ...
            <beanclass="org.jasig.cas.adaptors.jdbc.BindModeSearchDatabaseAuthenticationHandler">
               <property name="dataSource" ref="dataSource"/>
            </bean>
            ...
         </list>
      </property>
      ...
   </bean>
```
## QueryDatabaseAuthenticationHandler

使用QueryDatabaseAuthenticationHandler需要我们指定一个SQL，该SQL将接收一个用户名作为查询条件，然后返回对应的密码。该SQL将被QueryDatabaseAuthenticationHandler用来通过传入的用户名查询对应的密码，如果存在则将查询的密码与查询出来的密码进行匹配，匹配结果将作为认证结果。如果对应的用户名不存在也将返回false。

以下是QueryDatabaseAuthenticationHandler的一段主要代码：

    
```java
protected final boolean authenticateUsernamePasswordInternal(finalUsernamePasswordCredentials credentials) throws AuthenticationException {
        final String username = getPrincipalNameTransformer().transform(credentials.getUsername());

        final String password = credentials.getPassword();

        final String encryptedPassword = this.getPasswordEncoder().encode(

            password);

        try {

            final String dbPassword = getJdbcTemplate().queryForObject(this.sql, String.class, username);

            return dbPassword.equals(encryptedPassword);

        } catch (final IncorrectResultSizeDataAccessException e) {

            // this means the username was not found.

            returnfalse;

        }
    }
```

上面的逻辑非常明显。此外，如你所见，QueryDatabaseAuthenticationHandler使用的用户名会经过PrincipalNameTransformer进行转换，而密码会经过PasswordEncoder进行编码。Cas Server中基于JDBC的AuthenticationHandler实现中使用到的PrincipalNameTransformer默认是不进行任何转换的NoOpPrincipalNameTransformer，而默认使用的PasswordEncoder也是不会经过任何编码的PlainTextPasswordEncoder。当然了，cas-server-jdbc-support对它们也有另外两种支持，即PrefixSuffixPrincipalNameTransformer和DefaultPasswordEncoder。

### PrefixSuffixPrincipalNameTransformer

PrefixSuffixPrincipalNameTransformer的作用很明显，如其名称所描述的那样，其在转换时会将用户名加上指定的前缀和后缀。所以用户在使用的时候需要指定prefix和suffix两个属性，默认是空。

### DefaultPasswordEncoder

DefaultPasswordEncoder底层使用的是标准Java类库中的MessageDigest进行加密的，其支持MD5、SHA等加密算法。在使用时需要通过构造参数encodingAlgorithm来指定使用的加密算法，可以使用characterEncoding属性注入来指定获取字节时使用的编码，不指定则使用默认编码。以下是DefaultPasswordEncoder的源码，其展示了DefaultPasswordEncoder的加密逻辑。


```java
public final class DefaultPasswordEncoder implements PasswordEncoder {
    privatestaticfinalchar[] HEX_DIGITS = {'0', '1', '2', '3', '4', '5', '6', '7', '8','9', 'a', 'b', 'c', 'd', 'e', 'f'};
    @NotNull
    privatefinal String encodingAlgorithm;
    private String characterEncoding;
    public DefaultPasswordEncoder(final String encodingAlgorithm) {
        this.encodingAlgorithm = encodingAlgorithm;
    }
    public String encode(final String password) {

        if (password == null) {

            returnnull;

        }
        try {

            MessageDigest messageDigest = MessageDigest.getInstance(this.encodingAlgorithm);
            if (StringUtils.hasText(this.characterEncoding)) {

                messageDigest.update(password.getBytes(this.characterEncoding));

            } else {

                messageDigest.update(password.getBytes());
            }
            finalbyte[] digest = messageDigest.digest();
            return getFormattedText(digest);
        } catch (final NoSuchAlgorithmException e) {
            thrownew SecurityException(e);
        } catch (final UnsupportedEncodingException e) {
            thrownew RuntimeException(e);
        }
    }
    
    /**
     * Takes the raw bytes from the digest and formats them correct.
     * @param bytes the raw bytes from the digest.
     * @return the formatted bytes.
     */

    private String getFormattedText(byte[] bytes) {
        final StringBuilder buf = new StringBuilder(bytes.length * 2);

        for (int j = 0; j < bytes.length; j++) {
            buf.append(HEX_DIGITS[(bytes[j] >> 4) & 0x0f]);
            buf.append(HEX_DIGITS[bytes[j] & 0x0f]);
        }
        return buf.toString();
    }
    publicfinalvoid setCharacterEncoding(final String characterEncoding) {
       this.characterEncoding = characterEncoding;
    }
}
```
如果在认证时需要使用DefaultPasswordEncoder，则需要确保数据库中保存的密码的加密方式和DefaultPasswordEncoder的加密算法及逻辑是一致的。如果这些都不能满足你的需求，则用户可以实现自己的PrincipalNameTransformer和PasswordEncoder。

以下是一个配置使用QueryDatabaseAuthenticationHandler进行认证，且使用DefaultPasswordEncoder对密码进行MD5加密的示例：

  
```xml
<bean id="authenticationManager"
      class="org.jasig.cas.authentication.AuthenticationManagerImpl">
      ...
      <property name="authenticationHandlers">

         <list>
            ...
            <beanclass="org.jasig.cas.adaptors.jdbc.QueryDatabaseAuthenticationHandler">
                <property name="dataSource" ref="dataSource"/>
                <property name="passwordEncoder" ref="passwordEncoder"/>
                <property name="sql" value="select password from t_user where username = ?"/>
            </bean>
            ...

         </list>

      </property>

      ...

   </bean>
   <bean id="passwordEncoder"class="org.jasig.cas.authentication.handler.DefaultPasswordEncoder">
      <constructor-arg value="MD5"/><!-- 加密算法 -->
   </bean>
```
## SearchModeSearchDatabaseAuthenticationHandler

SearchModeSearchDatabaseAuthenticationHandler的主要逻辑是将传入的用户名和密码作为条件从指定的表中进行查询，如果对应记录存在则表示认证通过。使用该AuthenticationHandler时需要我们指定查询时使用的表名（tableUsers）、用户名对应的字段名（fieldUser）和密码对应的字段名（fieldPassword）。此外，还可以选择性的使用PrincipalNameTransformer和PasswordEncoder。以下是SearchModeSearchDatabaseAuthenticationHandler源码中的一段主要代码：


```java
private static final String SQL_PREFIX = "Select count('x') from ";
    @NotNull
    private String fieldUser;
    
    @NotNull
    private String fieldPassword;

    @NotNull
    private String tableUsers;

    private String sql;

    protectedfinalboolean authenticateUsernamePasswordInternal(finalUsernamePasswordCredentials credentials) throws AuthenticationException {

        final String transformedUsername = getPrincipalNameTransformer().transform(credentials.getUsername());
        final String encyptedPassword = getPasswordEncoder().encode(credentials.getPassword());

        finalint count = getJdbcTemplate().queryForInt(this.sql,

           transformedUsername, encyptedPassword);
        return count > 0;
    }

    publicvoid afterPropertiesSet() throws Exception {

        this.sql = SQL_PREFIX + this.tableUsers + " Where " + this.fieldUser

        + " = ? And " + this.fieldPassword + " = ?";

    }
```

以下是一个使用SearchModeSearchDatabaseAuthenticationHandler的配置示例：

  
```xml
<bean id="authenticationManager"
      class="org.jasig.cas.authentication.AuthenticationManagerImpl">

      ...

      <property name="authenticationHandlers">

         <list>
            ...

            <beanclass="org.jasig.cas.adaptors.jdbc.SearchModeSearchDatabaseAuthenticationHandler">

                <property name="dataSource" ref="dataSource"/>

                <property name="passwordEncoder" ref="passwordEncoder"/>

                <property name="tableUsers" value="t_user"/><!-- 指定从哪个用户表查询用户信息 -->

                <property name="fieldUser" value="username"/><!-- 指定用户名在用户表对应的字段名 -->

                <property name="fieldPassword" value="password"/><!-- 指定密码在用户表对应的字段名 -->

            </bean>

            ...

         </list>

      </property>

      ...

   </bean>
```
 
至此，cas-server-support-jdbc中支持JDBC的三个AuthenticationHandler就讲完了。如果用户觉得它们都不能满足你的要求，则还可以选择使用自己实现的AuthenticationHandler。至于其它认证方式，请参考官方文档。

# 建立使用Cas进行单点登录的应用

根据之前的描述我们知道，Cas由两部分组成，Cas Server和Cas Client。Cas Server是Cas自己的服务端，而Cas Client是Cas客户端，其需要与我们自己的应用进行集成。

## 加入cas-client-core-xxx.jar到classpath

在我们下载的Cas Client压缩包的modules目录下可以找到一个名为cas-client-core-xxx.jar的jar文件，首先需要将该jar包加入我们应用的类路径下，笔者这里使用的是cas-client-core-3.1.11.jar。如果用户的应用是使用Maven构造的，则可以在应用的pom.xml文件中加入如下依赖。
  
```xml
<dependency>
      <groupId>org.jasig.cas.client</groupId>

      <artifactId>cas-client-core</artifactId>

      <version>3.1.11</version>
   </dependency>
```
## 配置Filter

然后需要我们在应用的web.xml文件中配置四个Filter，这四个Filter必须按照固定的顺序来进行配置，而且它们必须配置在应用的其它Filter之前。它们的先后顺序要求如下：

- AuthenticationFilter
- TicketValidationFilter
- HttpServletRequestWrapperFilter
- AssertionThreadLocalFilter

这些Filter有的必须指定某些参数，有的可以指定某些参数，这些参数可以通过context-param来指定，也可以通过init-param来指定。Cas Client默认会先从init-param取，没取到则从context-param取，所以当init-param和context-param都指定了某个参数时，init-param指定的将拥有更高的优先级。所以当多个Filter需要共用一个参数时，我们可以把它定义为context-param。

### AuthenticationFilter

AuthenticationFilter用来拦截所有的请求，用以判断用户是否需要通过Cas Server进行认证，如果需要则将跳转到Cas Server的登录页面。如果不需要进行登录认证，则请求会继续往下执行。

AuthenticationFilter有两个用户必须指定的参数，一个是用来指定Cas Server登录地址的casServerLoginUrl，另一个是用来指定认证成功后需要跳转地址的serverName或service。service和serverName只需要指定一个就可以了。当两者都指定了，参数service将具有更高的优先级，即将以service指定的参数值为准。service和serverName的区别在于service指定的是一个确定的URL，认证成功后就会确切的跳转到service指定的URL；而serverName则是用来指定主机名，其格式为{protocol}:{hostName}:{port}，如：https://localhost:8443，当指定的是serverName时，AuthenticationFilter将会把它附加上当前请求的URI，以及对应的查询参数来构造一个确定的URL，如指定serverName为“http://localhost”，而当前请求的URI为“/app”，查询参数为“a=b&b=c”，则对应认证成功后的跳转地址将为“http://localhost/app?a=b&b=c”。

除了上述必须指定的参数外，AuthenticationFilter还可以指定如下可选参数：

- renew：当指定renew为true时，在请Cas Server时将带上参数“renew=true”，默认为false。
- gateway：指定gateway为true时，在请求Cas Server时将带上参数“gateway=true”，默认为false。
- artifactParameterName：指定ticket对应的请求参数名称，默认为ticket。
- serviceParameterName：指定service对应的请求参数名称，默认为service。

如下是一个配置AuthenticationFilter的示例，serverName由于在接下来配置的Filter中还要用，所以利用context-param将其配置为一个公用的参数。“elim”对应我的电脑名。

```xml
<context-param>
      <param-name>serverName</param-name>
      <param-value>http://elim:8080</param-value>
   </context-param>
   
   <filter>
      <filter-name>casAuthenticationFilter</filter-name>
   <filter-class>org.jasig.cas.client.authentication.AuthenticationFilter</filter-class>
      <init-param>
         <param-name>casServerLoginUrl</param-name>
         <param-value>https://elim:8443/cas/login</param-value>
      </init-param>
   </filter>
   
   <filter-mapping>
      <filter-name>casAuthenticationFilter</filter-name>
      <url-pattern>/*</url-pattern>
   </filter-mapping>
```

### TicketValidationFilter

在请求通过AuthenticationFilter的认证之后，如果请求中携带了参数ticket则将会由TicketValidationFilter来对携带的ticket进行校验。TicketValidationFilter只是对验证ticket的这一类Filter的统称，其并不对应Cas Client中的一个具体类型。Cas Client中有多种验证ticket的Filter，都继承自AbstractTicketValidationFilter，它们的验证逻辑都是一致的，都有AbstractTicketValidationFilter实现，所不同的是使用的TicketValidator不一样。笔者这里将以Cas10TicketValidationFilter为例，其它还有Cas20ProxyReceivingTicketValidationFilter和Saml11TicketValidationFilter。

   
```xml
<filter>
      <filter-name>casTicketValidationFilter</filter-name>
   <filter-class>org.jasig.cas.client.validation.Cas10TicketValidationFilter</filter-class>
      <init-param>
         <param-name>casServerUrlPrefix</param-name>
         <param-value>https://elim:8443/cas</param-value>
      </init-param>
   </filter>

   <filter-mapping>
      <filter-name>casTicketValidationFilter</filter-name>
      <url-pattern>/*</url-pattern>
   </filter-mapping>
```

必须指定的参数：

- casServerUrlPrefix：用来指定Cas Server对应URL地址的前缀，如上面示例的“https://elim:8443/cas”。
- serverName或service：语义跟前面介绍的一致。

可选参数：

- redirectAfterValidation ：表示是否验证通过后重新跳转到该URL，但是不带参数ticket，默认为true。
- useSession ：在验证ticket成功后会生成一个Assertion对象，如果useSession为true，则会将该对象存放到Session中。如果为false，则要求每次请求都需要携带ticket进行验证，显然useSession为false跟redirectAfterValidation为true是冲突的。默认为true。
- exceptionOnValidationFailure ：表示ticket验证失败后是否需要抛出异常，默认为true。
- renew：当值为true时将发送“renew=true”到Cas Server，默认为false。

### HttpServletRequestWrapperFilter

HttpServletRequestWrapperFilter用于将每一个请求对应的HttpServletRequest封装为其内部定义的CasHttpServletRequestWrapper，该封装类将利用之前保存在Session或request中的Assertion对象重写HttpServletRequest的getUserPrincipal()、getRemoteUser()和isUserInRole()方法。这样在我们的应用中就可以非常方便的从HttpServletRequest中获取到用户的相关信息。以下是一个配置HttpServletRequestWrapperFilter的示例：

  
```xml
<filter>
      <filter-name>casHttpServletRequestWrapperFilter</filter-name>
   <filter-class>org.jasig.cas.client.util.HttpServletRequestWrapperFilter</filter-class>
   </filter>
   
   <filter-mapping>
      <filter-name>casHttpServletRequestWrapperFilter</filter-name>
      <url-pattern>/*</url-pattern>
   </filter-mapping>
```

### AssertionThreadLocalFilter

AssertionThreadLocalFilter是为了方便用户在应用的其它地方获取Assertion对象，其会将当前的Assertion对象存放到当前的线程变量中，那么以后用户在程序的任何地方都可以从线程变量中获取当前Assertion，无需再从Session或request中进行解析。该线程变量是由AssertionHolder持有的，我们在获取当前的Assertion时也只需要通过AssertionHolder的getAssertion()方法获取即可，如：

```java
Assertion assertion = AssertionHolder.getAssertion();
```

像AssertionThreadLocalFilter这种设计理念是非常好的，实际应用中使用的也比较多，Spring Security中也有用到这种理念。为了便于大家了解，特贴出AssertionHolder的源码如下：


```java
public class AssertionHolder {
    /**
     * ThreadLocal to hold the Assertion for Threads to access.
     */
    private static final ThreadLocal threadLocal = new ThreadLocal();

    /**
     * Retrieve the assertion from the ThreadLocal.
     */

    public static Assertion getAssertion() {
        return (Assertion) threadLocal.get();
    }

    /**
     * Add the Assertion to the ThreadLocal.
     */

    public static void setAssertion(final Assertion assertion) {
        threadLocal.set(assertion);
    }

    /**
     * Clear the ThreadLocal.
     */

    public static void clear() {
        threadLocal.set(null);
    }
}
```

以下是配置AssertionThreadLocalFilter的示例：

  
```xml
<filter>

      <filter-name>casAssertionThreadLocalFilter</filter-name>

     <filter-class>org.jasig.cas.client.util.AssertionThreadLocalFilter</filter-class>

   </filter>

   <filter-mapping>

      <filter-name>casAssertionThreadLocalFilter</filter-name>

      <url-pattern>/*</url-pattern>

   </filter-mapping>
```

### 基于Spring的Filter配置

使用Cas单点登录的应用需要我们在应用的web.xml文件中配置上述介绍的四个Filter，但如果用户的应用是使用Spring开发的，则我们可以只在web.xml文件中配置四个Spring的DelegatingFilterProxy用来代理需要配置的四个Filter，对应的Filter名称对应我们需要代理的Spring ApplicationContext中bean的名称，此时我们需要将对应的Filter配置为Spring ApplicationContext中的一个bean对象。所以此时对应的web.xml文件的定义应该是这样的：

```xml
<filter>

      <filter-name>casAuthenticationFilter</filter-name>
     <filter-class>org.springframework.web.filter.DelegatingFilterProxy</filter-class>
   </filter>
   
   <filter-mapping>
      <filter-name>casAuthenticationFilter</filter-name>
      <url-pattern>/*</url-pattern>
   </filter-mapping>

   <filter>
      <filter-name>casTicketValidationFilter</filter-name>
     <filter-class>org.springframework.web.filter.DelegatingFilterProxy</filter-class>
   </filter>

   <filter-mapping>
      <filter-name>casTicketValidationFilter</filter-name>
      <url-pattern>/*</url-pattern>
   </filter-mapping>

   <filter>
      <filter-name>casHttpServletRequestWrapperFilter</filter-name>
     <filter-class>org.springframework.web.filter.DelegatingFilterProxy</filter-class>
   </filter>

   <filter-mapping>
      <filter-name>casHttpServletRequestWrapperFilter</filter-name>
      <url-pattern>/*</url-pattern>
   </filter-mapping>
   
   <filter>
      <filter-name>casAssertionThreadLocalFilter</filter-name>
     <filter-class>org.springframework.web.filter.DelegatingFilterProxy</filter-class>
   </filter>

   <filter-mapping>
      <filter-name>casAssertionThreadLocalFilter</filter-name>
      <url-pattern>/*</url-pattern>
   </filter-mapping>
```

而对应的Filter应该都以对应的名称定义为Spring ApplicationContext中的一个bean。

   
```xml
<bean name="casAuthenticationFilter"
      class="org.jasig.cas.client.authentication.AuthenticationFilter"
      p:casServerLoginUrl="https://elim:8443/cas/login" p:renew="false"
      p:gateway="false" p:serverName="http://elim:8080" />

   <bean name="casTicketValidationFilter"
      class="org.jasig.cas.client.validation.Cas10TicketValidationFilter"
      p:serverName="http://elim:8080" p:redirectAfterValidation="true">
      <property name="ticketValidator">
         <bean class="org.jasig.cas.client.validation.Cas10TicketValidator">
            <!-- 对应于casServerUrlPrefix -->
            <constructor-arg index="0" value="https://elim:8443/cas" />
         </bean>
      </property>
   </bean>

   <bean id="casHttpServletRequestWrapperFilter"class="org.jasig.cas.client.util.HttpServletRequestWrapperFilter"/>
   
   <bean id="casAssertionThreadLocalFilter"class="org.jasig.cas.client.util.AssertionThreadLocalFilter"/>
```

## 添加证书到信任库

在ticket验证成功后，还需要验证证书，这需要我们将之前建立的证书导出并添加到当前JRE的证书信任库中，否则将验证失败。JRE在寻找证书时将根据当前使用的host来寻找，且会用该host匹配之前创建证书时指定的用户名称，如果匹配则表示找到。这也就意味着我们在创建证书时指定的用户名称需要是我们的host。我的机器名称为“elim”，我就把它作为我的host，那么对应的证书应该这样创建。

```
keytool -genkey -keyalg RSA -alias tomcat -dname "cn=elim" -storepass changeit
```
该语句是对我们之前介绍的keytool -genkey -alias tomcat -keyalg RSA的精写，它已经通过相应的参数指定了对应的参数值，而不需要再与用户交互了。如果还用之前的语句生成证书的话，那么对应的值应该这样填：

之后会在用户的对应目录下生成一个.keystore文件。之后需要将该文件导出为一个证书到%JAVA_HOME%/jre/lib/security目录下，对应指令为：


```
keytool -export -alias tomcat -file %JAVA_HOME%/jre/lib/security/tomcat.crt -storepass changeit
```
之后需要将导出的tomcat.crt证书添加到运行时使用的JRE的受信任证书库中，此时如果出现异常可将原本%JAVA_HOME%/jre/lib/security目录下的cacerts删除后继续执行以下指令。

```
keytool -import -alias tomcat -file %JAVA_HOME%/jre/lib/security/tomcat.crt -keystore %JAVA_HOME%/jre/lib/security/cacerts -storepass changeit
```
经过以上几步后就可以启用我们自己的Cas Client应用了，然后初次访问该应用时就会跳转到Cas Server进行登录认证。认证成功后将跳转到我们自己的Client应用进行ticket的验证，验证通过后就可以自由的访问我们的Client应用了。

（注：本文是基于Cas Server3.5.2和Cas Client3.1.11所写）

# 单点登出

## Cas Client端配置单点登出

单点登出功能跟单点登录功能是相对应的，旨在通过Cas Server的登出使所有的Cas Client都登出。Cas Server的登出是通过请求“/logout”发生的，即如果你的Cas Server部署的访问路径为“https://localhost:8443/cas”时，通过访问“https://localhost:8443/cas/logout”可以触发Cas Server的登出操作，进而触发Cas Client的登出。在请求Cas Server的logout时，Cas Server会将客户端携带的TGT删除，同时回调该TGT对应的所有service，即所有的Cas Client。Cas Server中对应的TGT失效时默认也会触发同样的操作。Cas Client如果需要响应该回调，进而在Cas Client端进行登出操作的话就需要有对应的支持。具体来说，需要在Cas Client应用的web.xml文件中添加如下Filter和Listener。
  
```xml
<listener>
   <listener-class>org.jasig.cas.client.session.SingleSignOutHttpSessionListener</listener-class>
   </listener>
   
   <filter>
      <filter-name>casSingleSignOutFilter</filter-name>
      <filter-class>org.jasig.cas.client.session.SingleSignOutFilter</filter-class>
   </filter>
   
   <filter-mapping>
      <filter-name>casSingleSignOutFilter</filter-name>
      <url-pattern>/*</url-pattern>
   </filter-mapping>
```

SingleSignOutFilter需要配置在所有Filter之前，当Cas Client通过Cas Server登录成功，Cas Server会携带生成的Service Ticket回调Cas Client，此时SingleSignOutFilter会将Service Ticket与当前的Session绑定在一起。当Cas Server在进行logout后回调Cas Client应用时也会携带该Service Ticket，此时Cas Client配置的SingleSignOutFilter将会使对应的Session失效，进而达到登出的目的。

SingleSignOutHttpSessionListener用于在Cas Client应用中的Session过期时将其从对应的映射关系中移除。

## Cas Server端禁用单点登出

单点登出功能默认在Cas Server端是启用的。可能因为某种原因用户想禁用它。在Cas Server的WEB-INF/spring-configuration目录下有一个argumentExtractorsConfiguration.xml文件，其中定义了两个ArgumentExtractor类型的bean，一个是针对于Cas协议的，一个是针对于SAMP1.1协议的，但是它们都定义了一个共同的属性disableSingleSignOut，如下所示，该值对应的是一个属性替换变量，默认为false。

   
```xml
<bean
      id="casArgumentExtractor"
      class="org.jasig.cas.web.support.CasArgumentExtractor"
         p:httpClient-ref="noRedirectHttpClient"
         p:disableSingleSignOut="${slo.callbacks.disabled:false}" />
   <bean id="samlArgumentExtractor"class="org.jasig.cas.web.support.SamlArgumentExtractor"
             p:httpClient-ref="noRedirectHttpClient"
             p:disableSingleSignOut="${slo.callbacks.disabled:false}" />
```

在前面的内容中已经介绍过，Cas Server的所有属性替换变量都是定义在WEB-INF/cas.properties文件中的。所以如果需要禁用Cas Server的logout功能，我们可以在cas.properties文件中指定“slo.callbacks.disabled=true”。

（注：本文是基于Cas Server3.5.2和Cas Client3.1.11所写）

# 通过Proxy访问其它Cas应用

考虑这样一种场景：有两个应用App1和App2，它们都是受Cas Server保护的，即请求它们时都需要通过Cas Server的认证。现需要在App1中通过Http请求访问App2，显然该请求将会被App2配置的Cas的AuthenticationFilter拦截并转向Cas Server，Cas Server将引导用户进行登录认证，这样我们也就不能真正的访问到App2了。针对这种应用场景，Cas也提供了对应的支持。

## 原理

Cas Proxy可以让我们轻松的通过App1访问App2时通过Cas Server的认证，从而访问到App2。其主要原理是这样的，App1先通过Cas Server的认证，然后向Cas Server申请一个针对于App2的proxy ticket，之后在访问App2时把申请到的针对于App2的proxy ticket以参数ticket传递过去。App2的AuthenticationFilter将拦截到该请求，发现该请求携带了ticket参数后将放行交由后续的Ticket Validation Filter处理。Ticket Validation Filter将会传递该ticket到Cas Server进行认证，显然该ticket是由Cas Server针对于App2发行的，App2在申请校验时是可以校验通过的，这样我们就可以正常的访问到App2了。针对Cas Proxy的原理，官网有一张图很能说明问题，如下所示。

## 配置

Cas Proxy实现的核心是Cas20ProxyReceivingTicketValidationFilter，该Filter是Ticket Validation Filter的一种。使用Cas Proxy时我们需要使用Cas20ProxyReceivingTicketValidationFilter作为我们的Ticket Validation Filter，而且对于代理端而言该Filter需要放置在AuthenticationFilter之前。对于上述应用场景而言，App1就是我们的代理端，而App2就是我们的被代理端。Cas20ProxyReceivingTicketValidationFilter在代理端与被代理端的配置是不一样的。我们先来看一下在代理端的配置。

### 代理端

既然Cas20ProxyReceivingTicketValidationFilter是一个Ticket Validation Filter，所以之前我们介绍的Ticket Validation Filter需要配置的参数，在这里也需要配置，Ticket Validation Filter可以配置的参数这里也可以配置。所不同的是对于代理端的Cas20ProxyReceivingTicketValidationFilter必须指定另外的两个参数，proxyCallbackUrl和proxyReceptorUrl。

1. proxyCallbackUrl：

用于指定一个回调地址，在代理端通过Cas Server校验ticket成功后，Cas Server将回调该地址以传递pgtId和pgtIou，Cas20ProxyReceivingTicketValidationFilter在接收到对应的响应后会将它们保存在内部持有的ProxyGrantingTicketStorage中。之后在对传递过来的ticket进行validate的时候又会根据pgtIou从ProxyGrantingTicketStorage中获取对应的pgtId，用以保存在AttributePrincipal中，而AttributePrincipal又会保存在Assertion中。proxyCallbackUrl因为是指定Cas Server回调的地址，所以其必须是一个可以供外部访问的绝对地址。此外，因为Cas Server默认只回调使用安全通道协议https进行通信的地址，所以我们的proxyCallbackUrl需要是一个使用https协议访问的地址。

2. proxyReceptorUrl：

该地址是proxyCallbackUrl相对于代理端的一个地址，Cas20ProxyReceivingTicketValidationFilter将根据该地址来决定请求是否来自Cas Server的回调。

下面是一个Cas20ProxyReceivingTicketValidationFilter在代理端配置的示例，需要注意的是该Filter需要配置在AuthenticationFilter之前，所以完整配置如下：

   
```XML
<context-param>
      <param-name>serverName</param-name>
      <param-value>https://elim:8043</param-value>
   </context-param>

   <filter>
      <filter-name>proxyValidationFilter</filter-name>
   <filter-class>org.jasig.cas.client.validation.Cas20ProxyReceivingTicketValidationFilter</filter-class>
      <init-param>
         <param-name>casServerUrlPrefix</param-name>
         <param-value>https://elim:8443/cas</param-value>
      </init-param>

      <init-param>
         <param-name>proxyCallbackUrl</param-name>
         <param-value>https://elim:8043/app1/proxyCallback</param-value>
      </init-param>

      <init-param>
         <param-name>proxyReceptorUrl</param-name>
         <param-value>/proxyCallback</param-value>
      </init-param>
   </filter>

   <filter-mapping>
      <filter-name>proxyValidationFilter</filter-name>
      <url-pattern>/*</url-pattern>
   </filter-mapping> 

   <filter>
      <filter-name>casAuthenticationFilter</filter-name>
   <filter-class>org.jasig.cas.client.authentication.AuthenticationFilter</filter-class>
      <init-param>
         <param-name>casServerLoginUrl</param-name>
         <param-value>https://elim:8443/cas/login</param-value>
      </init-param>
   </filter>

   <filter-mapping>
      <filter-name>casAuthenticationFilter</filter-name>
      <url-pattern>/*</url-pattern>
   </filter-mapping>

   <filter>
      <filter-name>casHttpServletRequestWrapperFilter</filter-name>
   <filter-class>org.jasig.cas.client.util.HttpServletRequestWrapperFilter</filter-class>
   </filter>

   <filter-mapping>
      <filter-name>casHttpServletRequestWrapperFilter</filter-name>
      <url-pattern>/*</url-pattern>
   </filter-mapping>

   <filter>
      <filter-name>casAssertionThreadLocalFilter</filter-name>
     <filter-class>org.jasig.cas.client.util.AssertionThreadLocalFilter</filter-class>
   </filter>
   
   <filter-mapping>
      <filter-name>casAssertionThreadLocalFilter</filter-name>
      <url-pattern>/*</url-pattern>
   </filter-mapping>
```

### 被代理端

在被代理端Cas20ProxyReceivingTicketValidationFilter是扮演Ticket Validation Filter的角色，它可以验证正常通过Cas Server登录认证成功后返回的ticket，也可以认证来自其它代理端传递过来的proxy ticket，当然，最终的认证都是通过Cas Server来完成的。既然Cas20ProxyReceivingTicketValidationFilter在被代理端是作为Ticket Validation Filter来使用的，所以Ticket Validation Filter可以有的参数其都可以配置。在被代理端需要配置一个参数用以表示接受来自哪些应用的代理，这个参数可以是acceptAnyProxy，也可以是allowedProxyChains。acceptAnyProxy表示接受所有的，其对应的参数值是true或者false；而allowedProxyChains则用以指定具体接受哪些应用的代理，多个应用就写多行，allowedProxyChains的值对应的是代理端提供给Cas Server的回调地址，如果使用前文示例的代理端配置，我们就可以指定被代理端的allowedProxyChains为“https://elim:8043/app1/proxyCallback”，这样当app1作为代理端来访问该被代理端时就能通过验证，得到正确的响应。下面是一个被代理端配置Cas20ProxyReceivingTicketValidationFilter的完整配置示例。

  
```XML
<context-param>
      <param-name>serverName</param-name>
      <param-value>http://elim:8081</param-value>
   </context-param>
   
   <filter>
      <filter-name>casSingleSignOutFilter</filter-name>
      <filter-class>org.jasig.cas.client.session.SingleSignOutFilter</filter-class>
   </filter>

   <filter-mapping>
      <filter-name>casSingleSignOutFilter</filter-name>
      <url-pattern>/*</url-pattern>
   </filter-mapping>

   <filter>
      <filter-name>casAuthenticationFilter</filter-name>
   <filter-class>org.jasig.cas.client.authentication.AuthenticationFilter</filter-class>
      <init-param>
         <param-name>casServerLoginUrl</param-name>
         <param-value>https:// elim:8443/cas/login</param-value>
      </init-param>
   </filter>

   <filter-mapping>
      <filter-name>casAuthenticationFilter</filter-name>
      <url-pattern>/*</url-pattern>
   </filter-mapping>

   <filter>
      <filter-name>proxyValidationFilter</filter-name>
   <filter-class>org.jasig.cas.client.validation.Cas20ProxyReceivingTicketValidationFilter</filter-class>
      <init-param>
         <param-name>casServerUrlPrefix</param-name>
         <param-value>https://elim:8443/cas</param-value>
      </init-param>

      <init-param>
         <param-name>acceptAnyProxy</param-name>
         <param-value>true</param-value>
      </init-param>
   </filter>

   <filter-mapping>
      <filter-name>proxyValidationFilter</filter-name>
      <url-pattern>/*</url-pattern>
   </filter-mapping>
   
   <filter>
      <filter-name>casHttpServletRequestWrapperFilter</filter-name>
   <filter-class>org.jasig.cas.client.util.HttpServletRequestWrapperFilter</filter-class>
   </filter>

   <filter-mapping>
      <filter-name>casHttpServletRequestWrapperFilter</filter-name>
      <url-pattern>/*</url-pattern>
   </filter-mapping>

   <filter>
      <filter-name>casAssertionThreadLocalFilter</filter-name>
     <filter-class>org.jasig.cas.client.util.AssertionThreadLocalFilter</filter-class>
   </filter>

   <filter-mapping>
      <filter-name>casAssertionThreadLocalFilter</filter-name>
      <url-pattern>/*</url-pattern>
   </filter-mapping>
```

## 请求示例

配置好以后接下来将展示一个app1作为代理端访问app2的应用示例。该示例的重点在于app1的请求发起，对于需要请求的app2端的内容我们假设就是一个简单的jsp文件，其简单的输出一些文本。对于代理端而言，其请求的发起通常需要经过如下步骤：

1. 获取到当前的AttributePrincipal对象，如果当前可以获取到request对象并且使用了HttpServletRequestWrapperFilter，我们则可以直接从request中获取。

```
AttributePrincipal principal = (AttributePrincipal) req.getUserPrincipal();
```
当然，如果使用了AssertionThreadLocalFilter，我们也可以从AssertionHolder中获取Assertion，进而获取到对应的AttributePrincipal对象。

```
AttributePrincipal principal = AssertionHolder.getAssertion().getPrincipal();
```
2. 通过AttributePrincipal获取针对于被代理端对应的proxy ticket，该操作将促使AttributePrincipal向Cas Server发起请求，从而获取到对应的proxy ticket。针对同一URL每次从Cas Server请求获取到的proxy ticket都是不一样的。以下是一个获取针对于“http://elim:8081/app2/getData.jsp”的proxy ticket的示例：
  
```
String proxyTicket = principal.getProxyTicketFor("http://elim:8081/app2/getData.jsp");
```

3. 在请求被代理端时将获取到的proxy ticket以参数ticket一起传递过去，如：
 
```
URL url = new URL("http://elim:8081/app2/getData.jsp?ticket=" + proxyTicket);
```
完整的示例代码如下所示：


```java
@WebServlet(name="casProxyTest", urlPatterns="/cas/proxy/test")
public class CasProxyTestServlet extends HttpServlet {

   protected void doGet(HttpServletRequest req, HttpServletResponse resp)
         throws ServletException, IOException {
        doPost(req, resp);
   }

   protected void doPost(HttpServletRequest req, HttpServletResponse resp)
         throws ServletException, IOException {
         
      //1、获取到AttributePrincipal对象
      AttributePrincipal principal = AssertionHolder.getAssertion().getPrincipal();

      //2、获取对应的proxy ticket
      String proxyTicket = principal.getProxyTicketFor("http://elim:8081/app/getData.jsp");
      
      //3、请求被代理应用时将获取到的proxy ticket以参数ticket进行传递
      URL url = new URL("http://elim:8081/app/getData.jsp?ticket=" + URLEncoder.encode(proxyTicket, "UTF-8"));
      HttpURLConnection conn = (HttpURLConnection)url.openConnection();
      BufferedReader br = new BufferedReader(new InputStreamReader(conn.getInputStream(), "UTF-8"));
      StringBuffer content = new StringBuffer();
      String line = null;
      while ((line=br.readLine()) != null) {
        content.append(line).append("<br/>");
      }
      resp.getWriter().write(content.toString());
   }
}
```
