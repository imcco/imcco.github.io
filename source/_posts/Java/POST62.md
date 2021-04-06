---
title: XSS跨站脚本攻击漏洞修复
tags:
  - xss
category: bug
abbrlink: 26499
date: 2017-11-30 16:34:36
---
![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0014.jpg)

​	跨站脚本攻击(Cross Site Scripting)，为了不和层叠样式表(Cascading Style Sheets, CSS)的缩写混淆，故将跨站脚本攻击缩写为XSS。恶意攻击者往Web页面里插入恶意Script代码，当用户浏览该页之时，嵌入其中Web里面的Script代码会被执行，从而达到恶意攻击用户的目的。
​	XSS攻击分成两类，一类是来自内部的攻击，主要指的是利用程序自身的漏洞，构造跨站语句，如:dvbbs的showerror.asp存在的跨站漏洞。另一类则是来自外部的攻击，主要指的自己构造XSS跨站漏洞网页或者寻找非目标机以外的有跨站漏洞的网页。如当我们要渗透一个站点，我们自己构造一个有跨站漏洞的网页，然后构造跨站语句，通过结合其它技术，如社会工程学等，欺骗目标服务器的管理员打开。
XSS分为：存储型和反射型
​	存储型XSS：存储型XSS，持久化，代码是存储在服务器中的，如在个人信息或发表文章等地方，加入代码，如果没有过滤或过滤不严，那么这些代码将储存到服务器中，用户访问该页面的时候触发代码执行。这种XSS比较危险，容易造成蠕虫，盗窃cookie（虽然还有种DOM型XSS，但是也还是包括在存储型XSS内）。
​	反射型XSS：非持久化，需要欺骗用户自己去点击链接才能触发XSS代码（服务器中没有这样的页面和内容），一般容易出现在搜索页面。
<!--more-->

##### web.xml里面加入XssFilter

<filter>

```java
	<filter-name>XssFilter</filter-name>
	<filter-class>com.jeecms.common.web.XssFilter</filter-class>
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
		<param-value>\'@\"@＼@＃@：@％@＞</param-value>
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
	<filter-name>XssFilter</filter-name>
	<url-pattern>*.jspx</url-pattern>
</filter-mapping>
<filter-mapping>
	<filter-name>XssFilter</filter-name>
	<url-pattern>/jeeadmin/jeecms/login.do</url-pattern>
</filter-mapping>
<filter-mapping>
	<filter-name>encoding</filter-name>
	<url-pattern>*.*</url-pattern>
</filter-mapping>
```
##### XssFilter类源码

```java
import java.io.IOException;
import javax.servlet.Filter;
import javax.servlet.FilterChain;
import javax.servlet.FilterConfig;
import javax.servlet.ServletException;
import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;
import javax.servlet.http.HttpServletRequest;
public class XssFilter implements Filter {
	private String filterChar;
	private String replaceChar;
	private String splitChar;
	FilterConfig filterConfig = null;
	public void init(FilterConfig filterConfig) throws ServletException {
		this.filterChar=filterConfig.getInitParameter("FilterChar");
		this.replaceChar=filterConfig.getInitParameter("ReplaceChar");
		this.splitChar=filterConfig.getInitParameter("SplitChar");
		this.filterConfig = filterConfig;
	}
	public void destroy() {
		this.filterConfig = null;
	}
	public void doFilter(ServletRequest request, ServletResponse response,
	FilterChain chain) throws IOException, ServletException {
		chain.doFilter(new XssHttpServletRequestWrapper((HttpServletRequest) request,filterChar,replaceChar,splitChar), response);
	}
}
```

##### XssFilter实现方法

```java
import static com.jeecms.common.web.Constants.UTF8;
import java.io.UnsupportedEncodingException;
import java.net.URLDecoder;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletRequestWrapper;
import org.apache.commons.lang.StringUtils;
/**
 * @author Tom
 */
public class XssHttpServletRequestWrapper extends HttpServletRequestWrapper {
	private String[]filterChars;
	private String[]replaceChars;
	public XssHttpServletRequestWrapper(HttpServletRequest request,String filterChar,String replaceChar,String splitChar) {
		super(request);
		if(filterChar!=null&&filterChar.length()>0){
			filterChars=filterChar.split(splitChar);
		}
		if(replaceChar!=null&&replaceChar.length()>0){
			replaceChars=replaceChar.split(splitChar);
		}
	}
	public String getQueryString() {
		String value = super.getQueryString();
		if (value != null) {
			value = xssEncode(value);
		}
		return value;
	}
	/**
	 * 覆盖getParameter方法，将参数名和参数值都做xss过滤。<br/>
	 * 如果需要获得原始的值，则通过super.getParameterValues(name)来获取<br/>
	 * getParameterNames,getParameterValues和getParameterMap也可能需要覆盖
	 */
	public String getParameter(String name) {
		String value = super.getParameter(xssEncode(name));
		if (value != null) {
			value = xssEncode(value);
		}
		return value;
	}

	public String[] getParameterValues(String name) {
		String[]parameters=super.getParameterValues(name);
		if (parameters==null||parameters.length == 0) {
			return null;
		}
		for (int i = 0; i < parameters.length; i++) {
			parameters[i] = xssEncode(parameters[i]);
		}
		return parameters;
	}
	
	/**
	 * 覆盖getHeader方法，将参数名和参数值都做xss过滤。<br/>
	 * 如果需要获得原始的值，则通过super.getHeaders(name)来获取<br/> getHeaderNames 也可能需要覆盖
	 */
	public String getHeader(String name) {

		String value = super.getHeader(xssEncode(name));
		if (value != null) {
			value = xssEncode(value);
		}
		return value;
	}
	
	/**
	 * 将容易引起xss漏洞的半角字符直接替换成全角字符
	 * 
	 * @param s
	 * @return
	 */
	private  String xssEncode(String s) {
		if (s == null || s.equals("")) {
			return s;
		}
		//%作为特殊解码字符，get方式提交的汉字+%会解码不了
		try {
			s = URLDecoder.decode(s, UTF8);
		} catch (UnsupportedEncodingException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}catch(Exception e){
			
		}
		for (int i = 0; i < filterChars.length; i++) {
			if(s.contains(filterChars[i])){
				s=s.replace(filterChars[i], replaceChars[i]);
			}
		}
		return s;
	}
}
```

##### 相关资料：XSS漏洞解析：https://www.secpulse.com/archives/48976.html
