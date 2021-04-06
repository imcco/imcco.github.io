---
title: JEECMS自定义标签
tags:
  - Java
  - cms
  - jeecms
copyright: true
category: backend
abbrlink: 25712
date: 2017-09-04 15:34:36
---
![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0030.jpg)

CMS是"Content Management System"的缩写，意为"内容管理系统"。 内容管理系统是企业信息化建设和电子政务的新宠，也是一个相对较新的市场。对于内容管理，业界还没有一个统一的定义，不同的机构有不同的理解。
<!--more-->
### **自定义标签**[**mycontent_list**]实现步骤:
#### **创建jc_mycontent的表**

```sql
-- Create table
create table JC_MYCONTENT
(
  id      NUMBER not null,
  title   VARCHAR2(250),
  content VARCHAR2(250)
)
tablespace CMS
  pctfree 10
  initrans 1
  maxtrans 255
  storage
  (
    initial 64K
    minextents 1
    maxextents unlimited
  );
-- Create/Recreate primary, unique and foreign key constraints 
alter table JC_MYCONTENT
  add constraint PK_ID primary key (ID)
  using index 
  tablespace CMS
  pctfree 10
  initrans 2
  maxtrans 255;

```

#### **创建实体类**

```java
package com.jeecms.cms.entity.main;

public class MyContent {
	private Integer id;
	private String title;
	private String content;

	public Integer getId() {
		return id;
	}

	public void setId(Integer id) {
		this.id = id;
	}

	public String getTitle() {
		return title;
	}

	public void setTitle(String title) {
		this.title = title;
	}

	public String getContent() {
		return content;
	}

	public void setContent(String content) {
		this.content = content;
	}

	public MyContent(Integer id, String title, String content) {
		super();
		this.id = id;
		this.title = title;
		this.content = content;
	}

	public MyContent() {
		super();
	}

}
```


#### **接下来是配置hibernate中jc_mycontent表的配置文件**

```xml
<?xml version="1.0"?>
<!DOCTYPE hibernate-mapping PUBLIC "-//Hibernate/Hibernate Mapping DTD//EN" "http://hibernate.sourceforge.net/hibernate-mapping-3.0.dtd">
<hibernate-mapping package="com.jeecms.cms.entity.main">
	<class name="MyContent" table="jc_mycontent">
		<meta attribute="sync-DAO">false</meta>
		<cache usage="read-write" />
		<id name="id" type="java.lang.Integer" column="id">
			<generator class="identity" />
		</id>
		<property name="title" column="title" type="java.lang.String"
			not-null="true" />
		<property name="content" column="content" type="java.lang.String"
			not-null="true" />
	</class>
</hibernate-mapping>
```


#### **持久层接口**

```java
package com.jeecms.cms.dao.main;

import java.util.List;

import com.jeecms.cms.entity.main.MyContent;

public interface MyContentDao {
	public List<MyContent> getList();
}
```

#### **持久层实现类**

```java
package com.jeecms.cms.dao.main.impl;

import java.util.List;

import org.springframework.stereotype.Repository;

import com.jeecms.cms.dao.main.MyContentDao;
import com.jeecms.cms.entity.main.MyContent;
import com.jeecms.common.hibernate4.Finder;
import com.jeecms.common.hibernate4.HibernateBaseDao;

@Repository
// 持久层
public class MyContentDaoImpl extends HibernateBaseDao<MyContent, Integer> implements MyContentDao {
	@SuppressWarnings("unchecked")
	public List<MyContent> getList() {
		return find(byNothing());
	}

	private Finder byNothing() {
		Finder f = Finder.create();
		f.append("from MyContent");// 可以在此处添加查询条件或者添加各种方法进行动态查询
		f.setCacheable(true);
		return f;
	}

	@Override
	protected Class<MyContent> getEntityClass() {
		return MyContent.class;
	}
}
```

#### **业务层接口**

```java
package com.jeecms.cms.manager.main;

import java.util.List;

public interface MyContentMng {
  public List getList();
}public interface MyContentMng {
  public List getList();
}
```


#### **业务层实现类**

```java
package com.jeecms.cms.manager.main.impl;

import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

import com.jeecms.cms.dao.main.MyContentDao;
import com.jeecms.cms.entity.main.MyContent;
import com.jeecms.cms.manager.main.MyContentMng;
import com.jeecms.cms.service.ContentListener;

@Service
// ()业务层
@Transactional
public class MyContentMngImpl implements MyContentMng {
	@Transactional(readOnly = true)
	// 配置事务为只读
	public List<MyContent> getList() {
		return myContentDao.getList();
	}

	private MyContentDao myContentDao;

	@Autowired
	// 自动绑定
	public void setMyContentDao(MyContentDao myContentDao) {
		this.myContentDao = myContentDao;
	}

	private List<ContentListener> listenerList;

	@Autowired
	public void setListenerList(List<ContentListener> listenerList) {
		this.listenerList = listenerList;
	}
}
```

