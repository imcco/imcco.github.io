---
title: Eclipse报"Building workspace". GC overhead limit exceeded
category:
  - IDE
copyright: true
tags: eclipse
abbrlink: 64632
date: 2017-09-21 15:22:25
---
![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0059.jpg)

eclipse一直报An internal error occurred during: "Building workspace". GC overhead limit exceeded

<!--more-->
最近导入到eclipse里的工程挺大的，每次eclipse启动之后都回update workspace，然后就一直报：

An internal error occurred during: "Building workspace". GC overhead limit exceeded

这个错误。

 

**解决方法：** 

原因是Eclipse默认配置内存太小需要更改Eclipse安装文件夹下的eclipse.ini文件。

Eclipse.ini默认文件如下：

修改如下：

-Xms512m -Xmx1024m

第一个是最小的初始化内存，第二个是最大的占有内存

还可以加上 -XX:MaxPermSize=1024m这个意思是在编译文件时一直占有最大内存，重启Eclipse。
