---
title: 代码生成Tools
tags: CMS
category: CMS
date: 2018-03-14 16:44:36
---
![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0152.jpg)

技术点：采用SpringMVC3+Spring3+Hibernate3+Freemarker主流技术架构
<!--more-->
### 生成入口方法

```java
package com.jeecms.cms.template;


public class CmsModuleGenerator {
	private static String packName = "com.jeecms.cms.template";
	private static String fileName = "jeecms.properties";

	public static void main(String[] args) {
		new ModuleGenerator(packName, fileName).generate();
	}
}
```

### 生成方法

```java
package com.jeecms.cms.template;

import java.io.File;
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.IOException;
import java.util.Properties;
import java.util.Set;

import org.apache.commons.io.FileUtils;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

/**
 * 模块生成器
 * 
 * <p>
 * 用于生成JEE模块。
 * <p>
 * 包括JAVA类：action,dao,dao.impl,manager,manager.impl；
 * 配置文件：action配置,spring配置；ftl页面：list.html,add.html,edit.html。
 * 数据验证文件：Act-Com_save
 * -validation.xml,Act-Com_edit-validation.xml,Act-Com_update-validation.xml
 * <p>
 * 可设置的参数有：模块实体类名、java类包地址、配置文件地址、ftl页面地址。
 */
public class ModuleGenerator {
	private static final Logger log = LoggerFactory
			.getLogger(ModuleGenerator.class);
	public static final String SPT = File.separator;

	public static final String ENCODING = "UTF-8";

	private Properties prop = new Properties();
	
	private String packName;
	private String fileName;
	private File daoImplFile;
	private File daoFile;
	private File managerFile;
	private File managerImplFile;
	private File actionFile;
	private File pageListFile;
	private File pageEditFile;
	private File pageAddFile;

	private File daoImplTpl;
	private File daoTpl;
	private File managerTpl;
	private File managerImplTpl;
	private File actionTpl;
	private File pageListTpl;
	private File pageEditTpl;
	private File pageAddTpl;

	public ModuleGenerator(String packName, String fileName) {
		this.packName = packName;
		this.fileName = fileName;
	}

	@SuppressWarnings("unchecked")
	private void loadProperties() {
		try {
			log.debug("packName=" + packName);
			log.debug("fileName=" + fileName);
			FileInputStream fileInput = new FileInputStream(getFilePath(
					packName, fileName));
			prop.load(fileInput);
			String entityUp = prop.getProperty("Entity");
			log.debug("entityUp:" + entityUp);
			if (entityUp == null || entityUp.trim().equals("")) {
				log.warn("Entity not specified, exit!");
				return;
			}
			String entityLow = entityUp.substring(0, 1).toLowerCase()
					+ entityUp.substring(1);
			log.debug("entityLow:" + entityLow);
			prop.put("entity", entityLow);
			if (log.isDebugEnabled()) {
				Set ps = prop.keySet();
				for (Object o : ps) {
					log.debug(o + "=" + prop.get(o));
				}
			}
		} catch (FileNotFoundException e) {
			e.printStackTrace();
		} catch (IOException e) {
			e.printStackTrace();
		}
	}

	private void prepareFile() {
		String daoImplFilePath = getFilePath(prop.getProperty("dao_impl_p"),
				prop.getProperty("Entity") + "DaoImpl.java");
		daoImplFile = new File(daoImplFilePath);
		log.debug("daoImplFile:" + daoImplFile.getAbsolutePath());

		String daoFilePath = getFilePath(prop.getProperty("dao_p"), prop
				.getProperty("Entity")
				+ "Dao.java");
		daoFile = new File(daoFilePath);
		log.debug("daoFile:" + daoFile.getAbsolutePath());

		String managerFilePath = getFilePath(prop.getProperty("manager_p"),
				prop.getProperty("Entity") + "Mng.java");
		managerFile = new File(managerFilePath);
		log.debug("managerFile:" + managerFile.getAbsolutePath());

		String managerImplFilePath = getFilePath(prop
				.getProperty("manager_impl_p"), prop.getProperty("Entity")
				+ "MngImpl.java");
		managerImplFile = new File(managerImplFilePath);
		log.debug("managerImplFile:" + managerImplFile.getAbsolutePath());
		String actionFilePath = getFilePath(prop.getProperty("action_p"), prop
				.getProperty("Entity")
				+ "Act.java");
		actionFile = new File(actionFilePath);
		log.debug("actionFile:" + actionFile.getAbsolutePath());

		String pagePath = "WebContent/WEB-INF/"
				+ prop.getProperty("config_sys") + "/"
				+ prop.getProperty("config_entity") + "/";
		pageListFile = new File(pagePath + "list.html");
		log.debug("pageListFile:" + pageListFile.getAbsolutePath());
		pageEditFile = new File(pagePath + "edit.html");
		log.debug("pageEditFile:" + pageEditFile.getAbsolutePath());
		pageAddFile = new File(pagePath + "add.html");
		log.debug("pageAddFile:" + pageAddFile.getAbsolutePath());
	}

	private void prepareTemplate() {
		String tplPack = prop.getProperty("template_dir");
		log.debug("tplPack:" + tplPack);
		daoImplTpl = new File(getFilePath(tplPack, "dao_impl.txt"));
		daoTpl = new File(getFilePath(tplPack, "dao.txt"));
		managerImplTpl = new File(getFilePath(tplPack, "manager_impl.txt"));
		managerTpl = new File(getFilePath(tplPack, "manager.txt"));
		actionTpl = new File(getFilePath(tplPack, "action.txt"));
		pageListTpl = new File(getFilePath(tplPack, "page_list.txt"));
		pageAddTpl = new File(getFilePath(tplPack, "page_add.txt"));
		pageEditTpl = new File(getFilePath(tplPack, "page_edit.txt"));
	}

	private static void stringToFile(File file, String s) throws IOException {
		FileUtils.writeStringToFile(file, s, ENCODING);
	}

	private void writeFile() {
		try {
			if ("true".equals(prop.getProperty("is_dao"))) {
				stringToFile(daoImplFile, readTpl(daoImplTpl));
				stringToFile(daoFile, readTpl(daoTpl));
			}
			if ("true".equals(prop.getProperty("is_manager"))) {
				stringToFile(managerImplFile, readTpl(managerImplTpl));
				stringToFile(managerFile, readTpl(managerTpl));
			}
			if ("true".equals(prop.getProperty("is_action"))) {
				stringToFile(actionFile, readTpl(actionTpl));
			}
			if ("true".equals(prop.getProperty("is_page"))) {
				stringToFile(pageListFile, readTpl(pageListTpl));
				stringToFile(pageAddFile, readTpl(pageAddTpl));
				stringToFile(pageEditFile, readTpl(pageEditTpl));
			}
		} catch (IOException e) {
			log.warn("write file faild! " + e.getMessage());
		}
	}

	private String readTpl(File tpl) {
		String content = null;
		try {
			content = FileUtils.readFileToString(tpl, ENCODING);
			Set<Object> ps = prop.keySet();
			for (Object o : ps) {
				String key = (String) o;
				String value = prop.getProperty(key);
				content = content.replaceAll("\\#\\{" + key + "\\}", value);
			}
		} catch (IOException e) {
			log.warn("read file faild. " + e.getMessage());
		}
		return content;

	}

	private String getFilePath(String packageName, String name) {
		log.debug("replace:" + packageName);
		String path = packageName.replaceAll("\\.", "/");
		log.debug("after relpace:" + path);
		return "src/" + path + "/" + name;
	}

	public void generate() {
		loadProperties();
		prepareFile();
		prepareTemplate();
		writeFile();
	}

	public static void main(String[] args) {
		String packName = "com.jeecms.common.developer.template";
		String fileName = "template.properties";
		new ModuleGenerator(packName, fileName).generate();
	}
}
```

