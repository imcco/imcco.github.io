---
title: Java Listener实现在线统计人数和总访问量
tags:
  - Java
  - Listener
copyright: true
category: Java
abbrlink: 61929
date: 2017-12-13 22:34:36
---
![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0008.jpg)

监听器用于监听web应用中某些对象、信息的创建、销毁、增加，修改，删除等动作的发生，然后作出相应的响应处理。当范围对象的状态发生变化的时候，服务器自动调用监听器对象中的方法。常用于统计在线人数和在线用户，系统加载时进行信息初始化，统计网站的访问量等等。
 <!--more-->
    分类：

    按监听的对象划分，可以分为

ServletContext对象监听器
HttpSession对象监听器
ServletRequest对象监听器
 

    按监听的事件划分

对象自身的创建和销毁的监听器
对象中属性的创建和消除的监听器
session中的某个对象的状态变化的监听器


    示例：用监听器统计网站在线人数

    原理：每当有一个访问连接到服务器时，服务器就会创建一个session来管理会话。那么我们就可以通过统计session的数量来获得当前在线人数。
<!--more-->
监听器应用一般统计在线人数,闲着没事，写个笔记，我们项目中是这样做的

首先定义一个servlet 这个servelt 在服务器启动时，就会调用，初始化：
## 实现累计访问量的一个方法
### 1.web.xml的配置

```xml
<!-- added by li'n for 登陆页面自动读取访问数  on 2013-04-15  the beginning -->
   <servlet>
       <servlet-name>VisitCounts</servlet-name>
       <servlet-class>com.sysware.framework.login.VisitCounts</servlet-class>
       <load-on-startup>0</load-on-startup>
   </servlet>
   <!-- added by li'n for 登陆页面自动读取访问数 on 2013-04-15  the end -->

```
### ２.项目登录页面可以获取访问本项目的总人数（init方法）

```java
package com.sysware.framework.login;
 
import java.io.BufferedReader;
import java.io.File;
import java.io.FileReader;
import java.io.IOException;
 
import javax.servlet.http.HttpServlet;
 
/**
 * @version 1.0
 * @author li'n on 系统工程部
 * @since 2013-04-15 17:00
 *
 */
public class VisitCounts extends HttpServlet{
     
    /**
     *
     */
    private static final long serialVersionUID = -6737156348721467846L;
    //累计访问量
    public static int counts = 0;
 
    public static void setCounts(int counts) {
        VisitCounts.counts = counts;
    }
     
    /**
     *
     * @return 历史访问量
     */
    public int getCounts() {
        return counts;
    }
 
    /**
     * 服务器启动时读取文件中的历史访问量
     */
    public void init()
    {
        //获取visitCount.txt的文件路径
        String p = this.getClass().getClassLoader().getResource("").getPath();
        File file = new File(p+File.separator +"visitCount.txt");
         
        //读取文件中的数字，即系统的历史访问量
        if (file.exists()) {
            readFile(file);
        } else {
            try {
                file.createNewFile();
                readFile(file);
            } catch (IOException e) {
                e.printStackTrace();
            }
        }  
    }
     
    /**
     * 读取文件file中的访问量数据
     */
    @SuppressWarnings("static-access")
    public void readFile(File file){
        BufferedReader reader = null;
        String tempString = null;
            try {
                reader = new BufferedReader(new FileReader(file));
                    while((tempString = reader.readLine()) != null)
                    {
                        VisitCounts.counts = Integer.parseInt(tempString);
                        System.out.println();
                        this.setCounts(counts);
                    }
            } catch (Exception e) {
                // TODO Auto-generated catch block
                e.printStackTrace();
            }
         
    }
     
 
     
}

```
### ３.在用户登录时，数量加１并用流写到文件里面（doLogin方法里面处理）

