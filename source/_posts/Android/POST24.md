---
title: hybrid app技术开发APP应用
tags: ionic
category: ionic
date: 2018-01-30 15:44:36
---
![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0107.jpg)

hybrid app技术开发APP应用。而现在主流的**Hybrid app**框架是**phonegap**。在使用phonegap做混合式开发之前需要下载很多软件及插件。如：**JavaJDK、node.js、cordova、ionic、Android studio、AndroidSDK**等软件及相应的插件。（请不要下载phonegap应用程序，不要混淆）
下面就来简单介绍一下，如何安装cordova和ionic及常用的命令总结。
<!--more-->
## 第一部分  安装时总结

#### win系统下nodejs安装及环境配置

##### 第一步：下载nodejs

官网：http://nodejs.org/download/

##### 第二步：安装nodejs

1. 下载完成之后，双击"node-v0.10.28-x86.msi"，开始安装nodejs，自定义安装在D:\dev\nodejs下面。

2. 在cmd控制台输入：node -v，控制台将打印出：v0.10.28，出现版本提示表示安装成功。
3. 该引导步骤会将node.exe文件安装到D:\dev\nodejs\目录下，并将该目录添加进PATH环境变量。

##### 第三步：npm安装
由于新版的nodejs已经集成了npm，所以之前npm也一并安装好了。同样可以使用cmd命令行输入"npm -v"来测试是否成功安装。出现版本提示表示安装成功。


##### 第四步：安装相关环境
	npm install -g express-generator
	npm install jade -g
	npm install mysql -g

默认情况下上述组件都是安装在D:\dev\nodejs\node_modules文件夹下，这也是nodejs相关组件的自动查找路径。

##### 第五步：安装CoffeeScript

```
npm install coffee-script -g
```


确认安装的命令：coffee -v，出现版本号表示成功安装。

补充说明：
所有命令都是-g进行全局安装的，这样安装的安装包都在当前用户下，在磁盘的所有其他地方都可以访问到，比较方面。否则安装在当前目录下，只能在当前目录下使用。

安装express问题
安装nodejs安装包后，通过npm安装express后，运行express提示" express"不是内部或外部命令，原因是版本问题，当前版本是4.0.0，改成3.5.0即可运行。

```
npm install -g express@3.5.0 
```

注意：express 测试版本用大写V

npm命令集合：
1. npm install moduleNames：安装Node模块

- npm install express 
  默认会安装express的最新版本，也可以通过在后面加版本号的方式安装指定版本，如npm install express@3.0.6

- npm install <name> -g 
  将包安装到全局环境中

  但是代码中，直接通过require()的方式是没有办法调用全局安装的包的。全局的安装是供命令行使用的，就好像全局安装了vmarket后，就可以在命令行中直接运行vm命令

- npm install <name> --save 
  安装的同时，将信息写入package.json中项目路径中如果有package.json文件时，直接使用npm install方法就可以根据dependencies配置安装所有的依赖包，这样代码提交到github时，就不用提交node_modules这个文件夹了。


2. 全局安装命令为npm install -g moduleName。
3. npm view moduleNames：查看node模块的package.json文件夹
4. npm list：查看当前目录下已安装的node包
5. npm help：查看帮助命令
6. npm view moudleName dependencies：查看包的依赖关系
7. npm view moduleName repository.url：查看包的源文件地址
8. npm view moduleName engines：查看包所依赖的Node的版本
9. npm help folders：查看npm使用的所有文件夹
10. npm rebuild moduleName：用于更改包内容后进行重建
11. npm outdated：检查包是否已经过时，此命令会列出所有已经过时的包，可以及时进行包的更新
12. npm update moduleName：更新node模块
13. npm uninstall moudleName：卸载node模块
14. 一个npm包是包含了package.json的文件夹，package.json描述了这个文件夹的结构。访问npm的json文件夹的方法如下：

- $ npm help json 此命令会以默认的方式打开一个网页，如果更改了默认打开程序则可能不会以网页的形式打开。

15. 发布一个npm包的时候，需要检验某个包名是否已存在 $ npm search packageName
16. npm init：会引导你创建一个package.json文件，包括名称.  版本.  作者这些信息等
17. npm root：查看当前包的安装路径
18. npm root -g：查看全局的包的安装路径
19. npm -v：查看npm安装的版本
20. npm install --save moduleName 安装模块到本目录

更多命令请参看npm官方文档：https://www.npmjs.org/doc/

21. npm install -g cnpm --registry=https://registry.npm.taobao.org（npm镜像源指向淘宝） 
22. cnpm install -g cordova ionic（安装cordova ionic） 
23. cnpm update -g cordova ionic（更新cordova ionic） 
24. ionic -help（查看帮助） 
25. ionic -v（查看版本） 
26. ionic start myApp blank（空项目） 
27. ionic start myApp tabs（带导航条） 
28. ionic start myApp sidemenu（带侧滑菜单） 
29. ionic platform add android（添加android平台） 
30. ionic platform remove android（移除android平台） 
31. ionic build android（编译项目apk）
32. ionic emulate android（运行项目apk 手机连接在手机运行 模拟器连接在模拟器运行） 
33. ionic run android （相当于build + emulate） 
34. ionic serve （开启服务调试） 
35. ionic build ios（编译项目ipk）
36. ionic emulate ios（运行项目ipk） 