### Entity模板
	使用hibernateMapping插件生成

### Dao模板

​	dao.txt

```java
package #{dao_p};

import com.jeecms.common.hibernate4.Updater;
import com.jeecms.common.page.Pagination;
import #{entity_p}.#{Entity};

public interface #{Entity}Dao {
	public Pagination getPage(int pageNo, int pageSize);

	public #{Entity} findById(Integer id);

	public #{Entity} save(#{Entity} bean);

	public #{Entity} updateByUpdater(Updater<#{Entity}> updater);

	public #{Entity} deleteById(Integer id);
}
```

### DaoImpl模板

​	dao_impl.txt

```java
package #{dao_impl_p};

import org.hibernate.Criteria;
import org.springframework.stereotype.Repository;

import com.jeecms.common.hibernate4.HibernateBaseDao;
import com.jeecms.common.page.Pagination;
import #{dao_p}.#{Entity}Dao;
import #{entity_p}.#{Entity};

@Repository
public class #{Entity}DaoImpl extends HibernateBaseDao<#{Entity}, Integer> implements #{Entity}Dao {
	public Pagination getPage(int pageNo, int pageSize) {
		Criteria crit = createCriteria();
		Pagination page = findByCriteria(crit, pageNo, pageSize);
		return page;
	}

	public #{Entity} findById(Integer id) {
		#{Entity} entity = get(id);
		return entity;
	}

	public #{Entity} save(#{Entity} bean) {
		getSession().save(bean);
		return bean;
	}

	public #{Entity} deleteById(Integer id) {
		#{Entity} entity = super.get(id);
		if (entity != null) {
			getSession().delete(entity);
		}
		return entity;
	}
	
	@Override
	protected Class<#{Entity}> getEntityClass() {
		return #{Entity}.class;
	}
}
```

