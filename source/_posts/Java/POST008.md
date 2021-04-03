---
title: Java成神之路-Tomcat、Jsp/servlet、Ajax（八）
tags: Java
category: Java
date: 2018-02-23 16:44:36
---
![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0134.jpg)

Java成神之路-Tomcat、Jsp/servlet、Ajax
<!--more-->
# tomcat服务

## tomcat的内存设置

Tomcat的启动分为startupo.bat启动和注册为windows服务的启动，下面一一说明。

### startup.bat启动

在tomcat_home/bin目录下找到catalina.bat，用文本编辑器打开，加上下面一行：

```
set JAVA_OPTS= -Xms1024M -Xmx1024M -XX:PermSize=256M -XX:MaxNewSize=256M -XX:MaxPermSize=256M
```

解释一下各个参数：

* -Xms1024M：初始化堆内存大小（注意，不加M的话单位是KB）
* -Xmx1029M：最大堆内存大小
* -XX:PermSize=256M：初始化类加载内存池大小
* -XX:MaxPermSize=256M：最大类加载内存池大小
* -XX:MaxNewSize=256M：这个还不清楚哈，有知道的说声

还有一个-server参数，是指启动jvm时以服务器方式启动，比客户端启动慢，但性能较好，大家可以自己选择。

### windows服务启动

​       如果你的tomcat是注册为windows服务并且是以服务方式启动的，那么上面的方法就无效了，因为这时tomcat启动是读取注册表的参数，而不是读取批处理文件的参数，这时我们有两种方法来设置jvm参数。

1. 第一种比较简单，tomcat为我们提供了一个设置启动参数的窗体，双击tomcat_home/bin目录下的tomcat6w.exe，如图