```java
if(loginInfo.isSuccess())
        {
            //added by li'n for 访问量统计 on 2013-04-19 the beginning
            //登录成功，访问量加1
            VisitCounts.setCounts(VisitCounts.counts  + 1);
            //将访问量更新到文件visitCount.txt
            writeFile();
            //added by li'n for 访问量统计 on 2013-04-19 the end
        }<br>/**<br>     * 将访问量更新到文件visitCount.txt<br>     * @return  历史访问量<br>     * @author li'n on 系统工程部<br>     * @since 2013-04-19 <br>     */<br>    public void writeFile(){<br>        <br>        FileWriter filewrite = null;<br>        try {<br>            <br>            //获得文件路径<br>            filewrite = new FileWriter(this.getClass().getClassLoader().getResource("").getPath()+File.separator +"visitCount.txt");<br>            <br>            //将访问量写入文件<br>            int o = VisitCounts.counts;<br>            filewrite.write(o + "\r\n");<br>            filewrite.flush();<br>            filewrite.close();<br>        } catch (Exception e) {<br>            e.printStackTrace();<br>        }<br><br>    }

```
以上，是实现累计访问量的一个方法；

下面说说实现在线人数，这个就要用到监听器的方式
## 实现在线人数

### 首先还是在　web.xml中配置你所写的监听器

```xml
<!-- 在线用户session监听器 -->
   <listener>         
       <listener-class>com.sysware.framework.login.OnlineSessionListener</listener-class>
   </listener>
```
### ２.监听器类

```java
package com.sysware.framework.login;
 
import javax.servlet.http.HttpSessionAttributeListener;
import javax.servlet.http.HttpSessionBindingEvent;
 
import com.sysware.framework.commons.SystemConstants;
 
/**
 *
 *
 * 在线session监听器
 *
 * @version : 1.0
 * @since : 2012-5-21下午08:46:02
 * @team : 系统管理
 * @author : liuxj
 */
public class OnlineSessionListener implements HttpSessionAttributeListener {
     
 
    @Override
    public void attributeAdded(HttpSessionBindingEvent event) {
        if (SystemConstants.SESSION_KEY.equals(event.getName())) {
            OnlineUserContext.getInstance().addSession(event.getSession());
        }
    }
     
    @Override
    public void attributeRemoved(HttpSessionBindingEvent event) {
        if (SystemConstants.SESSION_KEY.equals(event.getName())) {
            OnlineUserContext.getInstance().removeSession(event.getSession());
        }
    }
 
    @Override
    public void attributeReplaced(HttpSessionBindingEvent event) {
        if (SystemConstants.SESSION_KEY.equals(event.getName())) {
            OnlineUserContext.getInstance().replaceSession(event.getSession());
        }
    }
}

```