### Manager模板

​	manager.txt

```java
package #{manager_p};

import com.jeecms.common.page.Pagination;
import #{entity_p}.#{Entity};

public interface #{Entity}Mng {
	public Pagination getPage(int pageNo, int pageSize);

	public #{Entity} findById(Integer id);

	public #{Entity} save(#{Entity} bean);

	public #{Entity} update(#{Entity} bean);

	public #{Entity} deleteById(Integer id);
	
	public #{Entity}[] deleteByIds(Integer[] ids);
}
```

### ManagerImpl模板

​	manager_impl.txt

```java
package #{manager_impl_p};

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

import com.jeecms.common.hibernate4.Updater;
import com.jeecms.common.page.Pagination;
import #{dao_p}.#{Entity}Dao;
import #{entity_p}.#{Entity};
import #{manager_p}.#{Entity}Mng;

@Service
@Transactional
public class #{Entity}MngImpl implements #{Entity}Mng {
	@Transactional(readOnly = true)
	public Pagination getPage(int pageNo, int pageSize) {
		Pagination page = dao.getPage(pageNo, pageSize);
		return page;
	}

	@Transactional(readOnly = true)
	public #{Entity} findById(Integer id) {
		#{Entity} entity = dao.findById(id);
		return entity;
	}

	public #{Entity} save(#{Entity} bean) {
		dao.save(bean);
		return bean;
	}

	public #{Entity} update(#{Entity} bean) {
		Updater<#{Entity}> updater = new Updater<#{Entity}>(bean);
		bean = dao.updateByUpdater(updater);
		return bean;
	}

	public #{Entity} deleteById(Integer id) {
		#{Entity} bean = dao.deleteById(id);
		return bean;
	}
	
	public #{Entity}[] deleteByIds(Integer[] ids) {
		#{Entity}[] beans = new #{Entity}[ids.length];
		for (int i = 0,len = ids.length; i < len; i++) {
			beans[i] = deleteById(ids[i]);
		}
		return beans;
	}

	private #{Entity}Dao dao;

	@Autowired
	public void setDao(#{Entity}Dao dao) {
		this.dao = dao;
	}
}
```

### Action模板 

​	action.txt

