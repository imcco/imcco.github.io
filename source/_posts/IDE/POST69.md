---
title: 代码去除编号小技巧
tags:
  - Java
copyright: true
category: Java
abbrlink: 58012
date: 2017-12-08 20:34:36
---
![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0007.jpg)

从网上或者哪里拷贝下来的代码前面总有编号，如何去掉呢，网上有说用程序的太麻烦，于是，我找到了下面两种方法，share 一下~
<!--more-->
1.使用正则表达式：
在editorplus（notepad++）里按ctrl+h，弹出框里勾选上“正则表达式（regular expression）”，然后
第一个框里写   ^[0-9]*.
第二个框里敲一个空格

2.这个是notepad++特有的，而editorplus没有的
将代码拷进去，按住ctrl+alt的同时，按住鼠标左键不放，将需要的代码部分截取出来，复制、黏贴即可
