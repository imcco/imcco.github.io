---
title: RestService和WebService的区别
category:
  - BackEnd
tags: WebService
abbrlink: 27499
date: 2017-09-21 10:08:01
---
![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0027.jpg)

# 什么是WebService？

且看百度百科是如何定义：

Web service是一个平台独立的，松耦合的，自包含的、基于可编程的web的应用程序，可使用开放的XML标准来描述、发布、发现、协调和配置这些应用程序，用于开发分布式的互操作的应用程序。
  <!--more-->
从定义中可以了解， WebService 主要具备三大特点：平台独立性，松耦合、自包含，分布式互操作。

首先，能称为Web Service的应用，具备平台独立性，所谓平台独立性，在Windows、Linux、Unix平台等等都可以使用，是大家遵守的行业标准或者是某些事实标准，有些虽然不是行业标准，但大家都这么做，也都支持，也就慢慢变成事实了。

那什么不是通用的解决方案呢？如：Windows平台的COM/DCOM技术，只能局限于某个平台，一旦脱离该平台就不可以使用。

基次，具备Web Service应用程序，是松耦合的，自包含的。所谓松耦合，即模块之间的依赖型和制约比较小，更改一个模块不会比较大的影响其他模块，说白了，不用关心模块内部是如何实现的，给你标准的接口，你用大家都用的技术与我互通即可。关于自包含的理解，指在组件重用时不需要包含其他的可重用组件，自己搞掂一切，呵呵。

再次，分布式互操作性，这点大家非常好理解，开发出来的应用，要具备与其它系统之间的互操作，现在系统都不是孤立的，开放出来的接口，可以在任意平台上可调用，不具有依赖性。

# Web Service标准

早期，软件巨头们定义的Web Service标准，主要由三部分构成： SOAP,WSDL,UDDI。

SOAP 即简单对象访问协议(Simple Object Access Protocol)，它是用于交换XML编码信息的轻量级协议。它有三个主要方面：XML-envelope为描述信息内容和如何处理内容定义了框架，将程序对象编码成为XML对象的规则，执行远程过程调用(RPC)的约定。SOAP可以运行在任何其他传输协议上。这里需要注意，SOAP是可以在其他协议上，不仅是HTTP, 可以基于SMTP，消息队列等。

WSDL 是web Service描述语言 就是用机器能阅读的方式提供的一个正式描述文档而基于XML的语言，用于描述Web Service及其函数、参数和返回值。因为是基于XML的，所以WSDL既是机器可阅读的，又是人可阅读的。

UDDI 的目的是为电子商务建立标准；UDDI是一套基于Web的、分布式的、为Web Service提供的、信息注册中心的实现标准规范，同时也包含一组使企业能将自身提供的Web Service注册，以使别的企业能够发现的访问协议的实现标准。

UDDI 基本没有实现，这种想法也不现实。可以理解UDDI是个Web Service公共仓库，服务写好注册到UDDI中，以便是其他系统方便调用。

# Rest Service

随着互联网技术的兴起，XML越来越令人诟病，XML的数据包越来重，SOAP协议方便性和灵活性都有欠缺，尤其兴起的Web2.0发展，由Yahoo、Google 和 Facebook等大型互联网公司的倡导，REST代表性状态传输（Representational State Transfer，REST）在 Web 领域已经得到了广泛的接受，是基于 SOAP 和 Web 服务描述语言（Web Services Description Language，WSDL）的 Web 服务的更为简单的替代方法。如GOOGLE 这些提供者弃用或放弃了基于 SOAP 和 WSDL 的接口，而采用了更易于使用、面向资源的模型来公开其服务。

Rest 服务定义：

即REST(Representational State Transfer表述性状态转移)是一种针对网络应用的设计和开发方式，可以降低开发的复杂性，提高系统的可伸缩性。

Rest Service特点：

- 客户端和服务器结构
- 连接协议具有无状态性
- 能够利用Cache机制增进性能
- 层次化的系统
- 随需代码

Rest Service 相比Web Service建议的标准更轻量级，甚到用Javascript都可以调用，使用方更方便、高效、简单。REST架构遵循了CRUD原则，CRUD原则对于资源只需要四种行为：Create（创建）、Read（读取）、Update（更新）和Delete（删除）就可以完成对其操作和处理。

# Web Service与Rest Service 区别

REST 从资源的角度来观察整个网络，分布在各处的资源由URI确定，而客户端的应用通过URI来获取资源的表征。获得这些表征致使这些应用程序转变了其状态。随着不断获取资源的表征，客户端应用不断地在转变着其状态，所谓表征状态转移（Representational State Transfer）。

Rest Service具备Web Service的所有特点：平台独立、松耦合、互操作性。并且，Rest 更轻量级，更简单。可以这么说吧，Rest Service 是Web Service的一种实现，并不是说Rest是Web service替代。
