---
title: JQuery中的DOM操作
tags: JQuery
category: JQuery
date: 2018-01-23 22:44:36
---
![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0097.jpg)

　JQuery中的DOM操作主要对包括：建【新建】、增【添加】、删【删除】、改【修改】、查【查找】【像数据库操作】。下面的DOM操作将围绕上面的DOM树进行学习JQueryDOM操作。

<!--more-->

### 一、查--查找DOM节点

查找节点非常容易，使用选择器就能轻松完成各种查找工作。

例：查找元素节点p返回p内的文本内容

```js
$("p").text();
```

例：查找元素节点p的属性返回属性名称对应的属性值

```js
$("p").attr("title")//返回p的属性title的值。
```

#### 1、jQuery选择器：$(‘#text’)

最基本的选择器，简化了js的getElementById\byClassNames，querySelectorAll()等方法。允许使用css选择器来对DOM元素进行选择。

#### 2、子元素

```javascript
$(“.parent”).children();//所有子元素的集合1
```

#### 3、parent()、parents()、closest()

parent()用于查找一级父元素，parents()查找所有父元素的集合，closest()查找最近的父元素 
例如：

```js
<div>
    <ul>
        <li id=“a”></li>
    </ul>
</div>
var test = $(“#a”);
test.parent();//ul
test.parents();//ul,div..一直到html
test.closest(“div”)//div12345678910
```

#### 4、find()

同js的[某元素].getElementById()，在某元素里面找元素

#### 5、nextAll()、prevAll()

Ps:javaScript的对应方法 

```js
firstChild(),lastChild,parent()….
```

### 二、建--新建DOM节点

#### 1、创建元素节点

创建元素节点并且把节点作为<ul>元素的子节点添加到DOM节点树上。先创建元素点，创建元素节点使用Jquery的工厂函数$()来完成，格式如下：$(html),该方法会根据传入的html字符串返回一个DOM对象，并将DOM对象包装成一个JQuery对象后返回。创建一个元素节点JQuery代码如下：

```js
$li1=$("<li></li>")
```

代码返回$li1就是一个由DOM对象包装成的JQuery对象。把新建节点添加到DOM树中JQuery代码如下：

```js
$("ul").append($li1); 
```

添加后页面中只能看到<li>元素默认的"·",由于没有为节点添加文本所以只显示默认符号，下面创建文本节点。
PS:append()方法是添加DOM节点方法详见增--添加DOM节点。 

#### 2、创建文本节点

使用JQuery的工厂函数$()同样能够创建文本节点，创建文本节点的JQuery代码如下：

```javascript
$li2=$("<li>苹果</li>");
```

代码返回$li2就是一个由DOM对象包装成JQuery对象，把新建的文本节点添加到DOM树中JQuery代码如下：

```javascript
$("ul").append($li2);
```

添加后页面中能看到"·苹果"，右键查看页面源码发现新加的文本节点没有title属性。下面方法创建带属性的节点。

#### 3、创建属性节点

创建属性节点同元素节点、文本节点一样使用JQuery的工厂函数完成。创建属性节点的JQuery代码如下：

```javascript
$li3=$("<li title='榴莲'>榴莲</li>");　
```

代码返回$li3也是一个由DOM对象包装成JQuery对象，把新建的属性节点添加到DOM树中JQuery代码如下：

```javascript
$("ul").append($li3);
```

添加后页面中能看到"·榴莲"，右键查看页面源码发现新加的属性节点有title='榴莲'属性。

### 三、增--添加DOM节点

动态新建元素不添加到文档中没有实际意义，将新建的节点插入到文档中有多个方法，如下：`append()、appendTo()、prepend()、prependTo()、after()、insertAfter()、before()、insertBefore()。`

#### 1、append()方法

append()方法向匹配的元素内部追加内容，方法如下：

```js
$("target").append(element);
```

例：

```js
$("ul").append("<li title='香蕉'>香蕉</li>");
```

该方法查找ul元素，然后向ul中添加新建的li元素。

#### 2、appendTo()方法