```java
package #{action_p};

import static com.jeecms.common.page.SimplePage.cpn;

import javax.servlet.http.HttpServletRequest;

import org.apache.shiro.authz.annotation.RequiresPermissions;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.ModelMap;
import org.springframework.web.bind.annotation.RequestMapping;

import #{entity_p}.#{Entity};
import com.jeecms.core.entity.CmsSite;
import #{manager_p}.#{Entity}Mng;
import com.jeecms.core.web.util.CmsUtils;
import com.jeecms.core.web.WebErrors;
import com.jeecms.common.page.Pagination;
import com.jeecms.common.web.CookieUtils;

@Controller
public class #{Entity}Act {
	private static final Logger log = LoggerFactory.getLogger(#{Entity}Act.class);

	@RequiresPermissions("#{config_entity}:v_list")
	@RequestMapping("/#{config_entity}/v_list.do")
	public String list(Integer pageNo, HttpServletRequest request, ModelMap model) {
		Pagination pagination = manager.getPage(cpn(pageNo), CookieUtils
				.getPageSize(request));
		model.addAttribute("pagination",pagination);
		model.addAttribute("pageNo",pagination.getPageNo());
		return "#{config_entity}/list";
	}

	@RequiresPermissions("#{config_entity}:v_add")
	@RequestMapping("/#{config_entity}/v_add.do")
	public String add(ModelMap model) {
		return "#{config_entity}/add";
	}

	@RequiresPermissions("#{config_entity}:v_edit")
	@RequestMapping("/#{config_entity}/v_edit.do")
	public String edit(Integer id, Integer pageNo, HttpServletRequest request, ModelMap model) {
		WebErrors errors = validateEdit(id, request);
		if (errors.hasErrors()) {
			return errors.showErrorPage(model);
		}
		model.addAttribute("#{entity}", manager.findById(id));
		model.addAttribute("pageNo",pageNo);
		return "#{config_entity}/edit";
	}

	@RequiresPermissions("#{config_entity}:o_save")
	@RequestMapping("/#{config_entity}/o_save.do")
	public String save(#{Entity} bean, HttpServletRequest request, ModelMap model) {
		WebErrors errors = validateSave(bean, request);
		if (errors.hasErrors()) {
			return errors.showErrorPage(model);
		}
		bean = manager.save(bean);
		log.info("save #{Entity} id={}", bean.getId());
		return "redirect:v_list.do";
	}

	@RequiresPermissions("#{config_entity}:o_update")
	@RequestMapping("/#{config_entity}/o_update.do")
	public String update(#{Entity} bean, Integer pageNo, HttpServletRequest request,
			ModelMap model) {
		WebErrors errors = validateUpdate(bean.getId(), request);
		if (errors.hasErrors()) {
			return errors.showErrorPage(model);
		}
		bean = manager.update(bean);
		log.info("update #{Entity} id={}.", bean.getId());
		return list(pageNo, request, model);
	}

	@RequiresPermissions("#{config_entity}:o_delete")
	@RequestMapping("/#{config_entity}/o_delete.do")
	public String delete(Integer[] ids, Integer pageNo, HttpServletRequest request,
			ModelMap model) {
		WebErrors errors = validateDelete(ids, request);
		if (errors.hasErrors()) {
			return errors.showErrorPage(model);
		}
		#{Entity}[] beans = manager.deleteByIds(ids);
		for (#{Entity} bean : beans) {
			log.info("delete #{Entity} id={}", bean.getId());
		}
		return list(pageNo, request, model);
	}

	private WebErrors validateSave(#{Entity} bean, HttpServletRequest request) {
		WebErrors errors = WebErrors.create(request);
		CmsSite site = CmsUtils.getSite(request);
		bean.setSite(site);
		return errors;
	}
	
	private WebErrors validateEdit(Integer id, HttpServletRequest request) {
		WebErrors errors = WebErrors.create(request);
		CmsSite site = CmsUtils.getSite(request);
		if (vldExist(id, site.getId(), errors)) {
			return errors;
		}
		return errors;
	}

	private WebErrors validateUpdate(Integer id, HttpServletRequest request) {
		WebErrors errors = WebErrors.create(request);
		CmsSite site = CmsUtils.getSite(request);
		if (vldExist(id, site.getId(), errors)) {
			return errors;
		}
		return errors;
	}

	private WebErrors validateDelete(Integer[] ids, HttpServletRequest request) {
		WebErrors errors = WebErrors.create(request);
		CmsSite site = CmsUtils.getSite(request);
		if (errors.ifEmpty(ids, "ids")) {
			return errors;
		}
		for (Integer id : ids) {
			vldExist(id, site.getId(), errors);
		}
		return errors;
	}

	private boolean vldExist(Integer id, Integer siteId, WebErrors errors) {
		if (errors.ifNull(id, "id")) {
			return true;
		}
		#{Entity} entity = manager.findById(id);
		if(errors.ifNotExist(entity, #{Entity}.class, id)) {
			return true;
		}
		if (!entity.getSite().getId().equals(siteId)) {
			errors.notInSite(#{Entity}.class, id);
			return true;
		}
		return false;
	}
	
	@Autowired
	private #{Entity}Mng manager;
}
```

### 页面增加模板

​	page_add.txt

