---
title: Eclipse部署Web项目到tomcat根路径下
tags:
  - Eclipse
copyright: true
category: Eclipse
abbrlink: 50748
date: 2017-12-06 21:34:36
---
![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0060.jpg)

Step1：**项目右键->属性->web project settings**
<!--more-->
修改程序的Properties下的Web Project Settings将Context root 例如 “/WebSample”修改为“/”。

Step2: 修改**services->modules->edit->path**

修改path为“/”。

重启tomcat即可。

