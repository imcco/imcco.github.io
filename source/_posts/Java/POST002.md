---
title: Java成神之路-HTML和CSS（二）
tags: Java
category: Java
date: 2018-02-23 10:44:36
---
![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0126.jpg)

Java成神之路-HTML和CSS
<!--more-->
# HTML

### HTML整体结构 

HTML基础设施

- 文件应以“<!DOCTYPE ......>”首行顶格开始，推荐使用“<!DOCTYPE html>”。

- 必须申明文档的编码charset，且与文件本身编码保持一致，推荐使用UTF-8编码<meta charset="utf-8"/>。

- 根据页面内容和需求填写适当的keywords和description。

- 页面title是极为重要的不可缺少的一项。

  ```html
  <!DOCTYPE html>
  <html>
  <head>
  <meta charset="utf-8"/>
  <title>NEC：更好的CSS方案</title>
  <meta name="keywords" content=""/>
  <meta name="description" content=""/>
  <meta name="viewport" content="width=device-width"/>
  <link rel="stylesheet" href="css/style.css"/>
  <link rel="shortcut icon" href="img/favicon.ico"/>
  <link rel="apple-touch-icon" href="img/touchicon.png"/>
  </head>
  <body>
    
  </body>
  </html>
  ```


- 结构顺序和视觉顺序基本保持一致结构顺序和视觉顺序基本保持一致

  - 按照从上至下、从左到右的视觉顺序书写HTML结构。
  - 有时候为了便于搜索引擎抓取，我们也会将重要内容在HTML结构顺序上提前。
  - 用div代替table布局，可以使HTML更具灵活性，也方便利用CSS控制。
  - table不建议用于布局，但表现具有明显表格形式的数据，table还是首选。

  结构、表现、行为三者分离，避免内联

  - 使用link将css文件引入，并置于head中。
  - 使用script将js文件引入，并置于body底部。

  保持良好的简洁的树形结构

  - 每一个块级元素都另起一行，每一行都使用Tab缩进对齐（head和body的子元素不需要缩进）。删除冗余的行尾的空格。
  - 使用4个空格代替1个Tab（大多数编辑器中可设置）。
  - 对于内容较为简单的表格，建议将tr写成单行。
  - 你也可以在大的模块之间用空行隔开，使模块更清晰。

  ```html
  <body>
  <!-- 侧栏内容区 -->
  <div class="m-side">
      <div class="side">
          <div class="sidein">
              <!-- 热门标签 -->
              <div class="sideblk">
                  <div class="m-hd3"><h3 class="tit">热门标签</h3> </div>
                  ...
              </div>
    
              <!-- 最热TOP5 -->
              <div class="sideblk">
                  <div class="m-hd3"><h3 class="tit">最热TOP5</h3> <a href="#" class="s-fc02 f-fr">更多»</a></div>
                  ...
              </div>
          </div>
      </div>
  </div>
  <!-- /侧栏内容区 -->
  </body>
  ```

  另外，请做到以下几点

  - 结构上如果可以并列书写，就不要嵌套。

    如果可以写成<div></div><div></div>那么就不要写成<div><div></div></div>

  - 如果结构已经可以满足视觉和语义的要求，那么就不要有额外的冗余的结构。

    比如<div><h2></h2></div>已经能满足要求，那么就不要再写成<div><div><h2></h2></div></div>

  - 一个标签上引用的className不要过多，越少越好。

    比如不要出现这种情况：<div class="class1 class2 class3 class4"></div>

  - 对于一个语义化的内部标签，应尽量避免使用className。

    比如在这样一个列表中，li标签中的itm应去除：<ul class="m-help"><li class="itm"></li><li class="itm"></li></ul>

  ​

### HTML表单元素

#### input元素

最重要的表单元素是 *<input>* 元素。

<input> 元素根据不同的 *type* 属性，可以变化为多种形态。

注释：下一章讲解所有 HTML 输入类型。

#### select元素（下拉列表）

*<select>* 元素定义*下拉列表*：

```html
<select name="cars">
<option value="volvo">Volvo</option>
<option value="saab">Saab</option>
<option value="fiat">Fiat</option>
<option value="audi">Audi</option>
</select>
```

#### option元素定义待选择的选项。

列表通常会把首个选项显示为被选选项。

您能够通过添加 selected 属性来定义预定义选项。

```html
<option value="fiat" selected>Fiat</option>
```

#### textarea元素

*<textarea>* 元素定义多行输入字段（*文本域*）：

```html
<textarea name="message" rows="10" cols="30">
The cat was playing in the garden.
</textarea>
```

以上 HTML 代码在浏览器中显示为：

```
The cat was playing in the garden.
```

#### button元素

*<button>* 元素定义可点击的*按钮*：

```html
<button type="button" onclick="alert('Hello World!')">Click Me!</button>
```

以上 HTML 代码在浏览器中显示为：

HTML5 增加了如下表单元素：

- datalist
- keygen
- output

注释：默认地，浏览器不会显示未知元素。新元素不会破坏您的页面。

#### HTML5 datalist元素

*<datalist>* 元素为 <input> 元素规定预定义选项列表。

用户会在他们输入数据时看到预定义选项的下拉列表。

<input> 元素的 *list* 属性必须引用 <datalist> 元素的 *id* 属性。

实例

通过 <datalist> 设置预定义值的 <input> 元素：

```html
<form action="action_page.php">
<input list="browsers">
<datalist id="browsers">
   <option value="Internet Explorer">
   <option value="Firefox">
   <option value="Chrome">
   <option value="Opera">
   <option value="Safari">
</datalist> 
</form>
```

# CSS

## 标签选择器

顾名思议，标签选择器是直接将HTML标签作为选择器，可以是p、h1、dl、strong等HTML标签。如：

