---
title: 使用 Travis CI 自动更新 GitHub Pages
category:
  - Git
copyright: true
tags: Git
abbrlink: 32102
date: 2017-11-24 23:42:21
---
![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0045.jpg)

​	这年头要是没有个博客都不好意思给别人说你是程序员，我用XX笔记呀，不行吗？不行，这玩意儿要么不能公开分享，要么公开分享要会员，现在到处都是开源，自己学到了东西都不能分享给需要帮助的人，真是伤心呀。那么今天就来聊聊当你用Hexo搭建了博客，怎么自动更新呢，大家都知道Hexo是需要手动生成HTML静态网页的，虽然命令很少，但是每次写完博客先得推送到git然后在生成静态文件，再推送到服务器，想想我这个心也是醉了，不过看到知乎上还有人带着U盘，我只能呵呵了~，你们耐心真好~
<!--more-->
那我们今天就来说说怎么使用Travis CI来自动构建你的博客

什么是Travis CI

Travis CI 是目前新兴的开源持续集成构建项目，它与jenkins，GO的很明显的特别在于采用yaml格式，同时他是在在线的服务，不像jenkins需要你本地打架服务器，简洁清新独树一帜。目前大多数的github项目都已经移入到Travis CI的构建队列中，据说Travis CI每天运行超过4000次完整构建。对于做开源项目或者github的使用者，如果你的项目还没有加入Travis CI构建队列，那么我真的想对你说out了。

我的博客架构

也算是一个框架吧

首先我的博客是使用Hexo来搭建的，托管到Github提供的Gitpage服务上的

每次写完博客git push到github，然后Travis自动构建，构建完成后自动推送到Gitpage服务上

生成后的HTML文件和博客的源文件我是放到一个仓库的，只是使用了不同的分支

master：博客的静态文件，也就是hexo生成后的HTML文件，因为要使用Gitpage服务，所以他规定的网页文件必须是在master分支

![img](C:/Users/lauke/AppData/Local/YNote/data/qq7308ED0E9FBD938CE38D1E1E470A3C7A/95cf6a6cd3e64688addbbcd67432e01d/de330a7a1f334caf833957a02b665fa5.png)

blog-source：是博客的源代码

![img](C:/Users/lauke/AppData/Local/YNote/data/qq7308ED0E9FBD938CE38D1E1E470A3C7A/f314b1bbb60b4488b98a35846f5b61b8/499b3e732392421494a3b2662ffd5da7.png)

当然这样做有隐私问题，因为任何人都能哪的你的博客源码，当然既然是博客，所以就没有这些问题了

启用要构建的项目

首先如果你要使用Travis CI，你必须要GIthub账号（好像Travis CI只支持构建github的项目）和一个项目