![Tomcat启动内存设置 - 一个人失眠 - 渴望](http://img853.ph.126.net/19ez1FAxdrVVLOWe75iqjQ==/613615449230442622.jpg)

下方的Initial memory pool就是初始化堆内存大小，Maximun memory pool是最大堆内存大小。

而要设置Perm Gen池的大小就要在Java Option里面加参数了，在里面加上：

* -Dcatalina.base=%tomcat_home%
* -Dcatalina.home=%tomcat_home%
* -Djava.endorsed.dirs=%tomcat_home%\endorsed
* -Djava.io.tmpdir=%tomcat_home%\temp
* -XX:PermSize=256M
* -XX:MaxPermSize=256M
* -XX:ReservedCodeCacheSize=48M
* -Duser.timezone=GMT+08

（PS：网上说每一行后面不要有空格，没试过）

2. 第二种方法是打开注册表->HKEY_LOCAL_MACHINE\SOFTWARE\Apache Software Foundation\Procrun 2.0\Tomcat6\Parameters\Java(路径可能有一点点差别)

![Tomcat启动内存设置 - 一个人失眠 - 渴望](http://img242.ph.126.net/wR1s2msgkQSskIdzeV-hDw==/1430737306621990743.png)

修改Options的值，把刚才上面那些参数加进去就OK了。（别忘了先备份一下注册表）

## 修改tomcat端口

### 默认情况下

tomcat的端口是8080，如果出现8080端口号冲突，用如下方法可以修改Tomcat的端口号：

在C:/Tomcat/conf/Server.xml文件中找到如下文本：

```xml
<Connector port="8080" protocol="HTTP/1.1" 
               maxThreads="150" connectionTimeout="20000" 
               redirectPort="8443" />
```

也有可能是这样的：

```xml
<Connector port="8080" maxThreads="150" minSpareThreads="25" maxSpareThreads="75" enableLookups="false" redirectPort="8443" acceptCount="100" debug="0" connectionTimeout="20000" disableUploadTimeout="true" />
```

等等；

最后：将port="8080"改为如port="8081"等，保存server.xml，重启Tomcat，Tomcat就可以使用8081端口。

### 使用两个tomcat

修改了上面的以后，还要修改两处：

1. 将 

```xml
<Connector port="8009" enableLookups="false" redirectPort="8443" debug="0"
protocol="AJP/1.3" />
```

的8009改为其它的端口。

2. 将

```xml
<Server port="8005" shutdown="SHUTDOWN" debug="0">
```

的8005改为其它的端口。
修改以上三处。

## tomcat改名

1. 找到tomcat下面的这个文件：tomcat_home\bin\catalina.bat 

2. 搜索到：

   ```xml
   :doStart  
   shift  
   if not "%OS%" == "Windows_NT" goto noTitle  
   set EXECJAVA=start "Tomcat" %RUNJAVA%  
   goto gotTitle 
   ```

   将"Tomcat"修改成想替换的名称即可

3. 在tomcat6.0.29以后的版本则如下： 

```xml
:doStart  
shift  
if not "%OS%" == "Windows_NT" goto noTitle  
if "%TITLE%" == "" set TITLE=Tomcat  
set _EXECJAVA=start "%TITLE%" %_RUNJAVA%  
goto gotTitle 
```

同理将 

```
if "%TITLE%" == "" set TITLE=Tomcat 
```

改为： 

```
if "%TITLE%" == "" set TITLE=你的应用服务名
```
# Servlet 

## Servlet 生命周期

Servlet 生命周期可被定义为从创建直到毁灭的整个过程。以下是 Servlet 遵循的过程：

- Servlet 通过调用 **init ()** 方法进行初始化。
- Servlet 调用 **service()** 方法来处理客户端的请求。
- Servlet 通过调用 **destroy()** 方法终止（结束）。
- 最后，Servlet 是由 JVM 的垃圾回收器进行垃圾回收的。

现在让我们详细讨论生命周期的方法。

### init() 方法

init 方法被设计成只调用一次。它在第一次创建 Servlet 时被调用，在后续每次用户请求时不再调用。因此，它是用于一次性初始化，就像 Applet 的 init 方法一样。

Servlet 创建于用户第一次调用对应于该 Servlet 的 URL 时，但是您也可以指定 Servlet 在服务器第一次启动时被加载。

当用户调用一个 Servlet 时，就会创建一个 Servlet 实例，每一个用户请求都会产生一个新的线程，适当的时候移交给 doGet 或 doPost 方法。init() 方法简单地创建或加载一些数据，这些数据将被用于 Servlet 的整个生命周期。

init 方法的定义如下：

```java
public void init() throws ServletException {
  // 初始化代码...
}

```

### service() 方法

service() 方法是执行实际任务的主要方法。Servlet 容器（即 Web 服务器）调用 service() 方法来处理来自客户端（浏览器）的请求，并把格式化的响应写回给客户端。

每次服务器接收到一个 Servlet 请求时，服务器会产生一个新的线程并调用服务。service() 方法检查 HTTP 请求类型（GET、POST、PUT、DELETE 等），并在适当的时候调用 doGet、doPost、doPut，doDelete 等方法。

下面是该方法的特征：

```java
public void service(ServletRequest request, 
                    ServletResponse response) 
      throws ServletException, IOException{
}

```

service() 方法由容器调用，service 方法在适当的时候调用 doGet、doPost、doPut、doDelete 等方法。所以，您不用对 service() 方法做任何动作，您只需要根据来自客户端的请求类型来重写 doGet() 或 doPost() 即可。

doGet() 和 doPost() 方法是每次服务请求中最常用的方法。下面是这两种方法的特征。

### doGet() 方法

GET 请求来自于一个 URL 的正常请求，或者来自于一个未指定 METHOD 的 HTML 表单，它由 doGet() 方法处理。

```java
public void doGet(HttpServletRequest request,
                  HttpServletResponse response)
    throws ServletException, IOException {
    // Servlet 代码
}

```

### doPost() 方法

POST 请求来自于一个特别指定了 METHOD 为 POST 的 HTML 表单，它由 doPost() 方法处理。

```java
public void doPost(HttpServletRequest request,
                   HttpServletResponse response)
    throws ServletException, IOException {
    // Servlet 代码
}

```

### destroy() 方法

destroy() 方法只会被调用一次，在 Servlet 生命周期结束时被调用。destroy() 方法可以让您的 Servlet 关闭数据库连接、停止后台线程、把 Cookie 列表或点击计数器写入到磁盘，并执行其他类似的清理活动。

在调用 destroy() 方法之后，servlet 对象被标记为垃圾回收。destroy 方法定义如下所示：

```java
  public void destroy() {
    // 终止化代码...
  }

```

### 架构图

Servlet 生命周期方案。

- 第一个到达服务器的 HTTP 请求被委派到 Servlet 容器。
- Servlet 容器在调用 service() 方法之前加载 Servlet。
- 然后 Servlet 容器处理由多个线程产生的多个请求，每个线程执行一个单一的 Servlet 实例的 service() 方法。

1. destory 方法被调用后，servlet 被销毁，但是并没有立即被回收，再次请求时，并没有重新初始化。

### 代码示例

   ```java
   private String message;

   @Override
   public void init() throws ServletException {
       message = "Hello World , Nect To Meet You: " + System.currentTimeMillis();
       System.out.println("servlet初始化……");
       super.init();
   }

   @Override
   public void doGet(HttpServletRequest req, HttpServletResponse response) throws ServletException, IOException {
       response.setContentType("text/html");
       PrintWriter writer = response.getWriter();
       writer.write("<h1>" + message + "</h1>");
       destroy();
   }

   @Override
   public void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
       // TODO Auto-generated method stub
       super.doPost(req, resp);
   }

   @Override
   public void destroy() {
       System.out.println("servlet销毁！");
       super.destroy();
   }
   ```

   控制台打印：

   ```
   servlet初始化……
   servlet销毁！
   2017-7-6 19:48:52 org.apache.catalina.core.StandardContext reload
   信息: Reloading Context with name [/myServlet] has started
   servlet销毁！
   2017-7-6 19:48:52 org.apache.catalina.core.StandardContext reload
   信息: Reloading Context with name [/myServlet] is completed
   servlet初始化……
   servlet销毁！
   servlet销毁！
   servlet销毁！
   servlet销毁！
   servlet销毁！
   servlet销毁！
   servlet销毁！
   ```

## Servlet常用类和接口

接口：Servlet、ServletConfig、ServletRequest、ServletResponse、HttpServletRequest、HttpServletResponse、ServletContext

类：HttpServlet(抽象类)、GenericServlet(抽象类)

基本类和接口
### javax.servlet.Servlet接口
servlet抽象集是javax.servlet.Servlet接口，它规定了必须由Servlet类实现由servlet引擎识别和管理的方法集。
Servlet接口的基本目标是提供生命期方法init()、service()和destroy()方法。

#### servlet接口中的方法       

- void init(ServletConfit config)throws ServletException     在servlet被载入后和实施服务前由servlet引擎进行?次性调用。如果init()产生溢出UnavailableException，则 servle退出服务。       
- ServletConfig getServletConfig()     返回传递到servlet的init()方法的ServletConfig对象       
- void service(ServletRequest request, ServletResponse response)throws ServletException,IOException     处理request对象中描述的请求，使用response对象返回请求结果       
- String getServletInfo()         返回描述servlet的一个字符串       
- void destory()         当servlet将要卸载时由servlet引擎调用   

### javax.servlet.GenericServlet类（协议无关版本）
GenericServlet是一种与协议无关的servlet，是一种跟本不对请求提供服务的servlet，而是简单地从init()方法启动后台线程并在destory()中杀死。它可以用于模拟操作系统的端口监控进程。
servlet API提供了Servlet接口的直接实现，称为GenericServlet。此类提供除了service()方法外所有接口中方法的缺省实现。这意味着通过简单地扩展GenericServlte可以编写一个基本的servlet。
除了Servlet接口外，GenericServlet也实现了ServletConfig接口，处理初始化参数和servlet上下文，提供对授权传递到init()方法中的ServletConfig对象的方法。

#### GenericServlet类中的方法       

- void destory()     编飞组成单词“destory”的一个注册入口       
- String getInitParameter(String name)     返回具有指定名称的初始化参数值。通过凋用config.getInitParameter(name)实现。       
- Enumeration getInitParameterNames()     返回此serv]et已编码的所有初始化参数的?个枚举类型值。调用config.getInitParameterNames()获得列表。如果未提供初始化参数，则返回?个空的枚举类型值（但不是null）       
- ServletConfig getServletConfig()     返回传递到init()方法的ServletConfig对象       
- ServletContext getServletContext()     返回在config对象中引用的ServletContext       
- String getServletInfo()     返回空字符串       
- void init(ServletConfig config)throws ServletException     在一实例变量中保存config对象。编写组成单词“init”的注册入口,然后调用方法init()       
- void init()throws ServletException     可以被跳过以处理servlet初始化.在config对象被保存后init(ServletConfig config)的结尾处自动被调用.servlet作者经常会忘记调用super.init(config)        
- void log(String msg)     编写注册servlet的入口.为此调用servlet上下文的log()方法.servlet的名字被加到消息文本的开头。       
- void log(String msg,Throwable t)     编写一个入口和servlet注册的栈轨迹。此方法也是ServletContext中相应方法的一个副本。       
- abstract void service(Request request,Response response)throws ServletException,IOException     由servlet引擎调用为请求对象描述的请求提供服务。这是GenericServlet中唯一的抽象方法。因此它也是唯一必须被子类所覆盖的方法。        
- String getServletName()     返回在Web应用发布描述器（web.xml）中指定的servlet的名字。     

### javax.servlet.http.HttpServlet类（HTTP版本）
虽然servlet API允许扩展到其它协议，但最终所有的servlet均在Web环境下实施操作，只有几种servlet直接扩展了GenericServlet。对servlet更一般的是扩展其HTTP子类HttpServlet。
HttpServlet 类通过调用指定到HTTP请求方法的方法实现service()，亦即对DELETE、HEAD、GET、OPTIONS、POST、PUT和 TRACE，分别调用doDelete()、doHead()、doGet()、doOptions()、doPost()、doPut()和 doTrace()方法，将请求和响应对象置入其HTTP指定子类。

#### HttpServlet类中的方法       

- Void doGet(HttpServletRequest request,HttpServletResponse response)throws ServletException,IOException     由servlet引擎调用用处理一个HTTP GET请求。输入参数、HTTP头标和输入流可从request对象、response头标和response对象的输出流中获得。       
- Void doPost(HttpServletRequest request,HttpServletResponse response)throws ServletException,IOException     由servlet引擎调用用处理一个HTTP POST请求。输入参数、HTTP头标和输入流可从request对象、response头标和response对象的输出流中获得。       
- Void doPut(HttpServletRequest request,HttpServletResponse response)throws ServletException,IOException     由servlet引擎调用用处理一个HTTP PUT请求。本方法中请求URI指出被载入的文件位置。       
- Void doDelete(HttpServletRequest request,HttpServletResponse response)throws ServletException,IOException     由servlet引擎调用用处理一个HTTP DELETE请求。请求URI指出资源被删除。       
- Void doOptions(HttpServletRequest request,HttpServletResponse response)throws ServletException,IOException     由servlet引擎调用用处理一个HTTP OPTIONS请求。返回一个Allow响应头标表明此servlet支持的HTTP方法。一个servlet不需要覆盖此方法，因为 HttpServlet方法已经实现规范所需的功能。       
- Void doTrace(HttpServletRequest request,HttpServletResponse response)throws ServletException,IOException     由servlet引擎调用用处理一个HTTP TRACE请求。使得请求头标被反馈成响应关标。一个servlet不需要覆盖此方法，因为HttpServlet方法已经实现HTTP规范所需的功能。        
- Void service(HttpServletRequest request,HttpServletResponse response)throws ServletException,IOException     Service(Request request,Response response)调用的一个立即方法，带有指定HTTP请求和响应。此方法实际上将请求导向doGet()、doPost()等等。不应该覆盖此方法。        
- Void service(Request request,Response response)throws ServletException,IOException     将请求和响应对象置入其指定的HTTP子类，并调用指定HTTP的service()方法。     

### javax.servlet.ServletRequest类（协议无关版本）
ServletRequest接口封装了客户端请求的细节。它与协议无关，并有一个指定HTTP的子接口。

#### ServletRequest主要处理：

1. 找到客户端的主机名和IP地址
2. 检索请求参数
3. 取得和设置属性
4. 取得输入和输出流

#### ServletRequest类中的方法       

- Object getAttribute(String name)     返回具有指定名字的请求属性，如果不存在则返回null。属性可由servlet引擎设置或使用setAttribute()显式加入。       
- Enumeration getAttributeName()     返回请求中所有属性名的枚举。如果不存在属性，则返回一个空的枚举。       
- String getCharacteEncoding()     返回请求所用的字符编码。       
- Int getContentLength()     指定输入流的长度，如果未知则返回-1。       
- ServletInputStream getInputStream()throws IOException     返回与请求相关的（二进制）输入流。可以调用getInputStream()或getReader()方法之一。       
- String getParameter(String name)     返回指定输入参数，如果不存在，返回null。       
- Enumeration getParameterName()     返回请求中所有参数名的一个可能为空的枚举。       
- String[] getParameterValues(String name)     返回指定输入参数名的取值数组，如果取值不存在则返回null。它在参数具有多个取值的情况下十分有用。       
- String get Protocol()     返回请求使用协议的名称和版本。       
- String getScheme()     返回请求URI的子串，但不包含第一个冒号前的内容。       
- String getServerName()     返回处理请求的服务器的主机名。       
- String getServerPort()     返回接收主机正在侦听的端口号。       
- BufferedReader getReader()throws IOException     返回与请求相关输入数据的一个字符解读器。此方法与getInputStream()只可分别调用，不能同时使用。       
- String getRemoteAddr()     返回客户端主机的数字型IP地址。       
- String getRemoteHost()     如果知道，返回客户端主机名。       
- void setAttribute(String name,Object obj)     以指定名称保存请求中指定对象的引用。       
- void removeAttribute(String name)     从请求中删除指定属性       
- Locale getLocale()     如果已知，返回客户端的第一现场或者为null。       
- Enumeration getLocales()     如果已知，返回客户端的第一现场的一个枚举，否则返回服务器第一现场。       
- boolean isSecure()     如果请求使用了如HTTPS安全隧道，返回true       
- RequestDispatcher getRequestDispatcher(String name)     返回指定源名称的RequsetDispatcher对象。 

### javax.servlet.http.HttpServletRequest接口（HTTP版本）
#### HttpServletRequest类主要处理：

1.读取和写入HTTP头标

2. 取得和设置cookies
3. 取得路径信息
4. 标识HTTP会话

#### HttpServletRequest接口中的方法       

- String getAuthType()     如果servlet由一个鉴定方案所保护，如HTTP基本鉴定，则返回方案名称。       
- String getContextPath()     返回指定servlet上下文（web应用）的URL的前缀。       
- Cookie[] getCookies()     返回与请求相关cookie的一个数组。       
- Long getDateHeader(String name)     将输出转换成适合构建Date对象的long类型取值的getHeader()的简化版。       
- String getHeader(String name)     返回指定的HTTP头标指。如果其由请求给出，则名字应为大小写不敏感。       
- Enumeration getHeaderNames()     返回请求给出的所有HTTP头标名称的权举值。       
- Enumeration getHeaders(String name)     返回请求给出的指定类型的所有HTTP头标的名称的枚举值，它对具有多取值的头标非常有用。       
- int getIntHeader(String name)     将输出转换为int取值的getHeader()的简化版。       
- String getMethod()     返回HTTP请求方法（例如GET、POST等等）       
- String getPathInfo()     返回在URL中指定的任意附加路径信息。       
- String getPathTranslated()     返回在URL中指定的任意附加路径信息，被子转换成一个实际路径。       
- String getQueryString()     返回查询字符串，即URL中?后面的部份。       
- String getRemoteUser()     如果用户通过鉴定，返回远程用户名，否则为null。       
- String getRequestedSessionId()     返回客户端的会话ID       
- String getRequestURI()     返回URL中一部分，从“/”开始，包括上下文，但不包括任意查询字符串。       
- String getServletPath()     返回请求URI上下文后的子串       
- HttpSession getSession()     调用getSession(true)的简化版。       
- HttpSession getSession(boolean create)     返回当前HTTP会话，如果不存在，则创建一个新的会话，create参数为true。       
- Principal getPrincipal()     如果用户通过鉴定，返回代表当前用户的java.security.Principal对象，否则为null。       
- boolean isRequestedSessionIdFromCookie()     如果请求的会话ID由一个Cookie对象提供，则返回true，否则为false。       
- boolean isRequestedSessionIdFromURL()     如果请求的会话ID在请求URL中解码，返回true，否则为false       
- boolean isRequestedSessionIdValid()     如果客户端返回的会话ID仍然有效，则返回true。       
- Boolean isUserInRole(String role)     如果当前已通过鉴定用户与指定角色相关，则返回true，如果不是或用户未通过鉴定，则返回false。     

### javax.servlet.ServletResponse接口（协议无关版本）
ServletResponse对象将一个servlet生成的结果传到发出请求的客户端。ServletResponse操作主要是作为输出流及其内容类型和长度的包容器，它由servlet引擎创建.

#### ServletResponse接口中的方法       

- void flushBuffer()throws IOException     发送缓存到客户端的输出内容。因为HTTP需要头标在内容前被发送，调用此方法发送状态行和响应头标，以确认请求。       
- int getBufferSize()     返回响应使用的缓存大小。如果缓存无效则返加0。       
- String getCharacterEncoding()     返回响应使用字符解码的名字。除非显式设置，否则为ISO-8859-1       
- Locale getLocale()     返回响应使用的现场。除非用setLocale()修改，否则缺省为服务器现场。       
- OutputStream getOutputStream()throws IOException     返回用于将返回的二进制输出写入客户端的流，此方法和getWrite()方法二者只能调用其一。       
- Writer getWriter()throws IOException     返回用于将返回的文本输出写入客户端的一个字符写入器，此方法和getOutputStream()二者只能调用其一。       
- boolean isCommitted()     如果状态和响应头标已经被发回客户端，则返回true，在响应被确认后发送响应头标毫无作用。       
- void reset()     清除输出缓存及任何响应头标。如果响应已得到确认，则引发事件IllegalStateException。       
- void setBufferSize(int nBytes)     设置响应的最小缓存大小。实际缓存大小可以更大，可以通过调用getBufferSize()得到。如果输出已被写入，则产生IllegalStateException。       
- void setContentLength(int length)     设置内容体的长度。       
- void setContentType(String type)     设置内容类型。在HTTP servlet中即设置Content-Type头标。       
- void setLocale(Locale locale)     设置响应使用的现场。在HTTP servlet中，将对Content-Type头标取值产生影响。     

### javax.servlet.http.HttpServletResponse接口（HTTP版本）
HttpServletResponse加入表示状态码、状态信息和响应头标的方法，它还负责对URL中写入一Web页面的HTTP会话ID进行解码。

#### HttpServletResponse接口中的方法       

- void addCookie(Cookie cookie)     将一个Set-Cookie头标加入到响应。       
- void addDateHeader(String name,long date)     使用指定日期值加入带有指定名字（或代换所有此名字头标）的响应头标的方法。       
- void setHeader(String name,String value)     设置具有指定名字和取值的一个响应头标。       
- void addIntHeader(String name,int value)     使用指定整型值加入带有指定名字的响应头标（或代换此名字的所有头标）。       
- boolean containsHeader(String name)     如果响应已包含此名字的头标，则返回true。       
- String encodeRedirectURL(String url)     如果客户端不知道接受cookid，则向URL加入会话ID。第一种形式只对在sendRedirect()中使用的URL进行调用。其他被编码的 URLs应被传递到encodeURL()       
- String encodeURL(String url)           
- void sendError(int status)     设置响应状态码为指定值（可选的状态信息）。HttpServleetResponse定义了一个完整的整数常量集合表示有效状态值。       
- void sendError(int status,String msg)           
- void setStatus(int status)     设置响应状态码为指定指。只应用于不产生错误的响应，而错误响应使用sendError()。     

### javax.servlet.ServletContext接口
一个servlet上下文是servlet引擎提供用来服务于Web应用的接口。Servlet上下文具有名字（它属于Web应用的名字）唯一映射到文件系统的一个目录。
一个servlet可以通过ServletConfig对象的getServletContext()方法得到servlet上下文的引用，如果servlet直接或间接调用子类GenericServlet，则可以使用getServletContext()方法。

#### Web应用中servlet可以使用servlet上下文得到：

1. 在调用期间保存和检索属性的功能，并与其他servlet共享这些属性。


2. 读取Web应用中文件内容和其他静态资源的功能。
3. 互相发送请求的方式。
4. 记录错误和信息化消息的功能。

#### ServletContext接口中的方法       

- Object getAttribute(String name)     返回servlet上下文中具有指定名字的对象，或使用已指定名捆绑一个对象。从Web应用的标准观点看，这样的对象是全局对象，因为它们可以被同一 servlet在另一时刻访问。或上下文中任意其他servlet访问。       
- void setAttribute(String name,Object obj)     设置servlet上下文中具有指定名字的对象。       
- Enumeration getAttributeNames()     返回保存在servlet上下文中所有属性名字的枚举。       
- ServletContext getContext(String uripath)     返回映射到另一URL的servlet上下文。在同一服务器中URL必须是以“/”开头的绝对路径。       
- String getInitParameter(String name)     返回指定上下文范围的初始化参数值。此方法与ServletConfig方法名称不一样，后者只应用于已编码的指定servlet。此方法应用于上下文中所有的参数。       
- Enumeration getInitParameterNames()     返回（可能为空）指定上下文范围的初始化参数值名字的枚举值。       
- int getMajorVersion()     返回此上下文中支持servlet API级别的最大和最小版本号。       
- int getMinorVersion()           
- String getMimeType(String fileName)     返回指定文件名的MIME类型。典型情况是基于文件扩展名，而不是文件本身的内容（它可以不必存在）。如果MIME类型未知，可以返回null。       
- RequestDispatcher getNameDispatcher(String name)     返回具有指定名字或路径的servlet或JSP的RequestDispatcher。如果不能创建RequestDispatch，返回null。如果指定路径，必须心“/”开头，并且是相对于servlet上下文的顶部。       
- RequestDispatcher getNameDispatcher(String path)           
- String getRealPath(String path)     给定一个URI，返回文件系统中URI对应的绝对路径。如果不能进行映射，返回null。       
- URL getResource(String path)     返回相对于servlet上下文或读取URL的输入流的指定绝对路径相对应的URL，如果资源不存在则返回null。       
- InputStream getResourceAsStream(String path)           
- String getServerInfo()     返顺servlet引擎的名称和版本号。       
- void log(String message)
- void log(String message,Throwable t)     将一个消息写入servlet注册，如果给出Throwable参数，则包含栈轨迹。       
- void removeAttribute(String name)     从servlet上下文中删除指定属性。     

### javax.servlet.http.HttpSession接口
HttpSession类似于哈希表的接口，它提供了setAttribute ()和getAttribute()方法存储和检索对象。HttpSession提供了一个会话ID关键字，一个参与会话行为的客户端在同一会话的请求中存储和返回它。servlet引擎查找适当的会话对象，并使之对当前请求可用。

#### HttpSession接口中的方法       

- Object getAttribute(String name)     将会话中一个对象保存为指定名字，返回或删除前面保存的此名称对象。       
- void setAttribute(String name,Object value)           
- void removeAttribute(String name)           
- Enumeration getAttributeName()     返回捆绑到当前会话的所有属性名的枚举值。       
- long getCreationTime()     返回表示会话创建和最后访问日期和时间的一个长整型，该整型形式为java.util.Date()构造器中使用的形式。       
- long getLastAccessedTime()           
- String getId()     返回会话ID，servlet引擎设置的一个唯一关键字。       
- ing getMaxInactiveInterval()     如果没有与客户端发生交互，设置和返回会话存活的最大秒数。       
- void setMasInactiveInterval(int seconds)           
- void invalidate()     使得会话被终止，释放其中任意对象。       
- boolean isNew()     如果客户端仍未加入到会话，返回true。当会话首次被创建，会话ID被传入客户端，但客户端仍未进行包含此会话ID的第二次请示时，返回true。

## Servlet的两种配置方式

在Servlet2.5规范之前，通过web.xml文件来配置管理

Servlet3.0规范可通过Annotation来配置管理Web组件

1. 通过web.xml配置

```xml
<span style="white-space:pre">	</span><servlet>
		<servlet-name>GetApplication</servlet-name>
		<servlet-class>com.fpp.GetApplication</servlet-class>
		<!-- 通过servletConfig getInitParameter()取出参数 -->
	</servlet>
	<servlet-mapping>
		<servlet-name>GetApplication</servlet-name>
		<url-pattern>/GetApplication</url-pattern>
	</servlet-mapping>
```

2. 使用@WebServlet Annotation进行配置

```xml
@WebServlet(name="GetApplication",urlPatterns="/GetApplication")  
public class GetApplication extends HttpServlet   
```

@WebServlet支持的常用属性：displayName,initParams,loadOnStartup,name,urlPatterns/value,asyncSupported

如果打算使用Annotation来配置Servlet，需要注意以下几点：

* 不要在web.xml文件的根元素（<web-app---/>）中指定metadata-complete=“true”；
* 不要在web.xml文件中配置该Servlet;

### 配置中各节点的含义

```xml
<!-- 配置一个servlet -->
<!-- servlet的配置 -->
<servlet>
    <!-- servlet的内部名称，自定义。尽量有意义 -->
    <servlet-name>ServletDemo</servlet-name>
    <!-- servlet的类全名： 包名+简单类名 -->
    <servlet-class>lm.practice.ServletDemo</servlet-class>
</servlet>
<!-- servlet的映射配置 -->
<servlet-mapping>
    <!-- servlet的内部名称，一定要和上面的内部名称保持一致！！ -->
    <servlet-name>ServletDemo</servlet-name>
    <!-- servlet的映射路径（访问servlet的名称） -->
    <url-pattern>/servlet</url-pattern>
</servlet-mapping>
```

可以看到，在配置Servlet时，有两个地方需要配置，一个是<servlet>,另一个是<servlet-Mapping>，这两个一个是配置Servlet，一个是配置其映射信息，其中<servlet>中的<servlet-name>可以随意指定，但要有一定的意义，一般取为类的名称，例如我的类名为ServletDemo，这里取名为ServletDemo,下面的<servlet-class>是类的全路径，package+calssname，一定要是全路径！

<servlet-Mapping>是映射信息，它也有一个<servlet-name>，里面的名字是对应的Servlet名，也就是我们上面配置的Servlet名字，这里是ServletDemo，下面的是映射路径，也就是访问Servlet的名称，这里也是以方便和有意义为前提的，是我们在访问Servlet在浏览器地址栏后面输入的那个信息，例如我的映射路径命名为/servlet，在地址栏中输入http://localhost/20170323/servlet

注意：这里的映射路径一定不是丢掉/，否则就会出错了，一定要写成/servlet，不能是servlet

这里说一下在配置映射路径的时候，有以下两种：

|          | url-pattern    | 浏览器输入                                 |
| -------- | -------------- | ------------------------------------------ |
| 精确匹配 | /servlet       | http://localhost:8080/day10/servlet        |
| 模糊匹配 | /*             | http://localhost:8080/20170323/任意路径    |
| 模糊匹配 | /lm/*          | http://localhost:8080/20170323/lm/任意路径 |
| 模糊匹配 | *.后缀名       | http://localhost:8080/20170323/任意路径.do |
| 模糊匹配 | *.do           |                                            |
| 模糊匹配 | *.action       |                                            |
| 模糊匹配 | *.html(伪静态) |                                            |

注意：

1. url-pattern要么以 / 开头，要么以*开头。  绝对不能漏掉斜杠！！！！！！！！！*
2. *不能同时使用两种模糊匹配，例如 /lm/*.do是非法路径
3. 当有输入的URL有多个servlet同时被匹配的情况下：
   * 精确匹配优先。（长的最像优先被匹配）
   * 以后缀名结尾的模糊匹配先级最低！！！

# JSP

一. 三大指令：

1. page: 该指令是针对当前页面的指令。
2. include: 用于指定如何包含另一个页面。
3. tablib: 用于定义和访问自定义标签。

二. 七大动作：

1. forward: 执行页面转向，将请求的处理转发到下一个页面。
2. param: 用于传递参数，必须与其他支持参数曲标签一起使用。
3. include: 用于动态引入一个 JSP 页面。
4.  plugin: 用于下载 JavaBean 或 Applet 到客户端执行。
5. useBean: 使用 JavaBean。
6. setProperty: 修改 JavaBean 实例的属性值。
7. getProperty: 获取 JavaBean 实例的属性值。

三. 九大对象：

1. application:

 javax.servlet.ServletContext 的实例，该实例代表JSP所属的 Web应用本身，可用于 JSP 页面，或者 Servlet 之间交换信息。

常用的方法有getAttribute(String attNarne) , setAttribute(String attNarne , String attValue)和getInitPararneter(String paramNarne)等。

1. config: 

javax.servlet.ServletConfig 的实例，该实例代表该JSP 的配置信息。常用的方法有 getInitPararneter(StringparamNarne)及 getInitPararneternarnes() 等方法。事实上， JSP 页面通常无须配置，也就不存在配置信息。因此，该对象更多地在Servlet 中有效。

1. exception:

 java.lang.Throwable的实例，该实例代表其他页面中的异常和错误。只有当页面是错误处理页面，即编译指令page 的 isErrorPage属性为 true 时，该对象才可以使用。

常用的方法有getMessageO和 printStackTraceO等。

1. out:

 javax.servlet.jsp.JspWriter的实例，该实例代表JSP 页面的输出流，用于输出内容，形成HTML 页面。

1. page: 

代表该页面本身，通常没有太大用处。也就是 Servlet 中的 this，其类型就是生成的 Serlet 。

1. pageContext:

javax.servlet.jsp.PageContext的实例，该对象代表该JSP 页面上下文，使用该对象可以访问页面中的共享数据。

常用的方法有getServletContextO和getServletConfigO等。

1. request:

 javax.servlet.http:HttpServletRequest的实例，该对象封装了一次请求，客户端的请求参数都被封装在该对象里。这是一个常用的对象，获取客户端请求参数必须使用该对象。封装请求属性，封装地址栏参数，封装表单域值。

常用的方法有getPararneter(StringparamNarne), getPararneterValues(String paramName), setAttribute(String atttibuteName,Object attributeValue),getAttribute(String attributeName)和 setCharacterEncoding(Stringenv)等。

1. response:

 javax.servlet.http.HttpServletResponse的实例，代表服务器对客户端的响应。通常，也很少使用该对象直接响应，输出响应使用out 对象，而 response 对象常用于重定向。

常用的方法有sendRedirect(java.lang.Stringlocation)等。

1. session:

 javax.servlet.http.HttpSession的实例，该对象代表一次会话。从客户端浏览器与站点建立连接起，开始会话，直到关闭浏览器时结束会话。

常用的方法有:getAttribute(StringattName),setAttribute(StringattName,String attValue)等。

## jsp九大内置对象

1. out 向客户端输出数据,字节流.如out.print(" dgaweyr");
2. request 接收客户端的http请求.

- String getParameter(String name):   得到表单参数名name的值.
- String[] getParameterValues(String name):  (得到String[]复选框时常用).
- setAttribute(String name,Object obj):  设置属性名为name,属性值为obj.
- getAttribute(String name);  得到属性值.

3. response: 封装jsp产生的回应,然后发送到客户端以响应客户的请求.重定向跳转任意界面.(服务器跳转)

- addCookie(Cookie cookie):
- sendRedirect("/wel.jsp"):跳转到指定页面

4. session:用于保存用户信息,跟踪用户行为,当前打开的浏览器内,多个页面共享数据. session对象指的是客户端与服务器的一次会话,从客户连到服务器的一个WebApplication开始,直到客户端与服务器断开连接为止.它是HttpSession类的实例.

- setAttribute(String name,Object obj):设置属性名为name,属性值为obj.
- getAttribute(String name):得到属性值.

5. application对象:实现了用户间数据的共享,可存放全局变量.它开始于服务器的启动,直到服务器的关闭,在此期间,此对象将一直存在;这样在用户的前后连接或不同用户之间的连接中,可以对此对象的同一属性进行操作;在任何地方对此对象属性的操作,都将影响到其他用户对此的访问.服务器的启动和关闭决定了application对象的生命.它是ServletContext类的实例.

### session, application, request的区别:

一个项目中session尽量少用几个,因为过多的session会影响程序的执行效率.它主要用于保存登录信息(用户信息,权限,资源)即频繁使用的信息.

application: 用于多个浏览器之间共享数据,多个用户共享该对象,可以做计数器.它的用法与session完全一样.

数据范围:

application(服务器关闭时失效)>session(浏览器关闭时失效)>request(只能用于两个跳转页面之间)

6. page对象代表jsp这个实体本身,即当前页面有效.相当于java中的this.

数据范围:page<session<application

7. exception:代表运行时的异常.

在会发生异常的页面加入指令:<%@ page errorPage="处理错误的页面.jsp"%>

在处理异常的页面写入:<%@ page isErrorPage="true"%>

8. pageContext对象 pageContext对象提供了对JSP页面内所有的对象及名字空间的访问,也就是说他可以访问到本页所在的SESSION,也可以取本页面所在的application的某一属性值,他相当于页面中所有功能的集大成者,它的本类名也叫pageContext.

9. config  jsp对应的servlet的配置,可以得到web.xml中的初使化参数.

## jsp七大动作

1. include 动态包含(分别编译) 用于动态引入一个 JSP 页面。

用jsp:include动作实现

```jsp
<jsp: include page="included.jsp" flush="true" />
```

它总是会检查所含文件中的变化，适合用于包含动态页面，并且可以带参数。flush属性: 用true ，表示页面可刷新。默认为false;

2. useBean动作(jsp页面使用javaBean的第二种方式):  使用 JavaBean。

```jsp
<jsp:useBean id="对象名" class="包名.类名" scope="作用范围(request/page/application/session)"/>
```

作用域默认为page(本页面有效).

3. getProperty动作(name为useBean动作中的id).  获取 JavaBean 实例的属性值。

从对象中取出属性值：

```jsp
<jsp:getProperty name="javaBean对象" property="javaBean对象属性名" />
```

4. setProperty动作(name为useBean动作中的id):  修改 JavaBean 实例的属性值。

为对象设置属性值：

```jsp
<jsp:setProperty name="javaBean对象" property="javaBean对象属性名" value=http://www.hake.cc/kf/201109/"值"/>
```

为对象设置属性值:

```jsp
<jsp:setProperty property="javaBean对象属性名" name="javaBean对象" param="username"/>
```

```jsp
(param="username" 相当于 value=http://www.hake.cc/kf/201109/<%=request.getParameter("username")%>)
```

5. param动作(传递参数)): 用于传递参数，必须与其他支持参数曲标签一起使用。

到达跳转页面可以通过 request.getParameter(“参数名”)方式取出参数值

```jsp
<jsp:include page="转向页面的url" >
           <jsp:param   name="参数名1" value=http://www.hake.cc/kf/201109/"参数值1">
		  <jsp:param   name="参数名2" value=http://www.hake.cc/kf/201109/"参数值2">
           ...........
</jsp:include>
```
或:

```jsp
<jsp:forward page="转向页面的url" >
           <jsp:param   name="参数名1" value=http://www.hake.cc/kf/201109/"参数值1">
           <jsp:param   name="参数名2" value=http://www.hake.cc/kf/201109/"参数值2">
           ...........
</jsp:forward>
```
6. forward动作: 执行页面转向，将请求的处理转发到下一个页面。

跳转页面

```jsp
<jsp:forward page="login.jsp" />
```

7. plugin动作:jsp:plugin:用于指定在客户端运行的插件 用于下载 JavaBean 或 Applet 到客户端执行。

## JSP三大指令

1. page指令: 该指令是针对当前页面的指令。

* 指定页面编码.例:

```jsp
<%@ page language="java" contentType="text/html;charset=gbk" pageEncoding="gbk" %>
```

* 导入包,例:

```jsp
<%@ page import="java.util.,java.text." %>
```

2. include 指令 用于指定如何包含另一个页面。

静态包含(统一编译):

```jsp
<%@ include file="included.jsp"%>
```

3. taglib 用于定义和访问自定义标签。

Jsp中的静态包含与动态包含

```jsp
<jsp: include page="included.jsp"/>//动态include
<%@ include file="included.jsp"%>//静态include
```

- 静态include的结果是把其他jsp引入当前jsp,两者合为一体,可以达到数据的共享即可以说是统一编译的,而
   动态include的结构是两者独立的,直到输出时才合并即为分别编译的.
- 动态include的jsp文件独立性很强,是一个单独的jsp文件,需要使用的对象,页面设置,都由自己创建,而静态include纯粹是把代码写在外面的一种共享方法,所有的变量都是可以和include它的主文件共享,两者高度紧密结合,不能 有变量同名的冲突.而页面设置也可以借用主文件的.
- 动态包含总是检查被包含页面的变化,静态包含不一定检查被包含页面的变化.
- 动态包含可带参数,静态包含不能带参数.如`(<jsp: include page="included.jsp">放入参数/jsp:include);`

## El表达式：

​         ${} ：可以自动获取域中对象，request，session，application，pageContext，EL找不到返回空白字符串。

​    EL表达式中11大内置对象

​           1，pageContext        获取页面context的Map对象     

​           2，pageScope        获取页面域的Map对象

​           3，requestScope        获取request域的Map对象

​           4，sessionScope        获取session域Map对象

​           5，applicationScope      获取context的Map对象

​           6，param                     获取一个请求参数

​           7，paramValues        获取一个请求参数数组    

​           8， header                  获取一个请求域对象

​           9，headerValues

​          10，cookies

​          11，initParam               获取web.xml中的参数

El表达式中可以直接获取域中的数据：

```jsp
<%  
     pageContext.setAttribute("NAME","哈哈");  
 %>              
 姓名：${NAME}<br/>  
 <hr/>  
 <%  
     User user = new User(2015,"呵呵",10000D);  
     request.setAttribute("USER",user);  
 %>  
 编号：${USER.id}<br/>  
 姓名：${USER.name}<br/><!--自动调用getter方法-->  
 薪水：${USER.sal}<br/>  
 <hr/>  
 <%  
     List<String> nameList = new ArrayList<String>();  
     nameList.add("A");  
     nameList.add("B");  
     nameList.add("C");  
     session.setAttribute("NAMELIST",nameList);      
 %>  
 第二个元素是：${NAMELIST[1]}<br/>  
 <hr/>  
 <%  
     Map<String,Integer> map = new LinkedHashMap<String,Integer>();  
     map.put("jack",10000);  
     map.put("marry",12000);  
     map.put("sisi",14000);  
     application.setAttribute("MAP",map);      
 %>  
 SISI的工资是：${MAP['sisi']}<br/>  
 <hr/>  
 <%  
     String[] strArray = {"北京","上海","广州","深圳"};  
     pageContext.setAttribute("STRARRAY",strArray);  
 %>  
 你目前所在的城市是：${STRARRAY[2]}<br/>      
 <hr/>  
 姓名：${NAMEE}<br/>  
```

EL表达式中的运算符：

```jsp
<%@ page language="java" pageEncoding="UTF-8"%>  
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">  
<html>  
  <body>  
      
    10+3=${10+3}<br/>  
    10-3=${10-3}<br/>  
    10*3=${10*3}<br/>  
    10/3=${10/3}<br/>  
    10%3=${10%3}<br/>  
    <hr/>  
    true && false = ${true && false}<br/>  
    true || false = ${true || false}<br/>  
    ! false = ${! false}<br/>  
    <hr/>  
    10>3=${10>3}<br/>  
    10!=3=${10 ne 3}<br/>  
    10==3=${10 eq 3}<br/>  
      
  </body>  
</html>  
```

EL表达式中的三木运算符：

```jsp
<%@ page language="java" pageEncoding="UTF-8"%>  
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">  
<html>  
  <body>  
    <%  
          
        pageContext.setAttribute("city","深圳");  
    %>  
    城市：  
    <select name="city">  
        <option>选择城市</option>  
        <option ${city=='北京'?'selected':''}>北京</option>  
        <option ${city=='上海'?'selected':''}>上海</option>  
        <option ${city=='深圳'?'selected':''}>深圳</option>  
        <option ${city=='广州'?'selected':''}>广州</option>  
    </select>              
  </body>  
</html>  
```

```jsp
<%@ page language="java" pageEncoding="UTF-8"%>  
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">  
<html>  
  <body>  
    当前web应用的根目录名：${pageContext.request.contextPath}<br/>  
    <a href="${pageContext.request.contextPath}/el/03_el.jsp">点点</a><br/>  
    <%  
        session.setAttribute("NAME","哈哈");  
    %>      
    姓名：${sessionScope.NAME}<br/>  
    参数值：${param.name}<br/>  
    第三个爱好是：${paramValues.like[2]}<br/>  
    请求头1：${header.host}<br/>  
    请求头2：${headerValues["Accept-Encoding"][0]}<br/>  
    cookie的名：${cookie.PASS.name}<br/>  
    cookie的值：${cookie.PASS.value}<br/>  
    <hr/>  
    web初始化参数之driver为：${initParam.driver}<br/>  
    web初始化参数之url为：${initParam.url}<br/>  
      
  </body>  
</html>  
```

## JSP-JSTL标签库----函数fn

​    1，导入相关的jstl包

​    2，要在使用jstl的页面中用taglib指令引入相关包

```jsp
<%@ taglib uri="http://java.sun.com/jsp/jstl/functions" prefix="fn" %>  
	   fn:toLowerCase("str")       字符串变成小写
       fn:toUpperCase("str")       字符串变成大写
       fn:trim("str")                       去掉字符串两端空白
       fn:split("String","regex")     切割字符串
       fn:join("array","#")              用#把array中每个元素连接
       fn:indexOf("str","s")           返回第二个参数在第一个参数首次出现的位置    
       fn:contains("s1","s2")         返回第二个参数是否包含在第一个参数
       fn:startsWith("s1","s2")      第一个参数是否以第二个参数开头        
       fn:endsWith("s1","s2")       第一个参数是否以第二个参数结尾    
       fn:replace("s1","s2","s3")   把s1中的所有s2用s3替换
       fn:substring("s1",a,b)         把s1中的第a个字符到b-1个字符截取出来
       fn:substringAfter()              ${fn:substringAfter("www@163@com","@")}
       fn:substringBefore()           ${fn:substringBefore("www@163@com","@")}
```

## JSP-JSTL标签库---核心core

###     <c:out>标签 -----把内容输出到浏览器

```jsp
<%@ page language="java" import="java.util.*" pageEncoding="UTF-8"%>  
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>  
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">  
<html>  
  <head>  
   <%  
       pageContext.setAttribute("script", "<script>alert('哈哈');</script>");  
    %>  
  </head>  
   
  <body>  
  <!-- 
      escapeXml="false" 表示不转义js代码 
   -->  
     <c:out value="${script} }" escapeXml="false"/>  
  </body>  
</html>  
```

###   <c:set>标签 ---- 把数据绑定到域中

```jsp
<%@ page language="java" import="java.util.*" pageEncoding="UTF-8"%>  
<%@ page import="itcast.util.User" %>  
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>  
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">  
<html>  
  <head>  
    <title>My JSP '02_c_set.jsp' starting page</title>  
    <meta http-equiv="pragma" content="no-cache">  
    <meta http-equiv="cache-control" content="no-cache">  
    <meta http-equiv="expires" content="0">      
    <meta http-equiv="keywords" content="keyword1,keyword2,keyword3">  
    <meta http-equiv="description" content="This is my page">  
  
  </head>  
   
  <body>  
      <!--  
          class User{  
              private String id;  
              private String name;  
              public void setId(String id){  
                  this.id = id;  
              }  
              public void setName(String name){  
                  this.name = name;  
              }  
              public String getId(){  
                  return this.id;  
              }  
              public String getName(){  
                  return this.name;  
              }  
          }  
       -->  
    <%  
     User user = new User();  
     pageContext.setAttribute("USER", user);  
    %>  
    <c:set target="${pageScope.USER}" property="id" value="2015"/>  
    <c:set target="${pageScope.USER}" property="name" value="笨笨"/>  
      
    编号:${pageScope.USER.id}<br/>  
    姓名:${pageScope.USER.name}  
      
  </body>  
</html>  
```

### <c:remove>标签  

```jsp
<c:remove var="NAME" scope="page"/>  移除pageContext域中的名为NAME的数据
```

### <c:catch>标签

```jsp
<c:catch var="myError">   
           <% int i = 10/0; %>
    </c:catch>
    原因为：${myError.message}<br/>
```

### <c:if>标签

```jsp
<c:if test="判断条件">
 </c:if>
```

### <c:choose>和<c:when><c:otherwise>标签

```jsp
 <c:choose>
          <c:when test="">
          </c:when>
          <c:when test="">
          </c:when>
          <c:when test="">
          </c:when>
          <c:otherwise>
          </c:otherwise>
    </c:choose>    
```

### <c:forEach var items/>标签

```jsp
<%@ page language="java" pageEncoding="UTF-8"%>  
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>  
<%@ page import="java.util.*" %>  
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">  
<html>  
  <body>  
      
    <%  
        List<String> nameList = new ArrayList<String>();  
        nameList.add("小德子");  
        nameList.add("小格子");  
        nameList.add("小桌子");  
        nameList.add("小羊子");  
        nameList.add("小凳子");  
        pageContext.setAttribute("NAMELIST",nameList);      
    %>          
    <table border="2" align="center">          
        <tr>  
            <th>索引</th>  
            <th>编号</th>  
            <th>姓名</th>  
            <th>是第一个元素吗</th>  
            <th>是最后个元素吗</th>  
        </tr>          
        <c:forEach var="xxx" items="${NAMELIST}" varStatus="stat" begin="0" end="2">  
            <tr>  
                <td>  
                    ${stat.index}  
                </td>  
                <td>  
                    ${stat.count}  
                </td>  
                <td>  
                    ${xxx}  
                </td>  
                <td>  
                    ${stat.first}  
                </td>  
                <td>  
                    ${stat.last}  
                </td>  
            </tr>  
        </c:forEach>  
    </table>                  
  </body>  
</html>  
```

 ### <c:url>和<c:param>标签

```jsp
<!-- /表示当前web应用的根目录 -->
      <c:url var="downURL" value="/DownloadServlet">
          <!-- c:param负责编码 -->
          <c:param name="filename" value="三国123abc"/>
      </c:url>
    <a href="${downURL}" style="text-decoration:none">
        下载三图[GET]
    </a>
```

### <c:redirect>标签     

```jsp
  <c:redirect url="/core/07_c_forEach.jsp"/>
```

### jsp:forward标签  

```jsp
  <jsp:forward page="/core/07_c_forEach.jsp"/>   
```

## Jsp监听器
参考 http://blog.csdn.net/u011024652/article/details/52293932

## Jsp过滤器

参考 http://blog.csdn.net/u011024652/article/details/52186200

## Jsp分页

参考 http://blog.csdn.net/u011054333/article/details/54632265

## Jsp上传文件

参考 http://blog.csdn.net/sinat_34803353/article/details/53249354

# AJax
##  JSON 语法

------

JSON 语法是 JavaScript 语法的子集。

------

###   JSON 语法规则

JSON 语法是 JavaScript 对象表示语法的子集。

- 数据在名称/值对中
- 数据由逗号分隔
- 大括号保存对象
- 中括号保存数组

------

###   JSON 名称/值对

JSON 数据的书写格式是：名称/值对。

名称/值对包括字段名称（在双引号中），后面写一个冒号，然后是值：

`"name" : "菜鸟教程"`

这很容易理解，等价于这条 JavaScript 语句：

name = "菜鸟教程"

------

###   JSON 值

JSON 值可以是：

- 数字（整数或浮点数）
- 字符串（在双引号中）
- 逻辑值（true 或 false）
- 数组（在中括号中）
- 对象（在大括号中）
- null

------

###   JSON 数字

JSON 数字可以是整型或者浮点型：

`{ "age":30 }`

------

###   JSON 对象

JSON 对象在大括号（{}）中书写：

对象可以包含多个名称/值对：

`{ "name":"菜鸟教程" , "url":"www.runoob.com" }`

这一点也容易理解，与这条 JavaScript 语句等价：

`name = "菜鸟教程"url = "www.runoob.com"`

------

###   JSON 数组

JSON 数组在中括号中书写：

数组可包含多个对象：

```json
{"sites": [{ "name":"菜鸟教程" , "url":"www.runoob.com" }, { "name":"google" , "url":"www.google.com" }, { "name":"微博" , "url":"www.weibo.com" }]}
```

在上面的例子中，对象 "sites" 是包含三个对象的数组。每个对象代表一条关于某个网站（name、url）的记录。

------

###   JSON 布尔值

JSON 布尔值可以是 true 或者 false：

`{ "flag":true }`

------

###   JSON null

JSON 可以设置 null 值：

`{ "runoob":null }`

------

###   JSON 使用 JavaScript 语法

因为 JSON 使用 JavaScript 语法，所以无需额外的软件就能处理 JavaScript 中的 JSON。

通过 JavaScript，您可以创建一个对象数组，并像这样进行赋值：

------

###   JSON 文件

- JSON 文件的文件类型是 ".json"
- JSON 文本的 MIME 类型是 "application/json"

## JS中使用Json
参考： http://blog.csdn.net/xujie3/article/details/52954940

## Java对象和Json互转
参考： https://www.cnblogs.com/wangf-keep/p/6480019.html

## XMLHttpRequest对象
参考： http://www.w3school.com.cn/ajax/ajax_xmlhttprequest_create.asp

## 使用jQuery实现ajax
参考:
* https://www.cnblogs.com/jackcheblog/p/7065421.html
* https://www.cnblogs.com/starof/p/6434791.html

## Ajax获取Json数据
参考： https://www.cnblogs.com/zhangyongl/p/6399955.html
