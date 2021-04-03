---
title: vue-cli本地环境API代理设置和解决跨域
tags: vueJs
category: vueJs
date: 2018-02-26 16:44:36
---
![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0144.jpg)

## 前言

我们在使用vue-cli启动项目的时候`npm run dev`便可以启动我们的项目了，通常我们的请求地址是以localhost:8080来请求接口数据的，localhost是没有办法设置cookie的。
<!--more-->

我们可以在vue-cli配置文件里面设置一个代理，跨域的方法有很多，通常需要后台来进行配置。我们可以直接通过node.js代理服务器来实现跨域请求。

## vue proxyTable接口跨域请求调试

在vue-cli项目中的`config`文件夹下的`index.js`配置文件中，`dev`长这样子：

```
dev: {
    env: require('./dev.env'),
    port: 8080,
    autoOpenBrowser: true,
    assetsSubDirectory: 'static',
    assetsPublicPath: '/',
    proxyTable: {},   
    cssSourceMap: false
  }

```

服务器提供的接口如果长这样`https://www.exaple.com/server_new/login`，我们把域名提取出来如`https://www.exaple.com`；

在config中新建一个文件命名为`proxyConfig.js `:

```
module.exports = {
  proxy: {
        '/apis': {    //将www.exaple.com印射为/apis
            target: 'https://www.exaple.com',  // 接口域名
            changeOrigin: true,  //是否跨域
            pathRewrite: {
                '^/apis': ''   //需要rewrite的,
            }              
        }
  }
}

```

`config`文件夹下的`index.js`引入`proxyConfig.js`：

```
var proxyConfig = require('./proxyConfig')

```

`config`文件夹下的`index.js`中的`dev`改成:

```
dev: {
    env: require('./dev.env'),
    port: 8080,
    autoOpenBrowser: true,
    assetsSubDirectory: 'static',
    assetsPublicPath: '/',
    proxyTable: proxyConfig.proxy,
    cssSourceMap: false
  }
  

```

重启项目`npm run dev`：

你会发现出现了这个

![img](https://segmentfault.com/img/bVUlEp?w=455&h=88)

这个时候我们已经设置好了本地API代理了

## 修改本地`hosts`文件

文件路径一般是`C:\Window\System32\drivers\etc`，打开`hosts`文件，在这一段下面把`localhost`设置进去

```
# localhost name resolution is handled within DNS itself.
# 127.0.0.1       localhost
# ::1             localhost
127.0.0.1                   activate.adobe.com
127.0.0.1                   practivate.adobe.com
127.0.0.1                   lmlicenses.wip4.adobe.com
127.0.0.1                   lm.licenses.adobe.com
127.0.0.1                   na1r.services.adobe.com
127.0.0.1                   hlrcv.stage.adobe.com

localhost                   www.exaple.com            

```

## 搞定

此时我们已经完全解决了跨域问题，以及本地测试后台无法向我们本地环境设置`cookie`的情况了。