##### 第六步：创建项目
	express /tmp/foo && cd /tmp/foo
	npm install //下载依赖包
	npm start //启动项目

目录介绍:

	node_moduls  存放所有的项目依赖库
	package.json  项目依赖配置及开发者信息
	app.js 程序启动文件
	public 静态文件（css js img）
	routes 路由文件（MVC中的C，controller）
	Viesws 页面文件（Ejs模板）

##### 第七步 创建cordova项目

（注意：当第一次创建或编译项目的时候，可能系统会自动下载一些东西，需要一些时间。）

1. 在某个目录下创建cordova项目，打开命令行

输入：

```shell
cordova  create  test  com.cordova.test   test 
```

 （创建cordova工程  <文件夹名> <包名> <app名>）

2. 文件夹目录

- hooks：存放自定义cordova命令的脚本文件。每个project命令都可以定义before和after的Hook，比如：before_build、after_build。没用过，不展开了。
- platforms：平台目录，各自的平台代码就放在这里，可以放一下平台专属的代码，现在这个目录应该是空的，后面会介绍如何创建平台。
- plugins：插件目录，安装的插件会放在这里。后面会有专门的文章介绍开发插件。
- www：最重要的目录，存放项目主题的HTML5和JS代码的目录。app一开始打开的就是这个目录中index.html文件。
- config.xml：主要是cordova的一些配置，比如：项目使用了哪些插件、应用图标icon和启动页面SplashScreen，修改app的版本，名字等信息，还有平台的配置。

3. 添加平台支持

用命令行打开对应的文件夹，

添加平台 输入：

```shell
cordova platforms add android
```

移除平台 输入：

```
cordova platforms rm android 
```

 （移除android平台支持）

也可以通过@版本号，来添加不同版本的android平台，如：

```shell
cordova platforms rm android @4.1.1
```

现在就可以在www文件夹内写自己的js和html代码了。

添加插件 输入:

```
cordova plugin add cordova-plugin-file
```

 (cordova plugin add <插件官方名称>)

插件搜索地址：<http://cordova.apache.org/plugins/>

也可以通过github查找一些第三方插件 输入：

```shell
cordova plugin add https://github.com/phonegap/phonegap-plugin-barcodescanner.git //这个是扫描二维码插件  (github项目地址)
```

github地址：<https://github.com/>

删除插件 输入：

```
cordova plugin rm cordova-plugin-file
```

（使用rm和remove都可以）

插件列表 输入：

```
cordova plugin list
```

（查看当前安装了哪些插件）

编译调试程序

不是每一句代码都需要运行，根据自己的需求进行选择。

- cordova install android //将编译好的应用程序安装到模拟器上。
- cordova emulate android //在模拟器上运行（前提是创建好AVD）
- cordova serve android //在浏览器运行
- cordova build android //打包cordova项目到android平台。
- cordova run android //通过USB直接安装到真机（该语句已经包括了build命令）

例如：我现在是手边直接连着测试手机，所以我就会直接用cordova run android

中间省略...

出现LAUNCH SUCCESS则编译成功，已经安装到手机上了。

##### 第八步 安装Android SDK

1. 下载Android SDK，点击安装，直接默认路径即可！

下载地址：http://developer.android.com/sdk/index.html

2. 默认路径安装后，安装完成，开始配置环境变量。
3. 打开计算机属性——高级系统设置——环境变量（如上文）

4. 新建一个环境变量，变量名：ANDROID_HOME，变量值：C:\Program Files (x86)\Android\android-sdk（以你安装目录为准,确认里面有tools和add-ons等多个文件夹），点击确认。
5. 在变量PATH后面加上变量值%ANDROID_HOME%\tools;点击确认即可。 如果没有这个变量，新建一个即可！新建方法见上文！
6. Android SDK配置完成，接下来验证配置是否成功。

7.  点击运行——输入cmd——回车——输入android -h——回车
8.  ionic  Android 环境搭建过程遇到的问题 http://bbs.ionic-china.com/read.php?tid=7&fid=4 


##### 第九步 安装Ionic
1.  执行npm install -g ionic
2.  创建一个Ionic APP 执行ionic start myapp[template]
  Template 有如下三种 默认是tabs project:
  tabs（默认）.  sidemenu.  blank
3.  安装模板  ionic install --save 模板
4.  cordova 是页面与设备桥接
5.  npm install --save bower

##### 第十步 安装gulp相关代码合并.  混淆工具
	npm install gulp  
	npm install gulp-concat:合并文件
	npm install gulp-rename:重命名文件
	npm install gulp-sass:支持sass
	npm install gulp-minify-css:压缩css 
	npm install gulp-connect  配置一个web服务器

##### 第十一步 编译测试
1. android版本

	cd myapp  
	ionic platform add android  
	ionic build android  
	ionic run android  
	如果要在虚拟机中测试，可以改用
	ionic emulate android