![](http://ovi3ob9p4.bkt.clouddn.com/Java/javaxuexi006.png)

### 二、id选择器：

每一个标签他都有一个共同属性id,所以我们通常给页面元素定义id。如图所示.

![](http://ovi3ob9p4.bkt.clouddn.com/Java/javaxuexi007.png)

其中”p1”,”p2”,”p3”是你自己定义的id名称。注意在前面加”#”号。 
id选择器也同样支持后代选择器(针对嵌套标签) 如图所示:

![](http://ovi3ob9p4.bkt.clouddn.com/Java/javaxuexi008.png)

### 三、类（class）选择器：

每一个标签同样都有一个类属性,所以在CSS里用一个点开头表示类别选择器定义，例如：

![](http://ovi3ob9p4.bkt.clouddn.com/Java/javaxuexi009.png)

在页面中，用class=”类别名”的方法调用,这个方法比较简单灵活，可以随时根据页面需要新建和删除。但需要避免多class综合症。

### 四、群组选择器：

当几个元素样式属性一样时，可以共同调用一个声明，元素之间用逗号分隔。如：

![](http://ovi3ob9p4.bkt.clouddn.com/Java/javaxuexi010.png)

下面来看(标签,id,类)群组选择器如图:

![](http://ovi3ob9p4.bkt.clouddn.com/Java/javaxuexi011.png)

使用组群选择器，将会大大的减化CSS代码，将具有多个相同属性的元素，合并群组进行选择，定义同样的CSS属性，这大大的提高了编码效率与CSS文件体积。

### 五、后代选择器：

后代选择器也称作为派生选择器,通过依据元素在其位置的上下文关系来定义样式，你可以使标记更加简洁。 
在 CSS 中，通过这种方式来应用规则的选择器被称为上下文选择器 (contextual selectors)，这是由于它们依赖于上下文关系来应用或者避免某项规则。在 CSS2 中，它们称为派生选择器，但是无论你如何称呼它们，它们的作用都是相同的。 
派生选择器允许你根据文档的上下文关系来确定某个标签的样式。通过合理地使用派生选择器，我们可以使 HTML 代码变得更加整洁。 
比方说，你希望列表中的 strong 元素变为斜体字，而不是通常的粗体字，可以这样定义一个派生选择器： 
请注意标记为 **的蓝色代码的上下文关系：**

![](http://ovi3ob9p4.bkt.clouddn.com/Java/javaxuexi012.png)
在上面的例子中，只有 li 元素中的 strong 元素的样式为斜体字，无需为 strong 元素定义特别的 class 或 id，代码更加简洁。

结合使用上面的五种CSS选择器，基本满足了CSS布局的需要，主要在于灵活的使用，特别是后代选择器的使用能大大的简化HTML文档，使HTML做到结构化明确，最小的代码实现同样的效果。 
选择器讲完了,我们来看看选择器的优先级.

### 六 比较器的优先级

比较同一级别的个数，数量多的优先级高，如果相同即比较下一级别的个数 ，至于各级别的优先级，大家应该已经很清楚了，就是：

```
important > 内联 > ID > 类 > 标签 | 伪类 | 属性选择 > 伪对象 > 继承 > 通配符 通配符 > 继承1
```

这也就解释了为什么11个标签的定义会比不上1个类的定义，1个类加11个标签会比不上2个类的权重高。

选择器的优先级计算细则就显得很重要了。 
之前有人说选择器的优先级是tagname=1,classname=10,id=100, 因此选择器 #demo > ul > .active的优先级就是 100+10+1 = 111,实际上真是这么计算的嘛，难道10个class就和一个id的优先级是一样的？

下面看看官方对选择器的定义： 
一个选择器的优先级由四个数字a,b,c,d确定。当比较两个选择器时，先比较a，a值大的优先级高，如果a相等则比较b，b值大的优先级高，以此类推。因此，无论b的值多大，也不会对a值的比较造成影响。

```
a由style确定，如果一个属性由元素上的style属性定义则a为1，否则a为0
b是id的数量
c是class和伪类以及属性的数量
d是tagname以及伪元素的数量1234
```

按照这个规则，我们来看看下面这个选择器的优先级：

![](http://ovi3ob9p4.bkt.clouddn.com/Java/javaxuexi013.png)

下面我们来看一下优先级的例子,如图所示:

![](http://ovi3ob9p4.bkt.clouddn.com/Java/javaxuexi014.png)

上例中的元素是绿色的，因为内联元素的优先级最高 
同时也可以得出结论，低优先级选择器的叠加是不会影响高优先级的选择器的.

## CSS引入方式

### 内嵌式

通过`<style> </style>`来书写CSS代码。

只能应用于当前网页，不能被其它网页共享。

注意：<style>标记可以放在网页的任何地方，但一般放在<head>。

### 外联式

通过<link>标记来引入外部的CSS文件(.css)。

可以被其它网页共享。public.css index.css news.css about.css

格式：

```
<link href=“CSS的URL” rel=“stylesheet” type=“text/css” />
```

注意：<link>标记只能放在<head>中

### 行内样式

通过style的属性来书写CSS代码。

每一个HTML元素，都有 style、class、id、name、title 属性。

举例：

```
<p style=“font-size:24px;”></p>
```

CSS的字体/文本属性

Font-size：文字大小

Font-weight：加粗

Font-style：斜体

Color：颜色

Line-height：行高

Text-indent：首行缩进

Text-align：水平对齐

Letter-spacing：字符间距

CSS列表

List-style-type：列表类型，取值：none、circle、 square

List-style-position：符号位置，取值：inside、outside

List-style-image：图片路径，举例：list-style-image:url(images/li01.gif);