```html
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8"/>
<title></title>
<#include "/#{config_sys}/head.html"/>
<script type="text/javascript">
$(function() {
	$("#jvForm").validate();
});
</script>
</head>
<body>
<div class="box-positon">
	<div class="rpos"><@s.m "global.position"/>: <@s.m "#{entity}.function"/> - <@s.m "global.add"/></div>
	<form class="ropt">
		<input class="return-button" type="submit" value="<@s.m "global.backToList"/>" onclick="this.form.action='v_list.do';"/>
	</form>
	<div class="clear"></div>
</div>
<div class="body-box">
<@p.form id="jvForm" action="o_save.do">
<@p.text label="#{entity}.name" name="name" required="true" class="required" maxlength="100"/>
<@p.td><@p.submit code="global.submit"/> &nbsp; <@p.reset code="global.reset"/></@p.td>
</@p.form>
</div>
</body>
</html>
```

### 页面修改模板

​	page_edit.txt

```html
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8"/>
<title></title>
<#include "/#{config_sys}/head.html"/>
<script type="text/javascript">
$(function() {
	$("#jvForm").validate();
});
</script>
</head>
<body>
<div class="box-positon">
	<div class="rpos"><@s.m "global.position"/>:  <@s.m "#{entity}.function"/> - <@s.m "global.edit"/></div>
	<form class="ropt">
		<input class="return-button" type="submit" value="<@s.m "global.backToList"/>" onclick="this.form.action='v_list.do';"/>
	</form>
	<div class="clear"></div>
</div>
<div class="body-box">
<@p.form id="jvForm" action="o_update.do">
<@p.text label="#{entity}.name" name="name" value=#{entity}.name required="true" class="required" maxlength="100"/>
<@p.td><@p.hidden name="id" value=#{entity}.id/><@p.submit code="global.submit"/> &nbsp; <@p.reset code="global.reset"/></@p.td>
</@p.form>
</div>
</body>
</html>
```

### 页面列表模板

​	page_list.txt

```html
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8"/>
<title></title>
<#include "/#{config_sys}/head.html"/>
<script type="text/javascript">
function getTableForm() {
	return document.getElementById('tableForm');
}
function optDelete() {
	if(Pn.checkedCount('ids')<=0) {
		$.alert("<@s.m 'global.prompt'/>","<@s.m 'error.checkRecord'/>");
		return;
	}
	$.confirm(doDelete,"<@s.m 'global.confirm'/>","<@s.m 'global.confirm.delete'/>");
}
function doDelete(){
	var f = getTableForm();
	f.action="o_delete.do";
	f.submit();
}
</script>
</head>
<body>
<div class="box-positon">
	<div class="rpos"><@s.m "global.position"/>: <@s.m "#{entity}.function"/> - <@s.m "global.list"/></div>
	<form class="ropt">
		<input class="add" type="submit" value="<@s.m "global.add"/>" onclick="this.form.action='v_add.do';"/>
	</form>
	<div class="clear"></div>
</div>
<div class="body-box">
<form id="tableForm" method="post">
<input type="hidden" name="pageNo" value="${pageNo!}"/>
<@p.table value=pagination;#{entity},i,has_next><#rt/>
	<@p.column title="<input type='checkbox' onclick='Pn.checkbox(\"ids\",this.checked)'/>" width="20">
		<input type='checkbox' name='ids' value='${#{entity}.id}'/><#t/>
	</@p.column><#t/>
	<@p.column title="ID">${#{entity}.id}</@p.column><#t/>
	<@p.column code="#{entity}.name">${#{entity}.name}</@p.column><#t/>
	<@p.column code="global.operate" align="center">
		<a href="v_edit.do?id=${#{entity}.id}&pageNo=${pageNo!}" class="pn-opt"><@s.m "global.edit"/></a> | <#rt/>
		<a href="o_delete.do?ids=${#{entity}.id}&pageNo=${pageNo!}" onclick="if(!$.confirmToUrl(this.href)) {return false;}" class="pn-opt"><@s.m "global.delete"/></a><#t/>
	</@p.column><#t/>
</@p.table>
<div><input class="del-button" type="button" value="<@s.m "global.delete"/>" onclick="optDelete();"/></div>
</form>
</div>
<#include "/common/alert_message.html"/>
</body>
</html>
```

### 使用

`com.jeecms.cms.template.CmsModuleGenerator.java`打开后`Run As—java application`
