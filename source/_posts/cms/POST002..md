---
title: 库内新增对象的流程及其他技巧
tags: cms
category: cms
date: 2018-03-10 16:44:36
---
![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0150.jpg)

cms开发 ———— 库内新增对象Products的流程说明及其他技巧
<!--more-->
### 第一步：Entity

1. `com.jeecms.cms.entity.assist.base`下建立模型基础类`BaseCmsProducts.java`
2. `com.jeecms.cms.entity.assist`  下建立对象类继承继承模型CmsProducts.java
3. `com.jeecms.cms.entity.assist.hbm` 配置hibernate对象映射CmsProducts.hbm.xml
4. `src .ehcache-hibernate.xml` 缓存对象配置：

```xml
 <cache name="com.jeecms.cms.entity.assist.CmsProducts" 
maxElementsInMemory="100" eternal="false" timeToIdleSeconds="600" timeToLiveSeconds="7200" overflowToDisk="true"/>
```

### 第二步：Dao

1. `com.jeecms.cms.dao.assist`数据库交互持久层DAO接口和实现`ProductsDao.java` `ProductsDaoImpl.java` 
2. 配置`WebRoot/WEB-INF/config/jeecms-context.xml`文件:

```xml
<bean id="cmsproductsDao" 
class="com.jeecms.cms.dao.assist.impl.CmsProductsDaoImpl"/>
```

### 第三步：Service

1. `com.jeecms.cms.manager.assist`业务层接口和实现，`ProductsMng.java , ProductsMngImpl.java`
2. 配置`WebRoot/WEB-INF/config/jeecms-context.xml`文件:

```xml
<bean id="cmsProductsMng" 
class="com.jeecms.cms.manager.assist.impl.CmsProductsMngImpl"/>
```

### 第四步：Action

1. `com.jeecms.cms.action.front` 写Action与前台对接，`ProductsAct.java`
2. XML配置：`jeecms-servlet-front-action.xml` 

```xml
<bean id="productsAct" 
class="com.jeecms.cms.action.front.ProductsAct"/>
```

3. `com.jeecms.cms.action.admin.assist` （加、删、改）写Action与后台对接`CmsProductsAct.java` 
4. XML配置：`jeecms-servlet-admin-action.xml` 

```xml
<bean id="cmsProductsAct" 
class="com.jeecms.cms.action.admin.assist.CmsProductsAct"/>
```

### 第五步：Directive

1. `com.jeecms.cms.action.directive` (设置并返回标签对象`[@cms_products_page]`)
2. 配置`WebRoot/WEB-INF/config/jeecms-context.xml`文件:

```xml
<bean id="cms_products_page" 

class="com.jeecms.cms.action.directive.CmsProductsDirective"/>

<bean id="staticPageSvc" class="com.jeecms.cms.staticpage.StaticPageSvcImpl">

    <property name="tplMessageSource" ref="tplMessageSource"/>

    <property name="freeMarkerConfigurer">

      <bean class="org.springframework.web.servlet.view.freemarker.FreeMarkerConfigurer">

        <property name="freemarkerVariables">

          <map>
```

3.  此处添加标签的配置信息：

   ```xml
        <entry key="cms_products_page" value-ref="cms_products_page"/>
   ```

4. 配置`WebRoot/WEB-INF/config/ Jeecms-servlet-front.xml`文件:

```xml
<bean id="freemarkerConfig" class="org.springframework.web.servlet.view.freemarker.FreeMarkerConfigurer">
    <property name="freemarkerVariables">
      <map>
```

5. 此处添加标签的配置信息： 

```xml
<entry key="cms_products_page" value-ref="cms_products_page" />
```

### 特别注意点：

1. 对象模板位置控制：`com.jeecms.cms.action.front.ProductsAct.java`

```java
// 方案路径site.getSolutionPath()=”/WEB-INF/t/cms/www/default”
// TPLDIR_SPECIAL="special" 模板位置
// PRODUCTS_INDEX= "tpl.productsIndex"; 模板名称
return FrontUtils.getTplPath(request, site.getSolutionPath(),TPLDIR_SPECIAL, PRODUCTS_INDEX);
```

2. `PRODUCTS_INDEX`对象需要在国际化处进行设置：`WebRoot/WEB-INF/languages/jeecms_tpl/messages_zh_CN.properties` ：

```xml
tpl.productsIndex=products_index
```

### 国际化文件说明：

1. `WEB-INF/languages/fck`  友情提示
2. `WEB-INF/languages/jeecms_admin`  (后台)功能页面，如：首页》内容统计 `statistic.function.content=内容统计`

3. `WEB-INF/languages/jeecms_front`  (前台)友情提示，如：验证码错误。

4. `WEB-INF/languages/jeecms_tpl`    (前台)功能页面，如：投票内容页， `tpl.tagDetail=voteIndex`