appendTo()方法将所有匹配的元素追加到指定的元素中，该方法是append()方法的颠倒[操作主题的颠倒并非操作结果]操作。方法如下：

```js
$(element).appendTo(target);
```

例： 

```js
$("<li title='荔枝'>荔枝<li>").appendTo("ul");
```

该方法新建元素li，然后把li添加到查找到的ul元素中。

#### 3、prepend()方法

prepend()方法将每匹配的元素内部前置要添加的元素，方法如下：$(target).prepend(element);例：

```js
$("ul").prepend("<li title='芒果'>芒果</li>")
```

该方法将查找元素ul然后将新建的li元素作为ul子节点，且作为ul的第一个子节点插入到ul中。

#### 4、prependTo()方法

　prependTo()方法将元素添加到每一个匹配的元素内部前置，方法如下：$(element).prependTo();例：

```js
$("<li title='西瓜’>西瓜</li>").prependTo("ul");
```

该方法将新建的元素li插入到查找到的ul元素中作为ul的第一个子节元素。

#### 5、after()方法

after()方法向匹配的元素后面添加元素，新添加的元素做为目标元素后的紧邻的兄弟元素。方法如下：

```js
$(target).after(element);
```

例:

```js
$("p").after("<span>新加段新加段新加段新加段新加段</span>");
```

方法将查找节点p，然后把新建的元素添加到span节点后面做为p的兄弟节点。

#### 6、insertAfter()方法

insertAfter()方法将新建的元素插入到查找到的目标元素后，做为目标元素的兄弟节点。方法如下：

```js
$(element).insertAfter(target);
```

例:

```js
$("<p>insertAfter操作</p>").insertAfter("span");
```

方法将新建的p元素添加到查找到目标元素span后面，做为目标元素后面的第一个兄弟节点。

#### 7、before()方法

before()方法在每一个匹配的元素之前插入，做为匹配元素的前一个兄弟节点。方法如下:

```javascript
$(target).before(element);
```

例：

```javascript
$("p").before("<span>下面是个段落</span>");
```

before方法查找每个元素p，将新建的span元素插入到元素p之前做为p的前一个兄弟节点。

#### 8、insertBefore()方法

insertBefore()方法将新建元素添加到目标元素前，做为目标元素的前一个兄弟节点，方法如下:

```javascript
$(element).insertBefore(target);
```

例:

```javascript
$("<a href='#'>锚</a>").insertBefore("ul");
```

insertBefore()新建a元素，将新建的a元素添加到元素ul前，做为ul的前一个兄弟节点。

增加元素的方法前四个是添加到元素内部，后四个是添加到元素外部的操作，有这些方法可以完成任何形式的元素添加。

### 四、删--删除DOM节点操作

如果想要删除文档中的某个元素JQuery提供了两种删除节点的方法：remove()和empty();

#### 1、remove()方法

remove()方法删除所有匹配的元素，传入的参数用于筛选元素，该方法能删除元素中的所有子节点，当匹配的节点及后代被删除后，该方法返回值是指向被删除节点的引用，因此可以使用该引用，再使用这些被删除的元素。方法如下：

```javascript
$(element).remove();
```

例:

```javascript
span=("span").remove();
$span.insertAfter("ul");
```

该示例中先删除所有的span元素，把删除后的元素使用$span接收，把删除后的元素添加到ul后面做为ul的兄弟节点。该操作相当于将所有的span元素以及后代元素移到ul后面。

#### 2、empty()方法。

empty()方法严格来讲并不是删除元素，该方法只是清空节点，它能清空元素中的所有子节点。方法如下:

```javascript
$(element).empty();
```

例：

```javascript
$("ul li:eq(0)").empty();
```

该示例使用empty方法清空ul中第一个li的文本值。只能下li标签默认符号”·“。

### 五、改--修改DOM节点操作

　修改文档中的元素节点可以使用多种方法:复制节点、替换节点、包裹节点。

#### 1、复制节点$(element).clone()

　复制节点方法能够复制节点元素，并且能够根据参数决定是否复制节点元素的行为。方法如下:

```javascript
$(element).clone(true);
```

例：