2. ios版本

	$ ionic start myapp tabs  
	$ cd myapp  
	$ ionic platform add ios  
	$ ionic build ios  
	$ ionic emulate ios
3. 浏览器同时修改端口号

	cd myapp
	ionic serve -p 8105

4. 连接数据线直接测试

	ionic run android

#### 重点集合

##### ionic安装失败或者cordova安装失败解决方法

镜像使用方法（三种办法任意一种都能解决问题，建议使用##### 第三种，将配置写死，下次用的时候配置还在）:

1. 通过config命令

	npm config set registry https://registry.npm.taobao.org 
	npm info underscore （如果上面配置正确这个命令会有字符串response）

2. 命令行指定

	npm --registry https://registry.npm.taobao.org info underscore 
3. 编辑 ~/.npmrc 加入下面内容

	registry = https://registry.npm.taobao.org
	搜索镜像: https://npm.taobao.org
	建立或使用镜像,参考: https://github.com/cnpm/cnpmjs.org

4. 使用cnpm(强烈建议)

	安装cnpm
	npm install -g cnpm --registry=https://registry.npm.taobao.org
	以后所有的npm可用cnpm代替，如:cnpm install ionic

5. 如果还是一直失败，将ionic文件下载解压后放到C:\Users\Auser\AppData\Roaming\npm\node_modules中

6. 配置环境变量

   node的npm命令Path : C:\Documents and Settings\Administrator\Application Data\npm

   补齐npm文件夹下有关于ionic的文件 http://download.csdn.net/detail/superjunjin/8417723

   补齐ionic项目下node_modules等文件 http://download.csdn.net/detail/superjunjin/8417731
   （具体文件见最后的压缩包）

   ```
   ionic start myApp sidemenu  //创建带有左侧带有menu栏的示例项目
   ionic start myApp blank   //创建空白项目
   ```

## 第二部分 开发过程总结
#### Angular 提供3种方法创建并注册服务

1. Provider  
2. Factory  
3. Service

Providers 是唯一一种你可以传进 .config() 函数的 service。当你想要在 service对象启用之前，先进行模块范围的配置，那就应该用 provider。

#### bower安装restangular失败

（报错：Bower : ENOGIT git is not installed or not in the PATH）

1. 添加git路径到环境变量PATH中（命令：set PATH=%PATH%;D:\Program Files\Git\bin）
2. 运行bower install restangular 即可安装成功
3. http://www.ng-newsletter.com/posts/restangular.html
4. restangular文档：https://github.com/mgonto/restangular#element-methods
5. 安装bower install underscore

#### restangular需要用到的js

(js少引入报错：Uncaught ReferenceError: _ is not defined from restangular)

```JS
<script type="text/javascript" src="http://cdnjs.cloudflare.com/ajax/libs/underscore.js/1.4.4/underscore-min.js"></script>
<script type="text/javascript" src="http://cdn.jsdelivr.net/restangular/latest/restangular.min.js"></script>
```

#### 跨域错误解决办法

（XMLHttpRequest cannot load  ''. No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin ' ' is therefore not allowed access. ）

Java代码中返回结果集前增加

```java
response.setHeader("Access-Control-Allow-Origin", "*");
```

#### 国际化 

 http://yijiebuyi.com/blog/3b55056c87b73ba606c19e9338dca679.html

1. 安装bower install angular-translate
2. 安装bower install angular-translate-loader-static-files
3. angular国际化不存在乱码，如果出现乱码可能是中文json文件编码非UTF-8格式
4. 国际化使用（移动端有问题  无法使用）：

需要引入JS

```js
<script src="lib/angular/angular.js"></script>
<script src="lib/angular-translate/angular-translate.js"></script>
<script src="lib/angular-translate-loader-static-files/angular-translate-loader-static-files.js"></script>
```


建立文件夹i18n存放cn.json /en.json

```
en.json：{"100001":"Login","100002":"Register"}
cn.json：{"100001":"登录","100002":"注册"}
```


在app.js中config里配置如下

```js
//国际化配置
$translateProvider.preferredLanguage('cn');
$translateProvider.useStaticFilesLoader({
  prefix: '/i18n/',
  suffix: '.json'
});
```


html页面使用

```js
{{'100001' | translate }}
5.按钮切换语种
.controller('LanguageSwitchingCtrl', ['$scope', '$translate', function (scope, $translate) {
scope.switching = function (lang) {
  $translate.use(lang);
		};
}]);
```

####  AngularJS iframe跨域打开内容时报错误的解决办法

```html
<iframe id="myFrame" ng-src="{{url}}" width="100%" height="100%" seamless frameborder="0" ></iframe>
```

#### 打开不同域的内容时报下面的错误

`Blocked loading resource from url not allowed by $sceDelegate policy`

解决方案：

```js
app.config(function($sceDelegateProvider) {
   $sceDelegateProvider.resourceUrlWhitelist([
       // Allow same origin resource loads.
       'self',
       // Allow loading from our assets domain.  Notice the difference between * and **.
       'http://media.w3.org/**']);
});
```
