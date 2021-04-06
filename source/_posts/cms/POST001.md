---
title: cms开发资料
tags: cms
category: cms
date: 2018-02-27 16:44:36
---
![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0147.jpg)

开发文档
<!--more-->

# 1. 系统架构概述

本系统核心架构为FreeMarker+hibernate+Spirng的mvc分层架构。

## 1.1分层架构模型

![img](http://ovi3ob9p4.bkt.clouddn.com/cms/cms002.png)

## 1.2数据流转模型(前端)

![img](http://ovi3ob9p4.bkt.clouddn.com/cms/cms004.png)

# 2.框架目录介绍

## 2.1Cms包介绍

| 具体包名                        | 相关功能描述                        |
| ------------------------------- | ----------------------------------- |
| com\mi\cms                      | 常量包                              |
| com\mi\cms\action               | 通用action抽象类包                  |
| com\mi\cms\action\directive     | cms自定义标签包,公用部分继承于abs包 |
| com\mi\cms\action\directive\abs | 公用部分自定义标签的抽象包          |
| com\mi\cms\action\front         | 前台action包                        |
| com\mi\cms\action\member        | 会员中心包                          |
| com\mi\cms\api                  |                                     |
| com\mi\cms\api\admin\assist     | Api辅助包                           |
| com\mi\cms\api\admin\main       | Api主要controller包                 |
| com\mi\cms\api\front            | API前端包                           |
| com\mi\cms\api\member           | Api会员中心包                       |
| com\mi\cms\dao\assist           | 辅助dao的接口层                     |
| com\mi\cms\dao\assist\impl      | 辅助dao的接口实现层                 |
| com\mi\cms\dao\main             | 核心的dao接口层                     |
| com\mi\cms\dao\main\impl        | 核心的dao接口实现层                 |
| com\mi\cms\entity\assist        | cms辅助实体类子类                   |
| com\mi\cms\entity\assist\base   | cms辅助实体类基类                   |
| com\mi\cms\entity\assist\hbm    | hibernate辅助实体类的关系映射文件   |
| com\mi\cms\entity\back          | cms备份实体类子类                   |
| com\mi\cms\entity\back\base     | cms备份实体类基类                   |
| com\mi\cms\entity\back\hbm      | hibernate备份实体类类的关系映射文件 |
| com\mi\cms\entity\main          | cms核心实体类子类                   |
| com\mi\cms\entity\main\base     | cms核心实体类基类                   |
| com\mi\cms\entity\main\hbm      | hibernate核心实体类的关系映射文件   |
| com\mi\cms\lucene               | 搜索引擎封装包                      |
| com\mi\cms\manager\assist       | 辅助的service接口层                 |
| com\mi\cms\manager\assist\impl  | 辅助的service接口实现层             |
| com\mi\cms\manager\main         | 核心的service接口层                 |
| com\mi\cms\manager\main\impl    | 核心的service接口实现层             |
| com\mi\cms\service              | service封装层                       |
| com\mi\cms\staticpage           | 静态页的封装包包                    |
| com\mi\cms\staticpage\exception | 静态页的异常处理类型                |
| com\mi\cms\statistic            | 内容发布统计包                      |
| com\mi\cms\statistic\workload   | 流量统计包                          |
| com\mi\cms\web                  | 拦截器包                            |
| com\mi\cms\task                 | 任务包                              |
| com\mi\cms\task\job             | 具体任务包                          |

 

## 2.2Common包介绍

| 具体包名                           | 相关功能描述            |
| ---------------------------------- | ----------------------- |
| com\mi\common\captcha              | 验证码封装包            |
| com\mi\common\dic                  | 分词                    |
| com\mi\common\email                | 邮件包                  |
| com\mi\common\fck                  | fck编辑器封装包         |
| com\mi\common\file                 | 文件操作封装包          |
| com\mi\common\hibernate4           | hibeinate封装包         |
| com\mi\common\image                | 图片的封装包            |
| com\mi\common\ipseek               | ip地址库封装            |
| com\mi\common\lucene               | 收索引擎的基础包        |
| com\mi\common\office               | 文档转换相关工具类      |
| com\mi\common\page                 | jeecms分页封装包        |
| com\mi\common\security             | 安全认证相关的包        |
| com\mi\common\security\annotation  |                         |
| com\mi\common\security\encoder     |                         |
| com\mi\common\security\rememberme  |                         |
| com\mi\common\security\userdetails |                         |
| com\mi\common\upload               | 上传封装包              |
| com\mi\common\util                 | 工具包                  |
| com\mi\common\web                  | 常量包                  |
| com\mi\common\web\freemarker       | freemarker视图封装包    |
| com\mi\common\web\session          | session包               |
| com\mi\common\web\session\cache    | session缓存包           |
| com\mi\common\web\springmvc        | springMVC的简单封装     |
| com\mi\cms\manager\main            | 核心的service接口层     |
| com\mi\cms\manager\main\impl       | 核心的service接口实现层 |
| com\mi\cms\service                 | service封装层           |
| com\mi\cms\staticpage              | 静态页的封装包          |
| com\mi\cms\staticpage\exception    | 静态页的异常处理类型    |
| com\mi\cms\statistic               | 流量统计包              |
| com\mi\cms\statistic\workload      | 内容发布统计包          |
| com\mi\cms\web                     | 拦截器包                |

 

## 2.3Core包介绍

| 具体包名                 | 相关功能描述                                 |
| ------------------------ | -------------------------------------------- |
| com\mi\core              | 一般常量和异常处理                           |
| com\mi\core\action\front | 核心前台的action                             |
| com\mi\core\dao          | 核心dao的接口                                |
| com\mi\core\dao\impl     | 核心dao的接口实现类                          |
| com\mi\core\entity       | 登录认证和核心用户的实体子类                 |
| com\mi\core\entity\base  | 登录认证和核心用户的实体基类                 |
| com\mi\core\entity\hbm   | 相关的hibernate的映射文件                    |
| com\mi\core\manager      | 核心的service接口层                          |
| com\mi\core\manager\impl | 核心的service接口实现层                      |
| com\mi\core\security     | 认证登录、退出相关                           |
| com\mi\core\tpl          | 模板接口和相关service层                      |
| com\mi\core\web          | 定义内容显示的接口，工具类，和错误页面的指定 |
| com\mi\core\web\util     | uri帮助类                                    |

## 2.4页面资源介绍

| 具体包名           | 相关功能描述                                                 |                   |
| ------------------ | ------------------------------------------------------------ | ----------------- |
| WebContent         |                                                              |                   |
| r                  | 前台资源文件，如css、img、js等                               |                   |
| jeeadmin           | 后台页面和资源文件（由vue源码工程打包）                      |                   |
| thirdparty         | 第三方插件（ckeditor编辑器、swf上传、My97DatePicker日期选择） |                   |
| u                  | 默认的用户上传资源目录                                       |                   |
| WebContent/WEB-INF |                                                              |                   |
| cache              | 缓存文件                                                     |                   |
| common             | 通用页面                                                     |                   |
| config             | 核心的配置文件，如果action，service,manager,dao,bean等  plug为插件配置文件目录 |                   |
| directive          | 标签向导模板文件                                             |                   |
| error              | 错误页面                                                     |                   |
| ftl                | 自定义的freemark宏文件                                       |                   |
| ispeek             | ip地址库                                                     |                   |
| languages          | 国际化配置                                                   |                   |
|                    | jeecms/admin                                                 | 后台              |
|                    | jeecms/front                                                 | 前台              |
|                    | jeecms/tpl                                                   | 模版              |
|                    | jeecore/admin                                                | 公用国际化        |
|                    | jeecms/adminapi                                              | API接口消息国际化 |
|                    | plug                                                         | 插件国际化配置    |
| lucene             | lucene文件夹                                                 |                   |
| t/cms              | 前台模板页面                                                 |                   |
| t/cms/www          | 站点资源目录                                                 |                   |
| t/cms/www/default  | 站点方案                                                     |                   |

 

# 3. 开发流程指导

## 3.1展现层

### 3.1.1Freemarer介绍

FreeMarker是一个用Java语言编写的模板引擎，它基于模板来生成文本输出。FreeMarker与Web容器无关，即在Web运行时，它并不知道Servlet或HTTP。它不仅可以用作表现层的实现技术，而且还可以用于生成XML，JSP或Java 等。

![img](http://ovi3ob9p4.bkt.clouddn.com/cms/cms006.png)

 

### 3.1.2后台页面

后台管理页面全部采用vue工程开发，参考

 4.后台管理页面vue工程开发](#_4.后台管理页面vue工程开发)

### 3.1.3前台模版页面

![img](http://ovi3ob9p4.bkt.clouddn.com/cms/cms008.png)

在default目录相应的文件夹添加html，在html文件中引入freemark机制控制control层传输对象的显示。

 

 

 

## 3.2控制层

### 3.2.1SpringMVC简介

Spring 框架提供了构建 Web 应用程序的全功能 MVC 模块。使用 Spring 可插入的 MVC 架构，通过策略接口，Spring 框架是高度可配置的，而且包含多种视图技术，例如 FreeMarker、JavaServer Pages（JSP）技术、Velocity、Tiles、iText和 POI。Spring MVC 框架并不知道使用的视图，所以不会强迫您只使用 JSP 技术。Spring MVC 分离了控制器、模型对象、分派器以及处理程序对象的角色，这种分离让它们更容易进行定制。

### 3.2.2控制层实现

#### 3.2.2.1配置SpringMVC支持

Web.xml配置Spring后台核心调度器

```xml
<servlet>

       <servlet-name>JeeCmsAdminApi</servlet-name>

       <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>

       <init-param>

           <param-name>dispatchOptionsRequest</param-name>

           <param-value>true</param-value>

        </init-param>

       <init-param>

           <param-name>contextConfigLocation</param-name>

           <param-value>

              /WEB-INF/config/jeecms-servlet-admin-api.xml

           </param-value>

       </init-param>  

       <load-on-startup>3</load-on-startup>

    </servlet>

```

Web.xml配置Spring后台核心调度器匹配路径

```xml
  <servlet-mapping>

       <servlet-name>JeeCmsAdminApi</servlet-name>

       <url-pattern>/api/admin/*</url-pattern>

    </servlet-mapping>
```

Web.xml配置Spring前台核心调度器

```xml
<servlet>

       <servlet-name>JeeCmsFront</servlet-name>

       <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>

       <init-param>

           <param-name>contextConfigLocation</param-name>

           <param-value>

              /WEB-INF/config/jeecms-servlet-front.xml

              /WEB-INF/config/plug/*/-servlet-front-action.xml

           </param-value>

       </init-param>

       <load-on-startup>2</load-on-startup>

    </servlet>
```

Web.xml配置Spring前台核心调度器匹配路径

```xml
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

```

#### 3.2.2.2Spring Controller

```java
com.mi.cms.api.admin.main核心管理模块Controller
com.mi.cms.api.admin.assist辅助管理模块Controller
@Controller

public class CmsLogApiAct {
         @RequestMapping("/log/operating_list")

         publicvoid list(String queryUsername, String queryTitle,Integer category,

                            StringqueryIp, Integer pageNo, Integer pageSize,HttpServletRequest request,

                            HttpServletResponseresponse){

                   Stringbody = "\"\"";

                   Stringmessage = Constants.API_MESSAGE_SUCCESS;

                   Stringcode = ResponseCode.API_CODE_CALL_SUCCESS;

                   if(pageNo==null) {

                            pageNo=1;

                   }

                   if(pageSize==null) {

                            pageSize=10;

                   }

                   if(category==null) {

                            category=CmsLog.OPERATING;

                   }

                   Paginationpage =null;

                   CmsSitesite = CmsUtils.getSite(request);

                   WebErrorserrors = WebErrors.create(request);

                   if(category.equals(CmsLog.OPERATING)) {

                            page= manager.getPage(CmsLog.OPERATING, site.getId(),queryUsername, queryTitle,queryIp, pageNo, pageSize);

                   }elseif (category.equals(CmsLog.LOGIN_SUCCESS)) {

                            page= manager.getPage(CmsLog.LOGIN_SUCCESS, null,queryUsername, queryTitle,queryIp, pageNo, pageSize);

                   }elseif (category.equals(CmsLog.LOGIN_FAILURE)) {

                            page= manager.getPage(CmsLog.LOGIN_FAILURE, null,null, queryTitle, queryIp, pageNo,pageSize);

                   }else{

                            errors.addErrorString(Constants.API_MESSAGE_PARAM_ERROR);

                   }

                   if(errors.hasErrors()) {

                            message= errors.getErrors().get(0);

                            code= ResponseCode.API_CODE_PARAM_ERROR;

                   }else{

                            inttotalCount = page.getTotalCount();

                            List<CmsLog>list = (List<CmsLog>) page.getList();

                            JSONArrayjsonArray = new JSONArray();

                            if(list!=null&&list.size()>0) {

                                     for(int i = 0; i < list.size(); i++) {

                                               jsonArray.put(i,list.get(i).convertToJson());

                                     }

                            }

                            body= jsonArray.toString()+",\"totalCount\":"+totalCount;

                   }

                   ApiResponseapiResponse = new ApiResponse(request, body, message, code);

                   ResponseUtils.renderApiJson(response,request, apiResponse);

         }

         //自动装配对象
         @Autowired
         privateCmsLogMng manager;
}
//统一返回JSON对象

ApiResponse apiResponse = newApiResponse(request, body, message, code);
ResponseUtils.renderApiJson(response,request, apiResponse);
```

另外一个例子

//需要签名认证（建议保存、修改、删除类方法添加签名注解）

```java
@SignValidate

@RequestMapping("/group/save")

public void save(CmsGroup bean,StringviewChannelIds,String contriChannelIds,

                            HttpServletRequestrequest,HttpServletResponse response){

                   Stringbody = "\"\"";

                   Stringmessage = Constants.API_MESSAGE_PARAM_REQUIRED;

                   Stringcode = ResponseCode.API_CODE_PARAM_REQUIRED;

                   WebErrorserrors = WebErrors.create(request);

                   //通用认证必填参数

                   errors= ApiValidate.validateRequiredParams(request, errors, bean.getName(),bean.getPriority(),

                                     bean.getAllowMaxFile(),bean.getAllowPerDay(),bean.getAllowFileSize(),bean.getAllowFileTotal());

                   if(!errors.hasErrors()) {

                            Integer[]viewChannel = StrUtils.getInts(viewChannelIds);

                            Integer[]contriChannel = StrUtils.getInts(contriChannelIds);

                            errors= validateArrayLength(errors, viewChannel, contriChannel);

                            if(errors.hasErrors()) {

                                     message= errors.getErrors().get(0);

                                     code= ResponseCode.API_CODE_PARAM_ERROR;

                            }else{

                                     bean.init();

                                     bean= manager.save(bean, viewChannel, contriChannel);

                                     log.info("saveCmsGroup id={}", bean.getId());

                                     cmsLogMng.operating(request,"cmsGroup.log.save", "id=" + bean.getId()

                                                        +";name=" + bean.getName());

                                     body="{\"id\":"+"\""+bean.getId()+"\"}";

                                     message =Constants.API_MESSAGE_SUCCESS;

                                     code= ResponseCode.API_CODE_CALL_SUCCESS;

                            }

                   }

                   ApiResponseapiResponse = new ApiResponse(request, body, message, code);

                   ResponseUtils.renderApiJson(response,request, apiResponse);

         }
```

#### 3.2.2.3SpringMVC扩展配置

`jeecms-servlet-admin-api.xml后台Spring国际化、拦截器、SpringMVC视图解析器配置`

`jeecms-servlet-front.xml前台Spring国际化、拦截器、SpringMVC视图解析器配置`

`jeecms-servlet-admin-api-action.xml 后台action配置文件`

`jeecms-servlet-front-action.xml前台action配置文件`

配置action bean

```xml
<bean id="ftpApiAct"class="com.mi.cms.api.admin.main.FtpApiAct"></bean>
```



## 3.3逻辑层

### 3.3.1类存放包介绍

`com.mi.cms.manager.main.impl核心service层`

`com.mi.cms.manager.assist.impl 辅助service层`

`com.mi.cms.service 采集、栏目、内容、流量公用service包`

### 3.3.2service类代码编写样例

```java
@Service

@Transactional

public class CmsConfigMngImplimplements CmsConfigMng {

         @Transactional(readOnly= true)

         publicCmsConfig get() {
                   CmsConfigentity = dao.findById(1);
                   returnentity;
         }
         publicvoid updateCountCopyTime(Date d) {

                   dao.findById(1).setCountCopyTime(d);

         }

 

         publicvoid updateCountClearTime(Date d) {

                   dao.findById(1).setCountClearTime(d);

         }

 

         publicCmsConfig update(CmsConfig bean) {

                   Updater<CmsConfig>updater = new Updater<CmsConfig>(bean);

                   CmsConfigentity = dao.updateByUpdater(updater);

                  entity.blankToNull();

                   returnentity;

         }

         publicMarkConfig updateMarkConfig(MarkConfig mark) {

                   get().setMarkConfig(mark);

                   returnmark;

         }

         publicvoid updateMemberConfig(MemberConfig memberConfig) {

                   get().getAttr().putAll(memberConfig.getAttr());

         }
         privateCmsConfigDao dao;

         //自动装配dao层

         @Autowired

         publicvoid setDao(CmsConfigDao dao) {

                   this.dao= dao;
         }
}
```

需要`@Service@Transactional`

注解标志该类为业务逻辑层，所有的service层均采用接口开发模式

`@Transactional(readOnly = true)只读事物`

### 3.3.3配置service bean

jeecms-context.xml中增加servicebean的配置

```xml
<bean   id="cmsConfigMng"class="com.mi.cms.manager.main.impl.CmsConfigMngImpl"/>
```

 

## 3.4持久层

### 3.4.1Hibernate4简介

Hibernate是一个开放源代码的对象关系映射框架，它对JDBC进行了非常轻量级的对象封装，使得Java程序员可以随心所欲的使用对象编程思维来操作数据库。 Hibernate可以应用在任何使用JDBC的场合，既可以在Java的客户端程序使用，也可以在Servlet/JSP的Web应用中使用

### 3.4.2DAO类代码编写样例

持久层采用Hibernate4，缓存采用Ehcache

`com.mi.cms.dao.main.impl 核心DAO层`

`com.mi.cms.dao.assist.impl 辅助DAO层`

```java
@Repository

public class CmsConfigDaoImplextends HibernateBaseDao<CmsConfig, Integer>

                   implementsCmsConfigDao {

         publicCmsConfig findById(Integer id) {

                   CmsConfigentity = get(id);

                   returnentity;

         }

         //重写getEntityClass方法

         @Override

         protectedClass<CmsConfig> getEntityClass() {
                   returnCmsConfig.class;
         }
}
```

@Repository注解标志该类是DAO层组件，可以选择继承HibernateBaseDao基础类，需要实现接口。

### 3.4.3配置DAO bean

jeecms-context.xml中增加dao bean的配置

```xml
<beanid="cmsConfigDao"class="com.mi.cms.dao.main.impl.CmsConfigDaoImpl"/>
```



### 3.4.4POJO

`com.mi.cms.entity.main核心功能包的pojo`

`com.mi.cms.entity.main.base 辅助功能包的pojo基础类`

`com.mi.cms.entity.main.hbm 辅助功能包Hibernate实体映射文件`

`com.mi.cms.entity.assist辅助功能包的pojo`

`com.mi.cms.entity.assist.base辅助功能包的pojo基础类`

`com.mi.cms.entity.assist.hbm辅助功能包Hibernate实体映射文件`

## 3.5自定义标签

### 3.5.1定义标签类

​         自定义标签类所属包com.mi.cms.action.directive

​         标签类需要实现Freemarker内置接口TemplateDirectiveModel     

​         获取标签参数可以用DirectiveUtils工具类获取

```java
public classChannelDirective implements TemplateDirectiveModel {

         /**

          * 输入参数，栏目ID。

          */

         publicstatic final String PARAM_ID = "id";

         /**

          * 输入参数，栏目路径。

          */

    public static final String PARAM_PATH ="path";

         /**

          * 输入参数，站点ID。存在时，获取该站点栏目，不存在时获取当前站点栏目。

          */

         publicstatic final String PARAM_SITE_ID = "siteId";

 

         @SuppressWarnings("unchecked")

         publicvoid execute(Environment env, Map params, TemplateModel[] loopVars,

                            TemplateDirectiveBodybody) throws TemplateException, IOException {

                   CmsSitesite = FrontUtils.getSite(env);

                   //getrequired params from directive

                   Integerid = DirectiveUtils.getInt(PARAM_ID, params);

                   Channelchannel;

                   if(id != null) {

                            channel= channelMng.findById(id);

                   }else {

                            Stringpath = DirectiveUtils.getString(PARAM_PATH, params);

                            if(StringUtils.isBlank(path)) {

                                     //如果path不存在，那么id必须存在。

                                     thrownew ParamsRequiredException(PARAM_ID);

                            }

                            IntegersiteId = DirectiveUtils.getInt(PARAM_SITE_ID, params);

                            if(siteId == null) {

                                     siteId= site.getId();

                            }

                            channel= channelMng.findByPathForTag(path, siteId);

                   }

 

                   Map<String,TemplateModel> paramWrap = new HashMap<String, TemplateModel>(

                                     params);

                   //putresult to view

                   paramWrap.put(OUT_BEAN,DEFAULT_WRAPPER.wrap(channel));

                   Map<String,TemplateModel> origMap = DirectiveUtils

                                     .addParamsToVariable(env,paramWrap);         

                   //render result to response

                   body.render(env.getOut());

                   DirectiveUtils.removeParamsFromVariable(env,paramWrap, origMap);

         }

         //装配所需service

         @Autowired

         privateChannelMng channelMng;

}
```



### 3.5.2配置标签

1.jeecms-context.xml配置Spring bean

```xml
<bean       id="cms_content_list"class="com.mi.cms.action.directive.ContentListDirective"/>
```

2.jeecms-context 文件中jeecms.properties文件中引入自定义标签bean

directive.cms_channel_list=cms_channel_list

### 3.5.3标签应用

```html
[@cms_content_list count='9'titLen='15' orderBy='8' channelOption='1' channelId='1']

                  [#list tag_list as a]

                  <li><ahref="${a.url}" title="${a.title}"target="_blank">[@text_cut s=a.title len=titLenappend=append/]</a></li>

                  [/#list]

 [/@cms_content_list]
```

cms_content_list标签名称count、titLen、orderBy、channelOption、channelId标签参数

tag_list标签结果

a循环变量

${a.url}输出a对象的url属性

 

# 4.后台管理页面vue工程开发

## 4.1环境准备

Vue简介

Vue.js提供一个官方命令行工具，可用于快速搭建大型单页应用。该工具提供开箱即用的构建工具配置，带来现代化的前端开发流程。只需几分钟即可创建并启动一个带热重载、保存时静态检查以及可用于生产环境的构建配置的项目：

推荐查阅官方文档https://cn.vuejs.org/v2/guide/installation.html

1、安装必要环境Node.js   下载地址 <http://nodejs.cn/>.   

安装最新版本Npm（最新版node自带）

2、开发环境安装和启动

使用命令行执行以下命令   执行安装node包

cd  项目路径（vue工程路径）

npminstall （推荐使用cnpm  install，<http://npm.taobao.org/>  加快依赖下载速度）

npmrun dev; 

## 4.2工程包介绍

目录结构：

![img](http://ovi3ob9p4.bkt.clouddn.com/cms/cms010.png)

![img](http://ovi3ob9p4.bkt.clouddn.com/cms/cms012.png)

一级目录说明：

build webpack打包配置文件

config  全局配置文件api域名 appid配置

                                           node_modules:node依赖文件夹

                                              src项目源代码
                                          static 全局资源文件夹（css images js）

index.html 入口文件

二级目录说明:

\1. config／dev.env.js   开发环境配置文件，修改源代码时会读取此文件中的配置项

  config／prod.env.js   生产环境配置文件，打包完成时会读取使用此文件中的配置项

![img](http://ovi3ob9p4.bkt.clouddn.com/cms/cms014.png)

![img](http://ovi3ob9p4.bkt.clouddn.com/cms/cms016.png)

以上图（config／dev.env.js）为例，定义了6个属性

  NODE_ENV :标识当前环境 （当前环境为开发者环境）

  BaseUrl：项目的api资源 地址

  Appid：项目的appid 

  aesKey：项目的aesKey (加密用)

  ivKey：项目的ivKey (加密用)

  appKey：项目的appKey (加密用)

配置好相应的属性 即可在任意js文件中使用该变量，调用方式为

process.env.xxx

process.env.baseUrl

 

 ![img](http://ovi3ob9p4.bkt.clouddn.com/cms/cms018.png)

2.

src/api  api以及全局拦截器

src/assets  源码中的资源文件（JS,CSS,iamges）

src/components  自定义组件目录

src/directive  自定义指令目录

src/mixins   自定义混合目录

src/plugs   第三方插件目录（jquery相关的插件可存放在此）

src/router    路由，角色目录

src/store    vuex 定义目录

src/untils  全局方法，签名，加密函数存放目录

src/views   功能页面目录

src/APP.vue  根组件（无需修改）

src/config.js  项目路径配置文件

src/main.js   页面入口文件

src/permissions.js   全局路由钩子配置文件

## 4.3新增功能模块讲解 

 

### 4.3.1新增页面

后台管理页面采用单页组件化开发

功能模块统一存放在src／views目录下，推荐以导航树层级管理页面，例如下图

![img](http://ovi3ob9p4.bkt.clouddn.com/cms/cms020.png)

1功能某块统一以单文件组件的形式构造，命名以.vue结尾，单文件组件开发详情查看<https://cn.vuejs.org/v2/guide/single-file-components.html>

2.页面基础样式框架基于element-ui2.x版本

3.页面详解：

此处以用户列表功能为事例说明

用户功能列表路径为src/views/userMange/list.vue



### 4.3.2数据交互

后台数据交互采用axios,更多

<https://www.npmjs.com/package/axios>（推荐）

<https://www.kancloud.cn/yunye/axios/234845>（汉化文档）

1.创建数据请求方法 (请求方法统一存放在src/api.js文件下)

2.将所有的接口地址统一存放在src/api.js下如图

 ![img](http://ovi3ob9p4.bkt.clouddn.com/cms/cms022.png)![img](http://ovi3ob9p4.bkt.clouddn.com/cms/cms024.png)

 

 如图：api.js 定义了一个setAdminApi函数 用于拼接重复的api地址

 api文档中以/api/admin/为路径的则可使用该函数 ，否则直接填写完整路径

例如  bsaeUpload:'/api/member/upload/o_upload',//普通文件上传

3．数据请求示例 

   1.普通未封装请求接口示例

   `在页面的<script>下引用 axios`

         ![img](http://ovi3ob9p4.bkt.clouddn.com/cms/cms026.png)         

然后在需要的方法中调用axios请求 此处以created方法中调用为示例 （具体功能，请根据自身情况修改使用）     

```js
 axios.post(this.$api.memberList,this.params)

      .then(res=>{~!

        //此处为请求成功的回调

      })

      .catch(error=>{

       //此处为请求失败的回调 

      })

```

说明：*axios.post()方法可以发送一个post 请求，其他请求类型有 get ，put ，delete等，具体参考axios文档*

*this.$api.memberList  此参数为api地址 在页面中使用 this.​$api.XXX 即可调用src*/api中的 地址变量   

此方法为获取所有用户列表信息，传递了一个参数对象params: {//只需要业务参数

​       

```js
 queryUsername: "",

        queryEmail: "",

        queryGroupId: "",

        queryStatus: "",

        pageNo: "",

        pageSize: ""

      }

```

在成功的回调函数中，可以将列表数据赋值给自定义的一个变量，在页面中调用该变量即可，

 

2 列表封装接口示例

  本系统封装了一个list和一个form 混合

![img](http://ovi3ob9p4.bkt.clouddn.com/cms/cms028.png)

此类文件的作用为 将一些公用的方法和变量插入页面组件中，在页面则可直接使用该变量以及方法

以src/views/userMange/list.vue为例

`在页面<script></script>中引入mixins`

![img](http://ovi3ob9p4.bkt.clouddn.com/cms/cms030.png)      

此处引入了 list.js 此文件预先定义了基础列表所需要的变量以及方法，简单的列表查询及操作，可直接使用该混合文件，具体变量及方法，查看文件内注释

在页面中的created方法中 调用了

`created(){`

`     this.initTableData(this.$api.memberList,this.params);`

  `}`

该方法为混合方法，作用为初始化列表信息，第一个参数传递的为api接口地址，第二个参数为自定义参数对象，调用该函数后会自动初始化列表以及赋值，在页面上直接使用tableData属性即可

 

 

 

数据加密方法介绍

如业务参数需要加密，则需要在页面组件中引用src/untils/aes.js

调用示例：

   

```
import { Encrypt } from "../untils/aes";

  let aesKey = this.$store.state.aesKey

    letivKey = this.$store.state.ivKey;

               letaesPassword = Encrypt(this.params.pwd, aesKey, ivKey);          

```



### 4.3.3将组件添加至路由表

页面编写完成后需要将组件注册到路由中

关于路由采用的是vue-router，更多详细：<https://router.vuejs.org/zh-cn/index.html> 

路由表文件为src/router/routes.js

 

```js
{

    path:'/',

    name:'用户管理', //用户管理

    component: body,

    iconCls: 'icon-user',

    meta:{

      isLink: true

    },

    children: [{

      path:'/user',

      name:'会员管理', //会员管理

      component: childView,

      isParent: true,

      redirect: '/user/list',

      children: [{

         path: '/user/list', //会员列表

         name: '会员列表',

         component:resolve => { require(['@/views/user/userMange/list.vue'], resolve) },

       },

       {

         path: '/user/save', //会员添加

         name: '会员添加',

         component:resolve => { require(['@/views/user/userMange/add.vue'], resolve) },

         hidden: true

       },

       {

         path: '/user/update', //会员添加

         name: '会员修改',

         component:resolve => { require(['@/views/user/userMange/edit.vue'], resolve) },

         hidden: true

       }

      ]

    }, {
```

`path：访问地址（唯一不可重复）`

meta：{role:’xxxx’}角色权限名,不可重复，而且不同板块前缀要不同，比如用户模块叫user   另外会员管理叫userMember  这样是不行的   

`name:’路由名称’,  【也就是菜单名】`

`component:  component:resolve => { require(['@/views/user/userMange/list.vue'], resolve) },`

`懒加载页面组件，在点击该功能时才加载文件`

`children:子路由`

### 4.3.4权限控制

权限文件为src/routers/roles.js

角色权限编是一个按照路由层级来构造的json对象（自定义添加时，也保持同样的规则）

以用户列表角色为例 

```js
  {

    name:'用户管理',

    role:'userlist',

    api:[

      '/api/admin/user/list',

      '/api/admin/user/get'

    ],

    children: [{

      name:'添加',

      role:'useradd',

      api:[

        '/api/admin/user/save'

      ],

    },

    {

      name:'修改',

      role:'useredit',

      api:['/api/admin/user/update'],

    },

    {

      name:'删除',

      role:'userdelete',

      api:[

        '/api/admin/user/delete'

      ],

    },

    ]
```

name：角色权限名称（角色管理中树状结构的名字）

role：页面显示权限（就是路由表中的meta：{role:’xxxx’}）

api：页面组件所拥有的调用后台api的地址

 

### 4.3.5 组件使用

vue是一个组件化开发的js 一切皆为组件

在本系统中页面基础组件采用了element2.0组件库 (页面中以el-xxxx开头)

自定义组件为自行封装（页面中以cms-xxxx开头）

自定义组件存放在

![img](http://ovi3ob9p4.bkt.clouddn.com/cms/cms032.png)

开发中，一些公用的功能可以封装为自定义组件

此处以cms-input组件为例

![img](http://ovi3ob9p4.bkt.clouddn.com/cms/cms034.png)

 

组件页面与功能页面结构完全相同

```js
< 
template>

    <divclass="cms-inline-input">

        <labelfor=""class="cms-inline-label">{{label}}:</label>

         <el-input  :value="currentValue"@input="handleInput"

         :style="{width:width+'px'}"

         ></el-input>

    </div>

</template>

<script>

exportdefault {

    name:'cms-input',

    props:{

         value:'',

         label:{

          type:String,

            default:'label'

         },

         width:{

              type:Number,

              default:160

         }

    },

    data(){

        return{

          currentValue:this.value

        }

    },

    methods:{

    handleInput(value){

       this.$emit('input',value);//触发input 事件，并传入新值

    }

    }

}

</script> 

<stylescoped lang="scss">

    .cms-inline-input{

        display:inline-block;

        position:relative;

        margin-left:12px;

    }

    .cms-inline-label{

        font-size:14px;

        color:#5a5e66;

    }

</style>
```

此处封装了一个标签和输入框，对外props 暴露了3个属性 value  label width

在页面上使用方法为   

`<cms-inputlabel=’标签名’ width=’120’  v-model=’xxx’></cms-input>`

`通过this.$emit('input',value);//触发input 事件，并传入新值`

 `向使用者传递返回值` 

 

## 4.4 后台页面发布

后台源码的开发文件不可直接运行在浏览器中，需要通过命令打包将源码生成为静态文件

1.使用命令行工具进入 工程目录

![img](http://ovi3ob9p4.bkt.clouddn.com/cms/cms036.png)

2.   项目工程中如果没有node_modules文件夹，则执行

npminstall （推荐使用cnpm  install，<http://npm.taobao.org/>  加快依赖下载速度）

如果有则忽略此步骤

3.执行命令npm run build

 

出现building for production 则成功执行，等待命令完成完成后会出现下图

![img](http://ovi3ob9p4.bkt.clouddn.com/cms/cms038.png)

出现上图，说明打包编译完成，在项目中会有一个dist文件夹

![img](http://ovi3ob9p4.bkt.clouddn.com/cms/cms040.png)

dist文件夹中有两个文件

![img](http://ovi3ob9p4.bkt.clouddn.com/cms/cms042.png)

4、将index.html改名为index.do后，将index.do和static文件夹放到ROOT下的/jeeadmin/jeecms文件夹下，eclipse下放到/WebContent/jeeadmin/jeecms下

 

## 4.5修改后台地址

1.带部署路径和端口的此类情况无须修改，如下

<http://xxx.jeecms.com/jeeadmin/jeecms/index.do>

<http://xxx.jeecms.com/project/jeeadmin/jeecms/index.do>

\2. 需要调整后台路径此类情况须要修改

http://xxx.jeecms.com/admin/jeecms/index.do

<http://xxx.jeecms.com/project/admin/jeecms/index.do>

此处修改源码工程中的src/config.js 

   

 ![img](http://ovi3ob9p4.bkt.clouddn.com/cms/cms044.png)

  将jeeadmin文件夹重命名为admin即可