#### **标签类的抽象类**

最主要的就是getData这个方法，以及绑定业务层(其中也可以添加多种查询方法，可参考类AbstractContentDirective )。

```java
package com.jeecms.cms.action.directive.abs;

import java.util.Map;

import org.springframework.beans.factory.annotation.Autowired;

import com.jeecms.cms.manager.main.MyContentMng;

import freemarker.core.Environment;
import freemarker.template.TemplateDirectiveModel;
import freemarker.template.TemplateException;

public abstract class AbstractMyContentDirective implements TemplateDirectiveModel {
	protected Object getData(Map params, Environment env) throws TemplateException {
		return myContentMng.getList();
	}

	@Autowired
	protected MyContentMng myContentMng;
}
```
#### **标签工具类DirectiveUtils下定义输出参数:MYOUT_LIST**      

```java
public static final String MYOUT_LIST = "mytag_list";
```

#### **自定义标签中最重要的类继承上边的抽象类**

```java
package com.jeecms.cms.action.directive;

import java.io.IOException;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

import com.jeecms.cms.action.directive.abs.AbstractMyContentDirective;
import com.jeecms.cms.entity.main.MyContent;

import static com.jeecms.common.web.freemarker.DirectiveUtils.MYOUT_LIST;
import com.jeecms.common.web.freemarker.DefaultObjectWrapperBuilderFactory;
import com.jeecms.common.web.freemarker.DirectiveUtils;
import com.jeecms.core.entity.CmsSite;
import com.jeecms.core.web.util.FrontUtils;

import freemarker.core.Environment;
import freemarker.template.TemplateDirectiveBody;
import freemarker.template.TemplateException;
import freemarker.template.TemplateModel;

public class MyContentListDirective extends AbstractMyContentDirective {
	/**
	 * 模板名称
	 */
	public static final String TPL_NAME = "mycontent_list";

	@SuppressWarnings("unchecked")
	public void execute(Environment env, @SuppressWarnings("rawtypes") Map params,
			TemplateModel[] loopVars, TemplateDirectiveBody body) throws TemplateException,
			IOException {
		// 获取站点
		CmsSite site = FrontUtils.getSite(env);
		// 获取内容列表
		List<MyContent> list = getList(params, env);
		Map<String, TemplateModel> paramWrap = new HashMap<String, TemplateModel>(params);
		// OUT_LIST值为tag_list，将内容列表放入其中
		paramWrap.put(MYOUT_LIST, DefaultObjectWrapperBuilderFactory.getDefaultObjectWrapper()
				.wrap(list));

		// 将params的值复制到variable中

		Map<String, TemplateModel> origMap = DirectiveUtils.addParamsToVariable(env, paramWrap);
		// 没有采用默认的模板，直接采用自己写的简单的模板（mycontent_list.html）
		FrontUtils.includeTpl(TPL_NAME, site, params, env);
		// 将variable中的params值移除
		DirectiveUtils.removeParamsFromVariable(env, paramWrap, origMap);
	}

	@SuppressWarnings("unchecked")
	protected List<MyContent> getList(Map<String, TemplateModel> params, Environment env)
			throws TemplateException {
		return myContentMng.getList();
	}
}
```

#### **在jeecms-context.xml中声明标签**

```xml
<bean id="cms_mycontent_list" class="com.jeecms.cms.action.directive.MyContentListDirective"/>
```

#### **在jeecms-context.xml中注入DAO**

```xml
<bean id="myContentDao" class="com.jeecms.cms.dao.main.impl.MyContentDaoImpl"/>
```

#### **在jeecms-context.xml中注入Manager**

```xml
<bean id="myContentMng" class="com.jeecms.cms.manager.main.impl.MyContentMngImpl"/>
```

#### **配置文件jeecms-servlet-front.xml中有一段对标签的配置**    

#### **jeecms.properties中配置标签名**

```xml
directive.cms_mycontent_list=cms_mycontent_list
```

#### **新建模板**

WEB-INF\t\cms\www\oa\tag下新建模板mycontent_list.html,并加入如下代码(里边也可以自己添加一些样式，可参考WEB-INF\t\cms_sys_defined\style_list下样式文件)

```xml
[#list mytag_list as a]
	<li>
  <a href="${a.title}">"${a.content}"</a>
	</li>
[/#list]
```

#### **调用代码**

```html
				[@cms_mycontent_list]
					[#list mycontent_list as a]
							<li>
						  <a href="${a.title}">"${a.content}"</a>
							</li>
					[/#list]
				[/@cms_mycontent_list]
```

通过以上这些代码，实现将自己的表jc_mycontent中的数据查询并显示在页面上
