---
title: 约定优于配置（convention over configuration）
tags:
  - Java
  - BackEnd
category: backend
abbrlink: 2779
date: 2017-08-24 17:28:48
---
![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0013.jpg)

文章来自维基百科

约定优于配置（convention over configuration），也称作按约定编程，是一种软件设计范式，旨在减少软件开发人员需做决定的数量，获得简单的好处，而又不失灵活性。
<!--more-->

本质是说，开发人员仅需规定应用中不符约定的部分。例如，如果模型中有个名为Sale的类，那么数据库中对应的表就会默认命名为sales。只有在偏离这一约定时，例如将该表命名为”products_sold”，才需写有关这个名字的配置。

如果您所用工具的约定与你的期待相符，便可省去配置；反之，你可以配置来达到你所期待的方式

动机
设计不好的框架通常需要多个配置文件，每一个都有许多设置。这些配置文件为每一个项目提供信息说明从URL到将类映射到数据库表的各种信息。大量包含太多参数的配置文件通常是过度复杂的应用设计的指标（代码坏味道）。

例如，在知名的Java对象关系映射框架hibernate的早期版本中，将类及其属性映射到数据库上需要是在XML文件中的描述，其中大部分信息都应能够按照约定得到，如将类映射到同名的数据库表，将属性分别映射到表上的字段。后续的版本抛弃了XML配置文件，而是使用这些恰当的约定，对于不符合这些约定的情形，可以使用Java 标注来说明（参见下面提供的JavaBeans规范）。

使用
许多新的框架使用了约定优于配置的方法，包括：spring，Ruby on Rails，Kohana PHP，Grails，Grok，Zend Framework，CakePHP，symfony，Maven，ASP.NET MVC，Web2py（MVC），Apache Wicket。

这是一个古老的概念, 甚至在Java类库中也可以找出这一概念的踪迹。例如，JavaBean规范非常多的依赖这一概念。下面摘录JavaBeans 1.1版规范的一段：

按照一般的规则，我们不希望造出一个奇怪的java.beans.everything类，其他类需要从该类派生。而是希望在运行时JavaBeans为一般的对象提供缺省的行为特征，但是允许对象通过继承特定的java.beans.something接口来覆盖缺省的行为特征的一部分。
Maven约定的项目结构如下图： 

只需配置很少的信息，Maven就可以自动完成编译、测试和打包等工作。