```java
package com.sysware.framework.login;

import java.util.ArrayList;
import java.util.HashMap;
import java.util.Iterator;
import java.util.List;
import java.util.Map;
import java.util.Map.Entry;

import javax.servlet.http.HttpSession;

import com.sysware.framework.commons.SystemConstants;
import com.sysware.framework.eobs.commons.ObjectCloneUtils;
import com.sysware.utils.SyswareUtil;

/**
 * copyright : Sysware Technology Co., Ltd
 * 
 * 在线用户信息上下文
 * 
 * @version : 1.0
 * @since : 2012-5-21下午08:31:07
 * @team : 系统管理
 * @author : liuxj
 */
public class OnlineUserContext {
    private static OnlineUserContext onlineUserContext;

    // 用户在线信息
    private Map<String, HttpSession> onlineUserMap;

    private OnlineUserContext() {
           onlineUserMap = new HashMap<String, HttpSession>();
    }

    /**
     * 获取在线用户上下文实例
     * 
     * @since : 2012-5-23:下午05:47:55
     * @return 在线用户上下文实例
     */
    public static OnlineUserContext getInstance() {
        return onlineUserContext == null ? onlineUserContext = new OnlineUserContext()
                : onlineUserContext;
    }

    /**
     * 加入session触发
     * 
     * @since : 2012-5-21:下午09:02:26
     * @param session
     *            session对象
     */
    synchronized void addSession(HttpSession session) {
        
        onlineUserMap.put(session.getId(), session);
    }

    /**
     * 移除Session
     * 
     * @since : 2012-5-21:下午09:02:56
     * @param session
     *            session对象
     */
    synchronized void removeSession(HttpSession session) {
        if (onlineUserMap.containsKey(session.getId())) {
            onlineUserMap.remove(session.getId());
        }
    }

    /**
     * 替换Session
     * 
     * @since : 2012-5-21:下午09:03:18
     * @param session
     *            session对象
     */
    synchronized void replaceSession(HttpSession session) {
        addSession(session);
    }
    /**
     * 根据用户ID判断是否登录
     * 
     * @since : 2013-1-28:下午02:01:31
     * @param userId
     *            用户ID
     * @return true已经登录|false未登录
     */
    public boolean isLogged(String userId) {
        List<OnlineUserInfo> list = getOnlineUserInfoList();
        for (OnlineUserInfo info : list) {
            if (info.isLogged() && info.getUserId().equals(userId)) {
                return true;
            }
        }
        return false;
    }
    /**
     * 根据sessionId获取session
     * @since : 2012-12-17:下午01:06:11
     * @param sessionId    sessionId
     * @return    session
     */
    public HttpSession getSession(String sessionId) {
        return onlineUserMap.containsKey(sessionId) ? onlineUserMap.get(sessionId) : null;
    }
    
    /**
     * 根据sessionId移除session
     * @since : 2012-12-17:下午01:05:39
     * @param sessionId    sessionId
     */
    public void removeSession(String sessionId) {
        HttpSession session = getSession(sessionId);
        if(!SyswareUtil.isEmpty(session)) {
            session.invalidate();
            session = null;
        }
    }

    /**
     * 获取当前登录操作用户数量
     * 
     * @since : 2012-5-21:下午09:20:54
     * @return 登录操作用户数量
     */
    public int getLoginUserCount() {
        int num = 0;
        List<OnlineUserInfo> list = getOnlineUserInfoList();
        for(OnlineUserInfo info : list) {
            if(info.isLogged()) {
                num ++;
            }
        }
        return num;
    }
    
    /**
     * 获取当前未登录的操作用户数量
     * 
     * @since : 2012-5-21:下午09:21:14
     * @return 未登录的操作用户数量
     */
    public int getNoLoginUserCount() {
        int num = 0;
        List<OnlineUserInfo> list = getOnlineUserInfoList();
        for(OnlineUserInfo info : list) {
            if(!info.isLogged()) {
                num ++;
            }
        }
        return num;
    }

    /**
     * 获取当前操作用户总数
     * 
     * @since : 2012-5-21:下午09:22:16
     * @return 操作用户总数
     */
    public int getOperatorCount() {
        return getOnlineUserInfoList().size();
    }

    /**
     * 获取当前操作用户列表
     * 
     * @since : 2012-5-22:上午10:11:39
     * @return 操作用户列表
     */
    public synchronized List<OnlineUserInfo> getOnlineUserInfoList() {
        List<OnlineUserInfo> list = new ArrayList<OnlineUserInfo>();
        Iterator<Entry<String, HttpSession>> iterator = onlineUserMap
                .entrySet().iterator();
        while (iterator.hasNext()) {
            Entry<String, HttpSession> entry = iterator.next();
            OnlineUserInfo info = ObjectCloneUtils.clone(getOnlineUserInfo(entry.getKey()));
            info.setSessionId(entry.getKey());
            list.add(info);
        }
        return list;
    }

    /**
     * 获取在线用户信息
     * 
     * @since : 2012-5-21:下午09:14:01
     * @param sessionId
     *            session的ID
     * @return 在线用户信息
     */
    private OnlineUserInfo getOnlineUserInfo(String sessionId) {
        HttpSession session = getSession(sessionId);
        return SyswareUtil.isEmpty(session) ? null : (Operator) session.getAttribute(SystemConstants.SESSION_KEY);
    }
}

```
上面的方法，一个是在线统计人数，一个是实现总访问量，自己项目中运用的，做个笔记。
### 页面

```html
<div class="visit">当前访问数：<span id="nowVisitCount" style="color:#5374B1;"><%=OnlineUserContext.getInstance().getLoginUserCount()  %></span></div>
                    <div class="count">累计访问数：<span id="historyVisitCount" style="color:#5374B1;"><%=new LoginServiceImpl().getCounts()%></span></div>

```