```javascript
$("ul li:eq(0)").clone(true);
```

　该方法复制ul的第一个li元素，true参数决定复制元素时也复制元素行为，当不复制行为时没有参数。

#### 2、替换节点$(element).repalcewith()、$(element).repalceAll()

　替换节点方法能够替换某个节点，有两种形式形式实现：replaceWith()和replaceAll().使用replaceWith方法使用后面的元素替换前面的元素，replaceAll方法使用前面的元素替换后面的元素，方法如下：$(oldelement).replaceWith(newelement);$(newelement).repalceAll(oldelement);例：　$("p").replaceWith("<strong>我要留下</strong>");该方法使用strong元素替换p元素。

　　$("<h3>替换strong</h3>").repalceAll("strong");该例使用h3元素替换所有的strong元素。

#### 3、包裹节点$(element).wrap()、$(element).wrapAll()、$(element).wrapInner()

　包裹节点方法使用其他标记包裹目标元素从而改变元素的显示形式等，并且该操作不会破坏原始文档的词义。包裹节点有三种实现形式：wrap();wrapAll();wrapInner();

　wrap()方法如下：

```javascript
$(dstelement).wrap(tag);
```

例：

```javascript
　$("p").wrap("<b></b>");//该示例方法使用b标签包裹所有的p元素每个元素都使用b标签包裹。
```

　wrapAll()方法如下：

```javascript
$(dstelement).wrapAll(tag);
```

例：

```javascript
　$("p").wrapAll("<b></b>");//访示例方法使用b标签包裹所有的p元素，所有的p元素标签用一个b标签包裹。
```

　　wrapInner()方法如下：

```javascript
$(dstelement).wrapInner(tag);
```

例：

```javascript
　$("strong").wrapInner("<b></b>");//该示例使用b标签包裹每个一strong元素的子元素。
```

Dom元素的其他操作:属性操作、样式操作、设置和获取HTML,文本和值、遍历节点操作、Css-Dom操作。

#### 1、属性操作attr()和removeAttr()

attr()方法能够获取元素属性，也能能够设置元素属性。方法如下，当attr(para1)方法有个参数时候用于获得当前元素的para1的属性值，当attr(para1,attrValue)有两个参数时候设置当前元素的属性名为para1的属性值为attrValue;例：

```javascript
 $("p").attr("title");//该示例用于获得p元素的title属性值。
 $("p").attr("title","//你最喜欢的水果");该示例设置p元素的title属性值为"你最喜欢的水果";
```

如果一次设置多个属性值可以使用“名/值”对形式，例：

```javascript
 $("p").attr({"title":"你最喜欢的水果","name":"水果"})//。该示例一次设置两个属性值。
```

removeAttr()方法用于删除特定的属性，方法是在参数中指定属性名。例：

```javascript
$("p").removeAttr("name");//该方法就是移除p元素的name属性。
```

####  2、样式操作addClass()、removeClass()、toggleClass()和hasClass()

添加样式addClass()方法，使用该方法对目标元素添加相应的样式，方法如下：

```javascript
$(element).addClass();
```

例：

```javascript
 $("p").addClass("ul");//该示例设置元素p的样式为ul。
```

移除样式removeClass()方法，使用该方法移除目标元素的指定样式，方法如下：

```javascript
$(element).removeClass();
```

例：

```javascript
 $("p").removeClass("ul");//该救命去除掉p元素的ul类样式。
```

切换样式toggleClass()方法，使用该方法切换目标元素的样式，方法如下：

```javascript
$(element).toggleClass();
```

例：

```javascript
$("p").toggleClass("ul");//该方法来回切换【添加删除实现切换】元素p的样式ul.
```

判断元素是否使用了样式 $(element).hasClass(),方法如下：$(element).hasClass(class);例：

```javascript
alert($("p").hasClass("ul"));//打印出p元素是否有ul样式。
```

PS:addClass()和attr()方法设置样式的不同，attr方法把元素的属性名对应的属性值设为方法中的参数值，addClass()则把属性值

添加到属性名对应的属性值中。例：已有元素<p class='class1'>元素样式</p>,使用attr()和addClass()分别添加新样式。