使用Github账号登录[Travis CI官网](https://travis-ci.org/)，如下图

![img](C:/Users/lauke/AppData/Local/YNote/data/qq7308ED0E9FBD938CE38D1E1E470A3C7A/5a5c00aa359d41ac9787f65d26584b2e/deeafba80c2648a7ac2ff00086ebeeda.png)

登录完后会进入如下界面

![img](C:/Users/lauke/AppData/Local/YNote/data/qq7308ED0E9FBD938CE38D1E1E470A3C7A/56684db47257478f9b02db6d793d4bb3/c2e7dca9505447aea9d09016c0758c97.png)

当然如果你以前没用使用过，所以你登录完是没有上图红框内的内容的，这里是因为我在写这篇博客前已经使用了，所以会有这些内容

接下来我们点击My Repositories旁边的+，意思是添加一个要自动构建的仓库，如下图：

![img](C:/Users/lauke/AppData/Local/YNote/data/qq7308ED0E9FBD938CE38D1E1E470A3C7A/82918125a3c34aabb3fdfeb7081cf079/fc8082672f8246d08711f9930c0f626f.png)

点击后就会来到如下界面：

![img](C:/Users/lauke/AppData/Local/YNote/data/qq7308ED0E9FBD938CE38D1E1E470A3C7A/b1b58ece11b1459b9680d20f1fd06bb4/0d025d4a8b2c4a22b19c496233605237.png)

可以看到这个界面会显示当前github账号的所以项目，如果没有显示，点击右上角的“Sync account”按钮，就可以同步过来了（ps：上次用windows电脑始终同步不过来项目，最后换成mac可以同步了，最后又换回windows也可以了，汗(⊙﹏⊙)b，不太懂，什么个情况）

居然仓库都同步过来了，那么下一步肯定是要开启你需要构建的仓库，可以看到我开启了lifengsofts.github.io这个项目，当然这个也是我就是我的博客啦

![img](C:/Users/lauke/AppData/Local/YNote/data/qq7308ED0E9FBD938CE38D1E1E470A3C7A/a84b6ecffdd14fec8e32b1335fbeacd2/2097e600450049fb993079ee04cbeccd.png)

开启后我们还需要进行一些配置，操作如下

![img](C:/Users/lauke/AppData/Local/YNote/data/qq7308ED0E9FBD938CE38D1E1E470A3C7A/0b5c02289c8340b099c72a3a83f1b307/b8c36660736243ba93f2e5395a681052.png)

点击红框的那个菜单按钮，就会出现这样的下拉菜单，我们选择设置，来到这个界面，我们按照如下勾选

![img](C:/Users/lauke/AppData/Local/YNote/data/qq7308ED0E9FBD938CE38D1E1E470A3C7A/c5fd953399fc449b980c4cbcfc60b844/7736877a6f014bb48610e558d83a46c4.png)

Build only if .travis.yml is present：是只有在.travis.yml文件中配置的分支改变了才构建

Build pushes：当推送完这个分支后开始构建

到这一步， 我们已经开启了要构建的仓库，但是还有个问题就是，构建完后，我们怎么将生成的文件推送到github上呢，如果不能推送那我们就不需要倒腾一番来使用Travis CI服务了，我们要的结果就是，我们只要想github一push，他就自动构建并push静态文件到gitpages呢，那么下面要解决的就是Travis CI怎么访问github了

在Travis CI配置Github的Access Token

标题已经说得很明白了吧，我们需要在Travis上配置Access Token，这样我们就可以在他构建完后自动push到gitpgaes了，到这里肯定有人要问了，咋你把用户名密码直接写文件里呢，如果你真有这样的问题，那我只能说呵呵~，但我要告诉你的是写里面肯定是可以push成功的

在github上生成Access Token

首先我们来到github的设置界面，点击到Personal access tokens页面，点击右上角的Generate new token按钮会重新生成一个，点击后他会叫你输入密码，然后来到如下界面，给他去一个名字，下面是勾选一些权限

![img](C:/Users/lauke/AppData/Local/YNote/data/qq7308ED0E9FBD938CE38D1E1E470A3C7A/e46ec41d3f2341518002d9336f63bbe2/d0c51193e6a2458b9c78cc92a6846012.png)

生成完后，你需要拷贝下来，只有这时候他才显示，下载进来为了安全他就不会显示了，如果忘了只能重新生成一个了，拷贝完以后我们需要到Travis CI网站配置下

在Travis CI配置

配置界面还是在项目的setting里面，如下图

![img](C:/Users/lauke/AppData/Local/YNote/data/qq7308ED0E9FBD938CE38D1E1E470A3C7A/086da591f0bc4c89b2e8b9fb359c5e09/99551df626964f8cacbae772ccf16d30.png)

至于为什么我们要在这里配置，我想大家肯定应该明白了，写在程序里不安全，配置到这里相当于一个环境变量，我们在构建的时候就可以引用他。

到这里我已经配置了要构建的仓库和要访问的Token，但是问题来了，他知道怎么构建，怎么生成静态文件吗，怎么push的gitpages，又push到那个仓库吗，所以这里我们还需要在源代码的仓库里创建一个.travis.yml配置文件，放到源代码的根目录，如下图

![img](C:/Users/lauke/AppData/Local/YNote/data/qq7308ED0E9FBD938CE38D1E1E470A3C7A/6eda5582175548e8ac32128d58f2d9ab/e9648c228d0148feb3a4c4c35c2f4d25.png)

其中内容如下：

```xml
language: node_js
node_js: stable
# S: Build Lifecycle
install:  - npm install
#before_script: # - npm install -g gulp
script:  - hexo gafter_script:  
- cd ./public  
- git init  
- git config user.name "lifengsofts"  
- git config user.email "lifengsofts@gmail.com"  - git add .  
- git commit -m "Update docs"  
- git push --force --quiet "https://${GH_TOKEN}@${GH_REF}"master:master
# E: Build LifeCycle
branches:  
only:    - blog-source
env: global:   -GH_REF: github.com/lifengsofts/lifengsofts.github.io.git
```

其中给你需要更换的又git config后面的配置信息

GH_REF的值更改为你的仓库地址

到这一步我们配置已经完成了，现在就是见证奇迹的时候了

Push文章到Github

到这一步，我们可以写一篇文章，添加到你的博客的_posts目录下，如图：

![img](C:/Users/lauke/AppData/Local/YNote/data/qq7308ED0E9FBD938CE38D1E1E470A3C7A/00157185adf240f6bde46232067edad7/d449638de9fa4e948e089e28068e6d41.png)

然后commit并push到你的Github上

git push origin blog-source:blog-source

如果不出意外，我们可以就可以在Travis CI网站看到他已经在构建了，如下图：

![img](C:/Users/lauke/AppData/Local/YNote/data/qq7308ED0E9FBD938CE38D1E1E470A3C7A/003fd961eb6740198a473f3aa4062169/48b139f5079e4dd88e4969a7366199ab.png)

构建完成后

![img](C:/Users/lauke/AppData/Local/YNote/data/qq7308ED0E9FBD938CE38D1E1E470A3C7A/03144d5878044ee68ae44f8742003de3/1f4ecd17544746dc9c59656002446c5a.png)

是不是逼格十足呢

如果我的文章对来带来的帮助，可加我微信，微博，QQ什么啥的交个朋友也是不错的，另外微信，微博都会不定期发一些优质的文章，感谢大家的支持~~，联系方式在我的个人介绍里啦，感谢你的阅读，谢谢~
