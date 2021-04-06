---
title: HEXO主题图配置
tags: hexo
category: hexo
abbrlink: 13957
date: 2017-09-01 07:28:28
---

![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0042.jpg)

为一篇博客添加相关的图片可以快速吸引读者的目光，也能帮助读者理解文章概要，尤其是在写技术博客或是某些很复杂的东西时。

然而，Hexo 主题一般只提供一种默认的图片样式，包括 Jacman。所以，我为 Jacman 实现了几种常见的图片样式。这里我会展示这几种图片样式以及其实现方法。
<!--more-->
[![img](http://ww4.sinaimg.cn/large/81b78497jw1en8b95t5kmj203j00u743.jpg)](http://ww4.sinaimg.cn/large/81b78497jw1en8b95t5kmj203j00u743.jpg)

## 主题图片

我比较喜欢像[36氪](http://www.36kr.com/)那样，一篇文章带一张相关图片。图片能传达的信息永远比文字丰富。当浏览文章列表时，除了标题最吸引人的自然就属文章主题图片了。

所以为了方便使用，我创建了一个图片样式叫`img-topic`，大概是长这样子的。

```
/themes/jacman/source/css/_partial/index.styl
.img-topic
  max-width: 300px;
  max-height: 1800px;
  display: block ;
  margin-left: .7em;
  margin-right: .7em;
  padding: 0;
  float: right;
  clear: right;
```

在博客中，我更希望主题图片居右，并且点进文章正文后该图片不会显示。我们可以使用HTML语法插入图片。

```
<img src="图片路径" class="img-topic" />
```

这儿是一张使用`img-topic`样式后的效果图。

[![img-topic效果图](http://ww2.sinaimg.cn/large/81b78497jw1en8cj4beb9j20s80b8adb.jpg)](http://ww2.sinaimg.cn/large/81b78497jw1en8cj4beb9j20s80b8adb.jpg)img-topic效果图

## Logo 图片

Logo 图片可以快速定义一篇文章的主题。与主题图片非常类似，只不过 Logo 图片放置的是 Logo ，而且一般比主题图片要小。类似的，Logo 图片也是居右，并且在文章正文中不会显示。

关于`img-logo`的样式如下

```
/themes/jacman/source/css/_partial/index.styl
.img-logo 
  max-width: 180px;
  max-height: 96px;
  display: block;
  margin-right: .7em;
  margin-left: .7em;
  padding: 0;
  float: right;
  clear: right;
```

使用HTML语法插入图片

```
<img src="图片路径" class="img-logo" />
```

效果如下图：

[![img-logo效果图](http://ww4.sinaimg.cn/large/81b78497jw1en8cj55warj20se0avgoh.jpg)](http://ww4.sinaimg.cn/large/81b78497jw1en8cj55warj20se0avgoh.jpg)img-logo效果图

## 居中图片

有网友抱怨 Jacman 的图片只能居左，他喜欢让图片居中却不知道该怎么做。为此，我也创建了一个图片样式叫`img-center`，所需的代码很短。

```
/themes/jacman/source/css/_partial/article.styl
.img-center
  display: block ;
  margin: auto;
```

## 阴影图片

有时候添加的图片可能会与文章背景混淆，使得读者看不清到底哪部分是图片哪部分是文章。使用`img-shadow`为图片添加边角阴影可以更加凸显图片的位置，也能更美观。

```
/themes/jacman/source/css/_partial/article.styl
.img-shadow
  box-shadow: 0 0 2px 3px #ddd;
```

使用HTML语法插入图片

```
<img src="http://ww1.sinaimg.cn/large/81b78497jw1enhkcat9mqj20go06g0sy.jpg" class="img-shadow" />
```

[![img](http://ww1.sinaimg.cn/large/81b78497jw1enhkcat9mqj20go06g0sy.jpg)](http://ww1.sinaimg.cn/large/81b78497jw1enhkcat9mqj20go06g0sy.jpg)

## 画廊图片

Hexo 中提供了一种文章类别叫`photo`，Jacman 也为这种文章设计了图片浏览方式。具体效果可以看这篇 [Demo](http://wuchong.me/jacman/gallery/)。`photo`类文章的写法可以看 [Demo Sample](https://raw.githubusercontent.com/wuchong/jacman/site/source/_posts/gallery.md)。

[![photo文章效果图](http://ww3.sinaimg.cn/large/81b78497jw1en8cj4r4bij20sl0krn4x.jpg)](http://ww3.sinaimg.cn/large/81b78497jw1en8cj4r4bij20sl0krn4x.jpg)photo文章效果图

## 图片备注

Jacman 中可以方便地为图片提供备注。

```
![添加你的备注](http://ww1.sinaimg.cn/mw690/81b78497jw1emfgts2pt4j21hc0u0k1c.jpg)
```

效果就如下图，在图片左下方会显示关于图片的描述。

[![添加你的备注](http://ww1.sinaimg.cn/mw690/81b78497jw1emfgts2pt4j21hc0u0k1c.jpg)](http://ww1.sinaimg.cn/mw690/81b78497jw1emfgts2pt4j21hc0u0k1c.jpg)添加你的备注

OK，就这么多了，希望大家喜欢。如果嫌折腾麻烦，欢迎使用最新版的 [Jacman](https://github.com/wuchong/jacman)，可以直接使用以上定义的样式！

*PS:有任何关于 Hexo 的问题，欢迎来 Hexo 中文社区 咨询。*
