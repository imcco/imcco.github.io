---
title: hexo next主题集成gitment评论系统
tags: hexo next
category: hexo
copyright: true
abbrlink: 47815
date: 2017-07-24 17:28:28
---
![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0041.jpg)

### 简介
本文介绍hexo next主题(5.1.2)集成giment评论系统的过程。所谓gitment就是把评论放到github的issues系统里，评论支持md，比较适合程序员.

<!--more-->
### 一.注册OAuth Application
点击https://github.com/settings/applications/new注册，注意Authorization callback URL填自己的网站urlhttp://yangq.me/.记下Client ID和Client Secret.
### 二.修改themes/next/_config.yml
#### 在config.yml中添加:

```xml
# Gitment
# Introduction: https://imsun.net/posts/gitment-introduction/
gitment:
  enable: true
  githubID: yourid
  repo: yourrepo
  ClientID: yourid
  ClientSecret: yoursecret
  lazy: true
```

注意:格式要正确，该空格的一定要空格。所有的yourXXX都换成自己的.
#### 在主题的en.yml增加:

```xml
gitmentbutton: Show comments from Gitment
```
#### zh-Hans.yml增加:

```xml
gitmentbutton: 显示 Gitment 评论
```

### 三.修改主题layout/_partials/comments.swig
找到这个文件里的这两行:

```xml
{% elseif theme.valine.appidand theme.valine.appkey %}      
"vcomments">
```

上面是最后一个elseif分支，在下面加一个elseif分支:

```xml
 {% elseif theme.gitment.enable %}
       {% if theme.gitment.lazy %}
         <div onclick="ShowGitment()" id="gitment-display-button">{{  __('gitmentbutton') }}</div>
         <div id="gitment-container" style="display:none"></div>
       {% else %}
         <div id="gitment-container"></div>
       {% endif %}
```

加完之后下面的内容是原来的，保持不变:

```xml
{% endif %}
  </div>
{% endif %}
```

### 四.增加gitment.swig
#### 在主题下layout/_third-party/comments/目录下中添加文件gitment.swig：

```xml
{% if theme.gitment.enable %}
   {% set owner = theme.gitment.githubID %}
   {% set repo = theme.gitment.repo %}
   {% set cid = theme.gitment.ClientID %}
   {% set cs = theme.gitment.ClientSecret %}
   <link rel="stylesheet" href="https://imsun.github.io/gitment/style/default.css">
   <script src="https://imsun.github.io/gitment/dist/gitment.browser.js"></script>
   {% if not theme.gitment.lazy %}
       <script type="text/javascript">
           var gitment = new Gitment({
               id: window.location.pathname, 
               owner: '{{owner}}',
               repo: '{{repo}}',
               oauth: {
                   client_id: '{{cid}}',
                   client_secret: '{{cs}}',
               }});
           gitment.render('gitment-container');
       </script>
   {% else %}
       <script type="text/javascript">
           function ShowGitment(){
               document.getElementById("gitment-display-button").style.display = "none";
               document.getElementById("gitment-container").style.display = "block";
               var gitment = new Gitment({
                   id: document.location.href, 
                   owner: '{{owner}}',
                   repo: '{{repo}}',
                   oauth: {
                       client_id: '{{cid}}',
                       client_secret: '{{cs}}',
                   }});
               gitment.render('gitment-container');
           }
       </script>
   {% endif %}
{% endif %}
```

#### 在主题下layout/_third-party/comments/index.swig文件中引入gitment.swig文件：

```xml
{% include'gitment.swig' %}
```

### 五.添加gitment.styl
在主题下source/css/_common/components/third-party/目录下添加gitment.styl文件，设置button的样式：

```css
#gitment-display-button{
     display: inline-block;
     padding: 0 15px;
     color: #0a9caf;
     cursor: pointer;
     font-size: 14px;
     border: 1px solid #0a9caf;
     border-radius: 4px;
 }
 #gitment-display-button:hover{
     color: #fff;
     background: #0a9caf;
 }
```

然后在主题下source/css/_common/components/third-party/third-party.styl文件中引入相应的CSS样式即可:

```css
@import"gitment";
```

这样就ok了！
##### 易错点
修改themes/next/_config.yml这个文件时，格式要正确。另外，repo是你要想创建issues的仓库，完全可以跟博文所放的仓库不一个。id就写自己的github用户名就可以，这个用户名跟repo必须匹配。
gitment可能不支持链接地址里有中文，所以安装gitment前一定要参考前文把链接持久化搞成全是英文的。
同一篇文章需要初始化comment两次的问题，是因为http://xxx.com/post/ab9bb85a.html和点击阅读全文进去的链接http://xxx.com/post/ab9bb85a.html#more对issues来说是不同的，所以创建两次。解决方法就是gitment.swig里id弄成window.location.pathname而不是document.location.href。
初始化评论后，可以到github里自己放issues的仓库查看issues是否创建成功，有时候浏览器可能会有缓存依然提示你初始化评论。一般过个两分钟就显示正常了。
参考文档
1. [主要参考文档](http://www.jianshu.com/p/10134c474991)
2. [一种相对简略的配置方式](https://meesong.github.io/StaticBlog/2017/NexT+Gitment/)
3. [作者issue](https://github.com/imsun/gitment/issues)