#### 3、设置和获取HTML【html()】,文本【text()】和值【val()】

html()方法获得或设置某个元素的html元素。方法如下:$(selector).html();例:

```javascript
 $("p").html();该示例获得元素p的html内容。

$("p").html("<strong>添加html内容</strong>");该示例设置p的html内容为”<strong>添加html内容</strong>“;

PS：该方法可以用于XHTML文档，不能用于XML文档。

text()方法获得或设置某个元素的文本值。方法如下:$(selecotr).text();例：

$("p").text();该示例获得元素p的text文本内容。

$("p").text("重新设置的文本内容");该示例设置元素p的text文本为"重新设置的文本内容";

PS:该方法对html和XML文档都适用。

val()方法获得或设置某个元素的值，如果元素值是多选则以数组形式返回，方法如下：$(selector).val();例:文本元素 
<input type="text" id="userName" value="请输入用户名" />
$("#userName").val();获得input元素的值。
$("#userName").val('响马');设置input元素的值为'响马'。
val()方法的不仅能操作input，最重要的一个用途用于select【下拉列表框】、checkbox【多选框】、radio【单选框】。例：在下拉框下的多选赋值应用<select id="fruits" multiple="multiple"><option>苹果</option><option>香蕉</option><option>西瓜</option></select>
$("#fruits").val(['苹果','香蕉']);该示例使select中苹果和香蕉两项被选中。
```

#### 4、遍历节点操作children()、next()、prev()、siblings()和closest()

children()方法用于取得匹配元素的子元素集合，只匹配子元素不考虑任何后代元素。方法如下：$(selector).children();例：

```javascript
　("("body").children().length;//该示例获得body元素的子元素个数；
```

next()方法用于匹配元素的下一个兄弟节点，方法如下:$(selector).next();例：

```javascript
　$("p").next().html();//该示例获得p元素的下一个兄弟节点的html内容。
```

prev()方法用于匹配元素的上一个兄弟节点，方法如下：$(selector).prev();例：

```javascript
　$("ul").prev().text();//该示例获得ul元素的上一个兄弟节点的文本内容。
```

siblings方法()用于匹配目标元素的所有兄弟元素，方法如下：$(selector).siblings();例：

```javascript
 $("p").slibings();//示例获得p元素的所有兄弟节点元素。
```

closest()方法()用来取得最近的匹配元素，首先检查当前元素是否匹配如果匹配则直接返回，否则继续向上查找父元素中符合条件的元素返回，如果没有匹配的元素则返回空JQuery对象。

#### 5、CSS-Dom操作css()、offset()、position()、scrollTop()和scrollLeft()

css()方法用于获取、设置元素的一个或多个属性。方法如下：$(selector).css();例：

```javascript
$("p").css("color","red");//该示例用于设置元素的颜色属性为红色;
   $("p").css("color")//该示例用于获得元素的color样式值;
　$("p").css({"font-size":"30px","backgroundColor","#888888"});//该示例用于设置元素的多个样式。
```

offset()方法用于获取元素相对当前窗体的偏移量，其返回对象包括两个属性：top和left。方法如下：$(selector).offset()

```javascript
　var offset= $("p").offset(); var left=offset.left;var top=offset.top;//该示例用于获得元素p的偏移量。
```

PS:offset()只对可见元素有效。

position()方法用于获取元素于最近的个position样式属性设置为relative或者absolute的祖交节点的相对偏移量。方法如下：$(selector).position();例：

```javascript
　var postion = $("p").positon();var left=positon.left;var top=positon.top;//该示例用于获得元素p的位置。
```

scrollTop()和scrollLeft()方法用于获取元素的滚动条距顶端的距离和距左侧的距离。方法如下:(selector).scrollTop();(selector).scrollLeft();例：

```javascript
var scrollTop=("p").scrollTop();var scrollLeft=("p").scrollLeft();//该示例用于获得元素的滚动条的位置。
```

也可以添加参数将元素滚动到指定的位置。例：

```javascript
　("textarea").scrollTop(300);("textarea").scrollLeft(300);
```