5. `WEB-INF/languages/jeecore_admin` 功能按钮、提示信息，如：`global.submit=提交，global.confirm.logout=您确定退出吗？`

### 修改后台访问地址

将`jeeadmin/jeecms/index.do` 改为`admin/index.do`为例

1. 修改`WebContent\WEB-INF\web.xml` 

```xml
<servlet-mapping>  
  <servlet-name>JeeCmsAdmin</servlet-name>  
  <url-pattern>/jeeadmin/jeecms/*</url-pattern>  
 </servlet-mapping>  
```

改为 

```xml
<servlet-mapping> 
  <servlet-name>JeeCmsAdmin</servlet-name> 
  <url-pattern>/admin/*</url-pattern> 
  </servlet-mapping> 
```

2. 修改`WebContent\WEB-INF\config\jeecms-servlet-admin.xml` 

```xml
<entry key="appBase" value="/jeeadmin/jeecms"/> 
```

改为 

```xml
<entry key="appBase" value="/admin"/> 
```

3. 修改`WebContent\WEB-INF\config\shiro-context.xml` 

把 

```xml
  *.jspx = anon 
  *.jhtml = anon 
  /member/forgot_password.jspx = anon 
  /member/password_reset.jspx = anon 
  /login.jspx = authc 
  /logout.jspx = logout 
  /member/** = user 
  /jeeadmin/jeecms/login.do = authc 
  /jeeadmin/jeecms/logout.do = logout 
  /jeeadmin/jeecms/** =user 
```

改为              

```xml
*.jspx = anon 
  *.jhtml = anon 
  /member/forgot_password.jspx = anon 
  /member/password_reset.jspx = anon 
  /login.jspx = authc 
  /logout.jspx = logout 
  /member/** = user 
  /admin/login.do = authc 
  /admin/logout.do = logout 
  /admin/** =user 
```

把 

```xml
<property name="adminLogin" value="/jeeadmin/jeecms/login.do"/>  
<property name="adminPrefix" value="/jeeadmin/jeecms/"/>  
```

改为 

```xml
<property name="adminLogin" value="/admin/login.do"/> 
<property name="adminPrefix" value="/admin/"/> 
```

把 

```xml
<property name="adminIndex" value="/jeeadmin/jeecms/index.do"/> 
```


改为 

```xml
<property name="adminIndex" value="/admin/index.do"/> 
```

3. 修改`\src\com\jeecms\cms\web\AdminContextInterceptor.java` 

把

```java
  private static String getURI(HttpServletRequest request) throws IllegalStateException { 
        UrlPathHelper helper = new UrlPathHelper(); 
        String uri = helper.getOriginatingRequestUri(request); 
        String ctxPath = helper.getOriginatingContextPath(request); 
        int start = 0, i = 0, count = 2 
        if (!StringUtils.isBlank(ctxPath)) { 
            count++; 
        } 
        while (i < count && start != -1) { 
            start = uri.indexOf('/', start + 1); 
            i++; 
        } 
    if (start <= 0) { 
        throw new IllegalStateException("admin access path not like '/jeeadmin/jeecms/...' pattern: " + uri); 
    } 
    return uri.substring(start); 
} 
```
改为 


```java
private static String getURI(HttpServletRequest request) throws IllegalStateException { 
        UrlPathHelper helper = new UrlPathHelper(); 
        String uri = helper.getOriginatingRequestUri(request); 
        String ctxPath = helper.getOriginatingContextPath(request); 
        // int start = 0, i = 0, count = 2;修改 
        int start = 0, i = 0, count = 1; 
        if (!StringUtils.isBlank(ctxPath)) { 
            count++; 
        } 
        while (i < count && start != -1) { 
            start = uri.indexOf('/', start + 1); 
            i++; 
        }   
    if (start <= 0) { 
        throw new IllegalStateException("admin access path not like '/admin/...' pattern: " + uri); 
    } 
    return uri.substring(start); 
}
```
### 通过数据库修改密码

1. 通过数据库修改admin密码

```sql
select * from core_user;
|       1 | admin      | jobar     | 0230504dd5de96d2f6784d45d1bc7633 |
```

密码已经是被加密过的了。

2. 密码加密类：`com.ponyjava.common.util.Md5PwdEncoder`

 例如我想将密码设为“zhaozh",就先用这个类加密，然后更新数据库就ok了。

```java
public class Test {
	public static void main(String[] args) {
		Md5PwdEncoder encoder = new Md5PwdEncoder();
		System.out.println(encoder.encodePassword("zhaozh"));
	}
}
```

输出为：`f06238ff925a61f9c62de7d64c64bad3`

mysql>

```sql
 update core_user set password='f06238ff925a61f9c62de7d64c64bad3' where user_id='1';
```

3. 再次登录就ok了。
