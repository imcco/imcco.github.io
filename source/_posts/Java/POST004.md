---
title: Java成神之路-jQuery和XML（四）
tags: Java
category: Java
date: 2018-02-23 12:44:36
---
![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0129.jpg)

Java成神之路-jQuery和XML
<!--more-->
## jQuery 的选择器

```js
$("#myELement")//选择id值等于myElement的元素，id值不能重复在文档中只能有一个id值是myElement所以得到的是唯一的元素 
$("div")// 选择所有的div标签元素，返回div元素数组 
$(".myClass") //选择使用myClass类的css的所有元素 
$("*") //选择文档中的所有的元素，可以运用多种的选择方式进行联合选择：例如$("#myELement,div,.myclass") 
```

### 层叠选择器

```js
$("form input")         选择所有的form元素中的input元素 
$("#main > *")          选择id值为main的所有的子元素 
$("label + input")     选择所有的label元素的下一个input元素节点，经测试选择器返回的是label标签后面直接跟一个input标签的所有input标签元素 
$("#prev ~ div")       同胞选择器，该选择器返回的为id为prev的标签元素的所有的属于同一个父元素的div标签 
```

### 基本过滤选择器 

```js
$("tr:first")               选择所有tr元素的第一个 
$("tr:last")                选择所有tr元素的最后一个 
$("input:not(:checked) + span")   
```

过滤掉：checked的选择器的所有的input元素 

```js
$("tr:even")               选择所有的tr元素的第0，2，4... ...个元素（注意：因为所选择的多个元素时为数组，所以序号是从0开始）  
$("tr:odd")                选择所有的tr元素的第1，3，5... ...个元素 
$("td:eq(2)")             选择所有的td元素中序号为2的那个td元素 
$("td:gt(4)")             选择td元素中序号大于4的所有td元素 
$("td:ll(4)")              选择td元素中序号小于4的所有的td元素 
$(":header") 
$("div:animated") 
```

### 内容过滤选择器 

```js
$("div:contains('John')") 选择所有div中含有John文本的元素 
$("td:empty")           选择所有的为空（也不包括文本节点）的td元素的数组 
$("div:has(p)")        选择所有含有p标签的div元素 
$("td:parent")          选择所有的以td为父节点的元素数组 
```



### 可视化过滤选择器

```js
$("div:hidden")        选择所有的被hidden的div元素 
$("div:visible")        选择所有的可视化的div元素 
```

### 属性过滤选择器

```js
$("div[id]")              选择所有含有id属性的div元素 
$("input[name='newsletter']")    选择所有的name属性等于'newsletter'的input元素 
$("input[name!='newsletter']") 选择所有的name属性不等于'newsletter'的input元素 
$("input[name^='news']")         选择所有的name属性以'news'开头的input元素 
$("input[name$='news']")         选择所有的name属性以'news'结尾的input元素 
$("input[name*='man']")          选择所有的name属性包含'news'的input元素 
$("input[id][name$='man']")    可以使用多个属性进行联合选择，该选择器是得到所有的含有id属性并且那么属性以man结尾的元素 
```

### 子元素过滤选择器

```js
$("ul li:nth-child(2)"),$("ul li:nth-child(odd)"),$("ul li:nth-child(3n + 1)") 
$("div span:first-child")          返回所有的div元素的第一个子节点的数组 
$("div span:last-child")           返回所有的div元素的最后一个节点的数组 
$("div button:only-child")       返回所有的div中只有唯一一个子节点的所有子节点的数组 
```

### 表单元素选择器

```js
$(":input")                  选择所有的表单输入元素，包括input, textarea, select 和 button 
$(":text")                     选择所有的text input元素 
$(":password")           选择所有的password input元素 
$(":radio")                   选择所有的radio input元素 
$(":checkbox")            选择所有的checkbox input元素 
$(":submit")               选择所有的submit input元素 
$(":image")                 选择所有的image input元素 
$(":reset")                   选择所有的reset input元素 
$(":button")                选择所有的button input元素 
$(":file")                     选择所有的file input元素 
$(":hidden")               选择所有类型为hidden的input元素或表单的隐藏域 
```

### 表单元素过滤选择器 

```js
$(":enabled")             选择所有的可操作的表单元素 
$(":disabled")            选择所有的不可操作的表单元素 
$(":checked")            选择所有的被checked的表单元素 
$("select option:selected") 选择所有的select 的子元素中被selected的元素 

选取一个 name 为”S_03_22″的input text框的上一个td的text值
$(”input[@ name =S_03_22]“).parent().prev().text() 
 
名字以”S_”开始，并且不是以”_R”结尾的
$(”input[@ name ^='S_']“).not(”[@ name $='_R']“) 
 
一个名为 radio_01的radio所选的值
$(”input[@ name =radio_01][@checked]“).val(); 

$("A B") 查找A元素下面的所有子节点，包括非直接子节点
$("A>B") 查找A元素下面的直接子节点
$("A+B") 查找A元素后面的兄弟节点，包括非直接子节点
$("A~B") 查找A元素后面的兄弟节点，不包括非直接子节点 
```

### 总结

```js
为了方便记忆，将功能相关的选择器进行总结：

1. $('#id')与$('.calss')

$('#id');  　　　　　 // 根据id选择元素
$('.class');  　　   // 根据class选择元素
 

2.$('div:first')与$('div:last')

$('div:first');     // 选择div元素集合中的第一个  
$('div:last');      // 选择div元素集合中的最后一个
 

3.$('div:odd')与$('div:even')

$('div:odd');       // 选择div元素集合中的奇数个元素  
$('div:even');      // 选择div元素集合中的偶数个元素
 

4.$('div:gt(i)')与$('div:lt(i)')

$('div:gt(i)');       // 选择div元素集合中索引大于i的元素  
$('div:lt(i)');       // 选择div元素集合中索引小于i的元素
 

5.$('div:empty')与$('div:parent')

$('div:empty');        // 选择div元素集合中空元素
$('div:parent');       // 选择div元素集合中非空元素
 

6.$('div:visible')与$('div:hidden')

$('div:visible');      // 选择div元素集合中可见元素
$('div:hidden');       // 选择div元素集合中隐藏元素
 

7.$('ul li:first')与$('ul li:first-child')

$('ul li:first');             // 选择所有ul后代元素li元素集合中的第一个 
$('ul li:first-child');       // 选择每个ul后代元素li元素集合中的第一个
 

8.$(':disable')与$(':enable')

$(':disable');             // 选择所有可以操作的表单元素
$(':enable');              // 选择所有不可以操作的表单元素
 
```



### 范例

1. $("A B") 查找A元素下面的所有子节点，包括非直接子节点 

例子：找到表单中所有的 input 元素 

HTML 代码: 

```html
<form>
<label>Name:</label>
<input name="name" />
<fieldset>
      <label>Newsletter:</label>
      <input name="newsletter" />
</fieldset>
</form>
<input name="none" /> 
```

jQuery 代码: 

```js
$("form input") 
```

结果: 

```
[ <input name="name" />, <input name="newsletter" /> ] 
```

2. $("A>B") 查找A元素下面的直接子节点 

例子：匹配表单中所有的子级input元素。 

HTML 代码: 

```html
<form>
<label>Name:</label>
<input name="name" />
<fieldset>
      <label>Newsletter:</label>
      <input name="newsletter" />
</fieldset>
</form>
<input name="none" /> 
```


jQuery 代码: 

```javascript
$("form > input") 
```


结果: 

```
[ <input name="name" /> ] 
```



3. $("A+B") 查找A元素后面的兄弟节点，包括非直接子节点 
  例子：匹配所有跟在 label 后面的 input 元素 

HTML 代码: 

```html
<form>
<label>Name:</label>
<input name="name" />
<fieldset>
      <label>Newsletter:</label>
      <input name="newsletter" />
</fieldset>
</form>
<input name="none" /> 
```


jQuery 代码: 

```javascript
$("label + input") 
```


结果: 

```
[ <input name="name" />, <input name="newsletter" /> ] 
```




4. $("A~B") 查找A元素后面的兄弟节点，不包括非直接子节点 
  例子：找到所有与表单同辈的 input 元素 

HTML 代码: 

```html
<form>
<label>Name:</label>
<input name="name" />
<fieldset>
      <label>Newsletter:</label>
      <input name="newsletter" />
</fieldset>
</form>
<input name="none" /> 
```


jQuery 代码: 

```javascript
$("form ~ input") 
```


结果: 

```
[ <input name="none" /> ]
```

## JQuery的Dom操作

### 样式操作

1、直接样式操作

    语法：
    css(name,value);   //设置单个属性
    css({name:value,name:value,....}) //同时设置多个属性，大括号包裹、冒号分割name与value，不同属性之间用逗号分隔


2、追加样式和移除样式
    语法：
    addClass("class") //追加单个样式
    addClass("class1 class2 ...")  //追加多个样式，同空格分隔

3、移除样式
    语法：
    removClass("class")  //移除单个样式
    removClass("class1 class2") //移除多个样式，有空格分隔
```html
//示例
<script type="text/javascript">
$(document).ready(function(){
//触发button的单击事件时，移除p标签的intro样式
  $("button").click(function(){
    $("p").removeClass("intro");
  });
});
</script>
<style type="text/css">
.intro{
    font-size:120%;
    color:red;
	}
</style>
</head>
<body>
<h1 id="h1">This is a heading</h1>
<p class="intro">This is a paragraph.</p>
<p>This is another paragraph.</p>
<button>从第一个段落中删除类</button>
</body>
```

4、切换样式

```
    使用toggle()方法可以切换元素的可见状态，而使用toggleClass()方法可以切换不同元素的类样式。
    语法：
    toggleClass("class");

```

```html
//示例
<script type="text/javascript">
$(document).ready(function(){
  $("button").click(function(){

    //触发单击事件时，切换为main样式，再次单击，回到原来样式
    $("p").toggleClass("main");
  });
});
</script>
<style type="text/css">
.main
{
font-size:120%;
color:red;
}
</style>
</head>

<body>
<h1 id="h1">This is a heading</h1>
<p>This is a paragraph.</p>
<p>This is another paragraph.</p>
<button class="btn1">切换段落的 "main" 类</button>
```

### **内容操作**

1、HTML代码操作

```

    语法：
    html(["content"]);

    定义和用法
    html() 方法返回或设置被选元素的内容 (inner HTML)。
    如果该方法未设置参数，则返回被选元素的当前内容。

```
返回元素内容

当使用该方法返回一个值时，它会返回第一个匹配元素的内容。

```html
    //示例
    <script type="text/javascript">
$(document).ready(function(){
  $(".btn1").click(function(){
    //未设置参数，返回p元素的值：This is a paragraph.
    alert($("p").html());
  });
});
</script>
</head>
<body>
<p>This is a paragraph.</p>
<button class="btn1">改变 p 元素的内容</button>
</body>
```

设置元素内容
当使用该方法设置一个值时，它会覆盖所有匹配元素的内容。

```html
//示例
<script type="text/javascript">
$(document).ready(function(){
  $(".btn1").click(function(){
    //所有p元素的值被修改为 Hello World!
    $("p").html("Hello <b>world!</b>");
  });
});
</script>
</head>
<body>
<p>This is a paragraph.</p>
<p>This is another paragraph.</p>
<button class="btn1">改变 p 元素的内容</button>
</body>
```
使用函数来设置元素内容

使用函数来设置所有匹配元素的内容。

```
    语法：
    $(selector).text(function(index,oldcontent))
    描述：
    必需。规定返回被选元素的新文本内容的函数。
    index - 可选。接受选择器的 index 位置。
    oldcontent- 可选。接受选择器的当前内容。

```

```html
    //示例
<script type="text/javascript">
    $(document).ready(function(){
      $("button").click(function(){
        $("p").html(function(n,oldcontent){

        //可以获得选择器的当前内容（这是一个段落、这是另一个段落）
         alert(oldcontent); 

        //返回新的内容（这个p元素的index是0，这个p元素的index是1）
        return "这个 p 元素的 index 是：" + n;
        });
      });
    });
</script>
</head>
<body>
    <p>这是一个段落。</p>
    <p>这是另一个段落。</p>
    <button>改变 p 元素的内容</button>
</body>
```

```
2.标签内容操作
    语法：text([content]);

    text() 方法设置或返回被选元素的文本内容。（content为空时返回，有值时设置）

a.返回文本内容

当该方法用于返回一个值时，它会返回所有匹配元素的组合的文本内容（会删除 HTML 标记）。

```

```
//示例
$(document).ready(function(){
  $(".btn1").click(function(){

    //返回所有匹配的p标签内容（This is a paragraph.、This is another paragraph.）
    alert($("p").text());
  });
});
</script>
</head>
<body>
<p>This is a paragraph.</p>
<p>This is another paragraph.</p>
<button class="btn1">获得 p 元素的文本内容</button>
</body>
```


设置文本内容
当该方法用于设置值时，它会覆盖被选元素的所有内容。


```html
//示例
<script type="text/javascript">
$(document).ready(function(){
  $(".btn1").click(function(){
   //将所有匹配的p元素的值设置为Hello world!
    $("p").text("Hello world!");
  });
});
</script>
</head>
<body>
<p>This is a paragraph.</p>
<p>This is another paragraph.</p>
<button class="btn1">改变所有 p 元素的文本内容</button>
</body>
```

| html()与text()方法的区别 |                             |                                                |
| ------------------------ | --------------------------- | ---------------------------------------------- |
| 语法格式                 | 参数说明                    | 功能描述                                       |
| html()                   | 无参数                      | 用于获取**第一个**匹配元素的HTML内容或文本内容 |
| html([content])          | content参数为元素的HTML内容 | 用于设置**所有**匹配元素的HTML内容或文本内容   |
| text()                   | 无参数                      | 用于设置**所有**匹配元素的文本内容             |
| text([content])          | content参数为元素的文本内容 | 用于设置**所有**匹配元素的文本内容             |

### 属性值操作

在jQuery中，除了可以使用html()方法和text()方法获取与设置元素内容外，还提供了获取元素value属性值的方法val()。该方法非常常用，多用于操作表单的<input>元素。

语法：
    val([value]);

val() 方法返回或设置被选元素的值。
元素的值是通过 value 属性设置的。该方法大多用于 input 元素。
如果该方法未设置参数，则返回被选元素的当前值。
1、返回 Value 属性
返回第一个匹配元素的 value 属性的值。


```html
//示例
<script type="text/javascript">
$(document).ready(function(){
  $("button").click(function(){
    //返回第一个匹配元素的value属性的值：Bill
    alert($("input:text").val());
  });
});
</script>
</head>
<body>
Firstname: <input type="text" name="fname" value="Bill" /><br />
Lastname: <input type="text" name="lname" value="Gates" /><br /><br />
<button>获得第一个文本域的值</button>
</body>
```

2、设置 Value 属性的值

```html
//示例
<script type="text/javascript">
$(document).ready(function(){
  $("button").click(function(){
    //将value值设置为Bill Gates
    $("input:text").val("Bill Gates");
    //意外发现：input:text 等同于 input[type=text]
    //$("input[type=text]").val("Bill Gates");
  });
});
</script>
</head>
<body>
<p>Name: <input type="text" name="user" /></p>
<button>设置文本域的值</button>
</body>
```

3.使用函数设置 Value 属性的值

```html
//示例
<script type="text/javascript">
$(document).ready(function(){
  $("button").click(function(){
    $("input:text").val(function(n,c){ //n是获取元素的index，c是返回值
      return c + " Gates";
    });
  });
});
</script>
</head>
<body>
<p>Name: <input type="text" name="user" value="Bill" /></p>
<button>设置文本域的值</button>
</body>
```

**html()、text()、val()共同点：**

1、都包含无参方法，等于无参方法时，返回各自的value

2、都包含有参方法，等于有参方法时，设置各自的value

3、都可以用函数设置value，其中，函数包含两个值，index和oldvalue，index是匹配元素的索引，oldvalue是匹配元素当前的value，都可以省略。

### 节点与属性操作


1:节点操作

在jQuery中，节点操作主要分为查找、创建、插入、删除、替换和复制6种操作方式。其中，查找、创建、插入、删除和替换节点是日常开发中使用最多，也是最重要的。


a、查找节点

    想要对节点进行操作，即增、删、改和复制，首先必须找到要操作的元素。
    所以，查找的语法示例：
    $("h1").hide();   
    查找h1的元素，并将其隐藏。
    其实，查找元素，就是通过选择器获取元素。


b、创建节点元素

    在之前的文章中，讲解过函数$()。该函数就是用于将匹配的DOM元素转换为jQuery对象的，它就好像一个零件的生产工厂，所以被形象地称为工厂函数。
    $()语法：
    $(selector) //选择器。使用jQuery选择器匹配元素
    $(element)  //DOM元素。以DOM元素来创建jQuery对象
    $(html)     //HTML代码，使用HTML字符串创建jQuery对象

HTML代码创建jQuery对象示例：
    var $newNode1 = $("<li></li>")  //创建空的li元素节点
    var $newNode2 = $("<li>HTML</li>") //创建包含文本的li元素节点
    var $newNode3 = $("<li title="标题">HTML</li>") //创建包含文本与属性的li元素节点

c、插入节点


| 插入节点方法          |                                                              |                                                              |
| --------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 插入方式              | 方法                                                         | 描述                                                         |
| 内部插入              | appen(content)                                               | 向所选的元素内部插入内容，即(A).append(B)表示将B追加到A中。如 (“ul”).append($newNode2);追加的位置在元素的最后 |
| appendTo(content)     | 把所选择的元素追加到另一个指定的元素集合中，既(A).appendTo(B)，表示把A追加到B中，如($newNode2).appendTo(“ul”);与append的唯一区别是语法顺序相反 |                                                              |
| prepend(content)      | 向每个选择的元素内部前置内容，即(A).prepend(B)表示将B追加到A中，如(“ul”).perpend($newNode2); |                                                              |
| prependTo(content)    | 将所有匹配元素前置到指定元素中。该方法仅颠倒了常规prepend()插入元素的操作，即$(A).perpendTo(B)表示将A前置到B中，如(newNode2).prependTo(“ul”) |                                                              |
| 外部插入              | after(content)                                               | 在每个匹配元素之后插入内容，即(A).after(B)表示将B插入到A之后，如(“ul”).after($newNode2); |
| insertAfter(content)  | 将所有匹配的元素插入到指定元素的后面。该方法颠倒了常规after()插入元素的操作，$(A).insertAfter(B)表示将A插入到B之后，如(newNode2).insertAfter(“ul”); |                                                              |
| before(content)       | 向所选择的元素外面前面插入元素，即$(A).before(B)表示将B插入至A之前，如(“ul”).before(newNode2); |                                                              |
| insertBefore(content) | 将所匹配的元素插入到指定元素的前面，该方法仅是颠倒了常规before()插入元素的操作，即$(A).insertBefore(B)表示将A插入至B之前，如(newNode2).insertBefore(“ul”); |                                                              |

**注意：**每个语句之前都需要添加$符号。

d、删除节点
```
    语法：(selector).remove([expr]);
    参数expr为可选参数，如果接受参数，则该参数为筛选元素的jQuery表达式，通过该表达式获取指定元素，并进行删除。

    示例：$("ul li:eq(1)").remove();

    定义和用法
    remove() 方法移除被选元素，包括所有文本和子节点。
    该方法不会把匹配的元素从 jQuery 对象中删除，因而可以在将来再使用这些匹配的元素。
    但除了这个元素本身得以保留之外，remove() 不会保留元素的 jQuery 数据。其他的比如绑定的事件、附加的数据等都会被移除。这一点与 detach() 不同。

```

更多解说，[查看这里](http://www.w3school.com.cn/jquery/manipulation_remove.asp)

```
语法：$(selector).empty()
empty() 方法从被选元素移除所有内容，包括所有文本和子节点。

```

更多解说，[查看这里](http://www.w3school.com.cn/jquery/manipulation_empty.asp)

```
语法：$(selector).detach();
detach() 方法移除被选元素，包括所有文本和子节点。
这个方法会保留 jQuery 对象中的匹配的元素，因而可以在将来再使用这些匹配的元素。
detach() 会保留所有绑定的事件、附加的数据，这一点与 remove() 不同。

```

更多解说，[查看这里](http://www.w3school.com.cn/jquery/manipulation_detach.asp)

e、替换节点
```

使用的方法：
    replaceWith()方法和replaceAll()方法
示例：
    $("ul li:eq(1)").replaceWith($newNode2);
    $($newNode2).replaceAll("ul li:eq(1)");

    两种方法的作用相同，只是颠倒了顺序。
```

f、复制节点
```

    语法：$(selecrot).clone([includeEvents]);
    includeEvents为可选值，为布尔值，规定是否复制元素的所有事件处理。true复制，flase不复制。

```

```html
//示例
<script type="text/javascript">
$(document).ready(function(){
  $("button").click(function(){
    //复制第一个p元素，包括事件处理，然后添加到body中。
    $("body").append($("p:first").clone(true));
  });
  $("p").click(function(){
    $(this).animate({fontSize:"+=1px"});
  });
});
</script>
</head>
<body>

<p>点击本段落可以增加文本的大小。事件处理器同样被复制到新的段落。</p>
<button>复制每个 p 元素，然后追加到 body 元素</button>

</body>
```

### 节点遍历

a.在jQuery中，遍历子元素的方法只有一个，即children()方法。


```

语法：$(childred).children([expr])

expr可选，用于过滤子元素的表达式

children() 方法返回被选元素的所有直接子元素。

该方法只会向下一级对 DOM 树进行遍历。
示例：

//p.c用于过滤子元素

$("div").children("p.c").css({"color":"red","border":"2px solid red"});

```


更多信息，[查看这里1](http://www.w3cschool.cn/jquery/jquery-traversing-descendants.html)，[查看这里2](http://www.w3school.com.cn/jquery/traversing_children.asp)
b.遍历同辈元素

| 遍历同辈元素的方法说明 |                                                              |
| ---------------------- | ------------------------------------------------------------ |
| 方法                   | 描述                                                         |
| next([expr])           | 用于获取紧邻匹配元素之后的元素。参数expr可选，用于过滤同辈元素的表达式，如$(“li:eq(1)”).next().css(“color”,”red”); |
| prev([expr])           | 用于获取紧邻匹配元素之前的元素。参数expr可选，用于过滤同辈元素的表达式，如$(“li:eq(1)”).prev().css(“color”,”red”); |
| siblings([expr])       | 用于获取紧邻匹配元素前面和后面的所有同辈元素。参数expr可选，用于过滤同辈元素的表达式，如$(“li:eq(1)”).siblings().css(“color”,”red”); |


c.遍历前辈元素
用与遍历父辈元素的方法主要有parent()和parents()。

| parent()方法和parents()方法的参数说明 |                                                    |
| ------------------------------------- | -------------------------------------------------- |
| 方法                                  | 描述                                               |
| parent([selector])                    | 参数可选。获取当前匹配元素集合中每个元素的父级元素 |
| parents([selector])                   | 参数可选。获取当前匹配元素集合中每个元素的祖先元素 |

**注意：**除了以上介绍的节点遍历方法之外，jQuery中还有许多遍历的方法，如each()、find()、filter()等，[查看更多](http://www.w3school.com.cn/jquery/jquery_ref_traversing.asp)

### CSS-DOM操作

| CSS-DOM相关操作方法说明 |                                                              |                              |
| ----------------------- | ------------------------------------------------------------ | ---------------------------- |
| 参数                    | 描述                                                         | 示例                         |
| css()                   | 设置或返回匹配元素的样式属性                                 | $(“#box”).css(“color”,”red”) |
| heigh([value])          | 参数可选。设置或返回匹配元素的高度。如果没有规定长度单位，则使用默认px作为单位 | $(“#box”).heigh(180)         |
| width([value])          | 参数可选。设置或返回匹配元素的宽度。如果没有规定长度单位，则使用默认px作为单位 | $(“#box”).width(180)         |
| offset([value])         | 返回以像素为单位的top和left坐标。此方法仅对可见元素有效      | $(“#box”).offset()           |
| offsetParent([value])   | 返回最近的已定位的祖先元素。定位元素指的是元素的CSS position值被设置为relative、absolute或fixed的元素 | $(“#box”).offsetParent()     |
| scrollLeft([position])  | 参数可选。设置或返回匹配元素相对滚动条左侧的偏移             | $(“#box”).scorllLeft(20)     |
| scrollTop([posttion])   | 参数可选。设置或返回匹配元素相对滚动条顶部的偏移             | $(“#box”).scrollTop(120)     |


此外，注意，选取元素的高度除了可以使用height()方法之外，还能使用css()方法，其获取高度值的代码为$("#box").css("height")。两者的区别在于使用css获取元素高度与样式设置有关，可能会得到“auto”，也可能得到“60px”之类的字符串；而height()方法获取的高度值则是元素在页面中的实际高度，与样式的设置无关，且不带单位。获取元素宽度的方式也是同理。


更多介绍，[查看这里](http://www.w3school.com.cn/jquery/jquery_ref_css.asp)

## jquery中的动画和事件

### 一.事件(重要)

#### jquery绑定事件

**jQuery对象.bind(“事件名”,可选参数,事件处理函数)**

注意:

1. 第二个参数为可选参数, 作为event.data属性值传递给事件对象的额外数据对象.
2. 事件名不要加on

`jquery对象.bind("事件名",function(){      })`

需求: 点击 h5标题后,显示h5下的div

演示代码:

```html
<!DOCTYPE html>
<html>

<head>
    <meta charset="UTF-8">
    <title>演示事件</title>
    <style type="text/css">
        * {
            margin: 0px;
            padding: 0px;
        }
        
        body {
            font-size: 13px;
            line-height: 130%;
            padding: 60px;
        }
        
        #panel {
            width: 300px;
            border: 1px solid #0050D0;
        }
        
        .head {
            padding: 5px;
            background: #96E555;
            cursor: pointer;
        }
        
        .content {
            padding: 10px;
            text-indent: 2em;
            border-top: 1px solid #0050D0;
            display: none;
        }
    </style>

    <scriptsrc="../js/jquery/jquery-1.7.2.js" type="text/javascript" charset="utf-8">
        </script>
        <scripttype="text/javascript">
            $(function() { 
            //需求:点击标题后显示标题下面的div
            $("#panelh5.head").bind("click",function(){ 
            //让标题下面的div显示 
           				 $(this).next().show(); 
            										}); 
         				});
            </script>
</head>

<body>
    <div id="panel">
        <h5class="head">什么是jQuery?</h5>
            <divclass="content">
jQuery是继Prototype之后又一个优秀的JavaScript库，它是一个由 John Resig 创建于2006年1月的开源项目。jQuery凭借简洁的语法和跨平台的兼容性，极大地简化了JavaScript开发人员遍历HTML文档、操作DOM、处理事件、执行动画和开发Ajax。它独特而又优雅的代码风格改变了JavaScript程序员的设计思路和编写程序的方式。
    	</div>
    </div>
</body>
</html>
```

#### 取消绑定

1.取消click事件的所有事件处理函数

`jQuery对象.unbind("click")`

2.取消click事件的事件处理函数f2

`jQuery对象.unbind("click",f2);`

fn1=function(){}   这个也可以作为函数的参数,相当于function(){},并且这个函数叫fn1

#### 只执行一次事件

`对象.one("事件名",事件处理函数);//只执行一次`

#### 触发某一个事件

`对象.trigger("click");  //相当于对象.click();`

#### div的显示和隐藏

```javascript
div.show("slow");
div.show("normal");
div.show("fast");
```

可见就隐藏,不可见就显示

```js
if(div对象.is(":visible")){
       div对象.hide(3000);
}else{
       div对象.show(3000);
}
```

#### 改变绑定事件的类型

mouseover

mouseout

演示代码:

```js
$(function() {

    //需求: 点击标题后 显示标题下面的div
    $("#panel").bind("mouseover", function() {
        $(this).find("div.content").show();
    });
    $("#panel").bind("mouseout", function() {
        $(this).find("div.content").hide();
    });
});

或者合成一句

$(function() {
    //需求: 点击标题后 显示标题下面的div
    $("#panel").bind("mouseover", function() {
        $(this).find("div.content").show();
    }).bind("mouseout", function() {
        $(this).find("div.content").hide();
    });
});

或者

$(function() {
    //需求: 点击标题后 显示标题下面的div
    $("#panel").mouseover(function() {
        $(this).find("div.content").show();
    }).mouseout(function() {
        $(this).find("div.content").hide();
    });
});
```

#### 选项卡的制作

演示代码:

```html
<!DOCTYPE HTML PUBLIC "-//W3C//DTDHTML 4.01 Transitional//EN">

<html>

<head>

    <title>网页选项卡</title>

    <metahttp-equiv="content-type" content="text/html;charset=UTF-8">

        <scriptsrc="../../js/jquery/jquery-1.7.2.js" type="text/javascript" charset="utf-8">
            </script>

            <style type="text/css">
                * {
                    margin: 0;
                    padding: 0;
                }
                
                body {
                    font: 12px/19px Arial, Helvetica, sans-serif;
                    color: #666;
                }
                
                .tab {
                    width: 240px;
                }
                
                .tab_menu {
                    clear: both;
                }
                
                .tab_menuli {
                    float: left;
                    text-align: center;
                    cursor: pointer;
                    list-style: none;
                    padding: 1px 6px;
                    margin-right: 4px;
                    background: #F1F1F1;
                    border: 1px solid #898989;
                    border-bottom: none;
                }
                
                .tab_menuli:hover {
                    background: #DFDFDF;
                }
                
                .tab_menuli.selected {
                    color: #FFF;
                    background: #6D84B4;
                }
                
                .tab_box {
                    clear: both;
                    border: 1px solid #898989;
                    height: 100px;
                }
                
                .hide {
                    display: none
                }
            </style>

            <script src="../../js/jquery/jquery-1.7.2.js" type="text/javascript" charset="utf-8"></script>



            <script type="text/javascript">
                $(function() {
                    //给所有的li绑定单击事件 
                    var$menu_li = $("div.tab_menu li");
                    $menu_li.click(function() {
                        //1.将点击的li高亮 
                        $(this).addClass("selected");
                        //并去掉其他的高亮 
                        $(this).siblings().removeClass("selected");
                        //2.让对应的div显示 
                        //点击第1个li 显示第1个div 点击第2个li 显示第2个div 
                        //首先获得点击了第几个li
                        var clickedLiIndex = $menu_li.index($(this));
                        $("div.tab_box>div").eq(clickedLiIndex).show();
                        $("div.tab_box>div").eq(clickedLiIndex).siblings().hide();
                    });
                });
            </script>

</head>

<body>

    <div class="tab">

        <divclass="tab_menu">

            <ul>

                <liclass="selected">时事</li>

                    <li>体育</li>

                    <li>娱乐</li>

            </ul>

    </div>

    <divclass="tab_box">

        <div>时事</div>

        <div class="hide">体育</div>

        <div class="hide">娱乐</div>

        </div>

        </div>

</body>

</html>
```

 

#### 合成事件 2个

jQuery有两个合成事件-----hover()方法和toggle()方法.

#### 1.hover(enter,leave);

`hover = mouseover + mouseout ;` 

hover()方法用于模拟光标悬停事件.当光标移动到元素上时,执行第一个函数(enter),当光标移出这个元素时,会触发第二个函数(leave). 

上面的例子可以改写成hover()方法 

演示代码:

```js
$("#panel").hover(function() {
              $(this).find("div.content").show();
},function(){
              $(this).find("div.content").hide();
});
```

 #### 2.toggle(事件处理函数1，事件处理函数2,….); //开关函数

jQuery1.9 以后取消了toggle事件

`toogle(fn1,fn2,…fnN)`

toogle()方法用于模拟鼠标连续单击事件。第1次单击元素,触发第一个函数(fn1),第二次单击同一个元素,触发fn2…如果有多个函数,依次触发,直到最后一个,随后的每次单击从头开始轮番调用这几个函数。

 

前面的单击标题例子中,使用了如下代码:



虽然能实现效果,但是这种方法不是最合适的.如果需要连续单击”标题”,来达到使”内容”显示和隐藏的目的,那么很适合使用toggle()方法

演示代码:



```js
$(function() {

    $("#btn1").click(function() {

        vardiv1 = ("#div1");

        if ($div1.is(":visible")) {

            $div1.hide(3000);

        } else {

            $div1.show(3000);

        }

    });

});
```

toggle()方法还有另外一个作用:切换元素的可见状态.如果元素是可见的,单击切换后则隐藏；

如果元素是隐藏的,单击切换后则为可见的.

对于层来说 div对象.toggle();//显示的层变隐藏、隐藏的层变显示

 

#### 加强效果 

为了能有更好的用户体验,现在需要在用户单击”标题”后,不仅显示内容,而且高亮显示”标题”。

单击标题后,标题高亮显示

为了完成这一功能,首先在CSS中定义一个高亮的样式,CSS代码如下:

```css
.highlight { background-color:#ff3300;}
```

演示代码:

```js
$(function() {
                            //需求: 点击标题后 显示标题下面的div
                            $("#panel").click(function() {
                                   $(this).find("div.content").toggle();                    
                                   $(this).find("h5").toggleClass("highlight");
                            }); 
});
```

 

### 二.动画

#### 内置动画

##### 1.show()和hide()

```js
div.show("slow");//0.6秒
div.show("normal");//0.4秒
div.show("fast");//0.2秒
div.show(毫秒);
```

如果不加参数直接调用show()是立即显示 没有动画效果 

增加宽度(从左到右增大)、增加高度(从上到下增大),同时增加内容的不透明度

##### 2.fadeIn()和fadeOut()淡入淡出效果

`对象.fadeIn();//淡入 增加不透明度`

`对象.fadeOut();//淡出 减少不透明度 直到元素完全消失(display:none)` 

与show()方法不同,fadeIn()和fadeOut()方法只改变元素的不透明度.

##### 3.slideUp()、slideDown()收缩、展开效果(用于层)

`slideUp()、slideDown()  只会改变元素的高度`

 

##### 动画积累问题解决

当用户快速在某个元素上执行animate()动画时,就会出现动画积累.解决方法是判断元素是否处于动画状态,如果元素不处于动画状态,才为元素添加新的动画,如果元素正处于动画状态不添加动画. 

```js
$(function() {

    //需求: 点击标题后 显示标题下面的div
    $("#panel").toggle(function() {
        if (!$("#panel").find("div.content").is(":animated")) { //不处在动画状态中
            $("#panel").find("div.content").slideDown("slow");
        }
    }, function() {
        if (!$("#panel").find("div.content").is(":animated")) {
            $("#panel").find("div.content").slideUp("slow");
        }
    });
});
```

#### 自定义动画animate()

\1. 自定义简单动画

```js
animate({left:"500px"},3000,function(){alert(3);}); 
```

参数1：让div向右移动500px

参数2：移动所需的时间(可以省略)

参数3:  移动完成调用回调函数(可以省略)

完整演示代码如下:

```html
<!DOCTYPE html>

<html>

<head>

    <meta charset="UTF-8">

    <title>演示事件</title>

    <style type="text/css">
        #panel {
            width: 100px;
            height: 100px;
            background-color: yellowgreen;
            position: relative;
            cursor: pointer;
        }
    </style>

    <script src="../jquery-1.7.2.js" type="text/javascript" charset="utf-8"></script>

    <scripttype="text/javascript">

        $(function(){ $("#panel").click(function(){ 							$(this).animate({left:"500px"},3000,function(){ 
        $(this).fadeOut(2000); 
      	  	}); 
      	  }); 
        });

        </script>

</head>

<body>

    <div id="panel">

        保存成功

    </div>

</body>

</html>
```

2. 多重动画 (1)同时执行多个动画

```html
<!DOCTYPE html>

<html>

<head>

    <meta charset="UTF-8">

    <title>演示事件</title>

    <style type="text/css">
        * {
            margin: 0px;
            padding: 0px;
        }
        
        #img1 {
            position: relative;
        }
    </style>

    <scriptsrc="../jquery-3.1.0.js" type="text/javascript" charset="utf-8">
        </script>

        <scripttype="text/javascript">

            $(function(){ $("#img1").click(function(){ $(this).animate({left:"500px",height:"200px"},3000); }); });

            </script>

</head>

<body>

    <img id="img1" src="../../../image/smile.png" />

</body>

</html>
```


(2)按顺序执行多个动画 

上例中是移动和调整宽度同时进行,如果想按顺序执行动画,例如先向右滑动,然后再放大它的高度,只需把代码拆开 

```js
$(function() {
                            $("#img1").click(function(){
                                   $(this).animate({left:"500px"},3000);
                                   $(this).animate({width:"200px"},3000);
                            });
});
```

 

因为animate都是对同一个jQuery对象操作的,也可以改为链式操作

```javascript
$(function() {
                            $("#img1").click(function(){
                                   $(this).animate({left:"500px"},3000).
                                             animate({width:"200px"},3000);                             
                            });
});
```

像这样动画效果的执行具有先后顺序,称为”动画队列”.

3. 综合动画

```html
<!DOCTYPE html>

<html>

<head>

    <meta charset="UTF-8">

    <title>演示事件</title>

    <style type="text/css">
        * {
            margin: 0px;
            padding: 0px;
        }
        
        #panel {
            width: 100px;
            height: 100px;
            background-color: burlywood;
            position: relative;
        }
    </style>

    <scriptsrc="../../../js/jquery/jquery-1.7.2.js" type="text/javascript" charset="utf-8">
        </script>

        <scripttype="text/javascript">

            $(function(){ $("#panel").css("opacity","0.5");//设置不透明度 $("#panel").click(function(){ $("#panel").animate({left:"400px",height:"200px",opacity:"1"},3000) .animate({top:"200px",width:"200px"},3000) .fadeOut("slow"); }); });

            </script>

</head>

<body>

    <div id="panel">

    </div>

</body>

</html>
```

 

4. 动画回调函数

如果想在最后一步切换元素的CSS样式,而不是隐藏元素.

需要把最后`fadeOut(“slow”)改为.css("border","5px solid blue");`

但是这样并不能得到预期效果,预期效果是在动画的最后一步改变元素的样式,而实际效果是刚开始执行的时候,css()方法就执行了.出现这个问题的原因是css()方法并不会加入到动画队列中,而是立即执行.可以使用回调函数(callback)对非动画方法实现排队.只要把css()方法写在最后一个动画的回调函数里即可.代码如下:

```js
$(function() {

    $("#panel").css("opacity", "0.5"); //设置不透明度

    $("#panel").click(function() {

        $("#panel").animate({ left: "400px", height: "200px", opacity: "1" }, 3000)

        .animate({ top: "200px", width: "200px" }, 3000, function() {

            $("#panel").css("border", "5pxsolid blue");

        });

    });

});
```

5. 停止动画

```js
$("#btn1").click(function(){
       $("#panel").stop();//只能停止一个动画
});
```

6. 延迟动画

```js
$(function() {

    $("#panel").css("opacity", "0.5"); //设置不透明度

    $("#panel").click(function() {

        $("#panel").animate({ left: "400px", height: "200px", opacity: "1" }, 3000)

        .delay(3000)

        .animate({ top: "200px", width: "200px" }, 3000, function() {

            $("#panel").css("border", "5pxsolid blue");

        });

    });

    //停止动画

    $("#btn1").click(function() {

        $("#panel").stop();

    });

});
```

#### 其他动画方法

`toggle()、slideToggle()、fadeTo()和fadeToToggle();`

fadeTo() 把元素的不透明度调整到指定的值

```js
$(function() {

    //需求: 点击标题后 显示标题下面的div
    $("#panelh5.head").click(function() {
        $(this).next().fadeTo(600, 0.2); //600毫秒 不透明度调整到0.2
    });
})
```

在css中调整不透明度

```css
.content {
                padding: 10px;
                text-indent: 2em;
                border-top: 1px solid #0050D0;
                background-color: rgba(255,0,0,0.5);//alpha参数是介于 0.0（完全透明）与 1.0（完全不透明）的数字。      
}
```

fadeToToggle();  对应fadeIn()和fadeOut()

## 原生和jQuery的ajax用法

form数据的序列化：



```js
$('#submit').click(function(){
    $('#form').serialize();        //会根据input里面的name，把数据序列化成字符串；eg：name=yang
    $('#form').serializeArray();    //会根据input里面的name，把数据序列化成数组；eg：[object]
　　//注意：没有name会获取不到值

    //下面两种不是jQuery的方法
    JSON.parse()    //json字符串转化为json对象
    JSON.stringify()    //json对象转化为json字符串
});
```



jQuery的ajax方法：

```javascript
$.ajax({
    url:'/comm/test1.php',
    type:'POST', //GET
    async:true,    //或false,是否异步
    data:{
        name:'yang',age:25
    },
    timeout:5000,    //超时时间
    dataType:'json',    //返回的数据格式：json/xml/html/script/jsonp/text
    beforeSend:function(xhr){
        console.log(xhr)
        console.log('发送前')
    },
    success:function(data,textStatus,jqXHR){
        console.log(data)
        console.log(textStatus)
        console.log(jqXHR)
    },
    error:function(xhr,textStatus){
        console.log('错误')
        console.log(xhr)
        console.log(textStatus)
    },
    complete:function(){
        console.log('结束')
    }
})
```



原生的ajax方法：



```javascript
$('#send').click(function(){
    //请求的5个阶段，对应readyState的值
        //0: 未初始化，send方法未调用；
        //1: 正在发送请求，send方法已调用；
        //2: 请求发送完毕，send方法执行完毕；
        //3: 正在解析响应内容；
        //4: 响应内容解析完毕；

    var data = 'name=yang';
    var xhr = new XMLHttpRequest();        //创建一个ajax对象
    xhr.onreadystatechange = function(event){    //对ajax对象进行监听
        if(xhr.readyState == 4){    //4表示解析完毕
            if(xhr.status == 200){    //200为正常返回
                console.log(xhr)
            }
        }
    };
    xhr.open('POST','url',true);    //建立连接，参数一：发送方式，二：请求地址，三：是否异步，true为异步
    xhr.setRequestHeader('Content-type','application/x-www-form-urlencoded');    //可有可无
    xhr.send(data);        //发送
});
```

#### jQuery Ajax 操作函数


jQuery 库拥有完整的 Ajax 兼容套件。其中的函数和方法允许我们在不刷新浏览器的情况下从服务器加载数据。

| 函数                                                         | 描述                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [jQuery.ajax()](http://www.w3school.com.cn/jquery/ajax_ajax.asp) | 执行异步 HTTP (Ajax) 请求。                                  |
| [.ajaxComplete()](http://www.w3school.com.cn/jquery/ajax_ajaxcomplete.asp) | 当 Ajax 请求完成时注册要调用的处理程序。这是一个 Ajax 事件。 |
| [.ajaxError()](http://www.w3school.com.cn/jquery/ajax_ajaxerror.asp) | 当 Ajax 请求完成且出现错误时注册要调用的处理程序。这是一个 Ajax 事件。 |
| [.ajaxSend()](http://www.w3school.com.cn/jquery/ajax_ajaxsend.asp) | 在 Ajax 请求发送之前显示一条消息。                           |
| [jQuery.ajaxSetup()](http://www.w3school.com.cn/jquery/ajax_ajaxsetup.asp) | 设置将来的 Ajax 请求的默认值。                               |
| [.ajaxStart()](http://www.w3school.com.cn/jquery/ajax_ajaxstart.asp) | 当首个 Ajax 请求完成开始时注册要调用的处理程序。这是一个 Ajax 事件。 |
| [.ajaxStop()](http://www.w3school.com.cn/jquery/ajax_ajaxstop.asp) | 当所有 Ajax 请求完成时注册要调用的处理程序。这是一个 Ajax 事件。 |
| [.ajaxSuccess()](http://www.w3school.com.cn/jquery/ajax_ajaxsuccess.asp) | 当 Ajax 请求成功完成时显示一条消息。                         |
| [jQuery.get()](http://www.w3school.com.cn/jquery/ajax_get.asp) | 使用 HTTP GET 请求从服务器加载数据。                         |
| [jQuery.getJSON()](http://www.w3school.com.cn/jquery/ajax_getjson.asp) | 使用 HTTP GET 请求从服务器加载 JSON 编码数据。               |
| [jQuery.getScript()](http://www.w3school.com.cn/jquery/ajax_getscript.asp) | 使用 HTTP GET 请求从服务器加载 JavaScript 文件，然后执行该文件。 |
| [.load()](http://www.w3school.com.cn/jquery/ajax_load.asp)   | 从服务器加载数据，然后把返回到 HTML 放入匹配元素。           |
| [jQuery.param()](http://www.w3school.com.cn/jquery/ajax_param.asp) | 创建数组或对象的序列化表示，适合在 URL 查询字符串或 Ajax 请求中使用。 |
| [jQuery.post()](http://www.w3school.com.cn/jquery/ajax_post.asp) | 使用 HTTP POST 请求从服务器加载数据。                        |
| [.serialize()](http://www.w3school.com.cn/jquery/ajax_serialize.asp) | 将表单内容序列化为字符串。                                   |
| [.serializeArray()](http://www.w3school.com.cn/jquery/ajax_serializearray.asp) | 序列化表单元素，返回 JSON 数据结构数据。                     |

#### jQuery插件

##### 前端实用插件

- Highcharts  图表插件，个人所接触上第一个插件，记得没错的话当初应该是由几个大神编写，官网也还没有现在这么强大。掌握基础用法比较简单。当初算是它让我知道什么是插件，怎么样用插件，也是它使我发现插件的美。 所以感触感悟较深，让我第一次感觉强大的功能实现起来如此简单。 当时心境：只要从官网找一些例子，看一看api然后写一些简单的Dome。需要注意的就是它的数据来源全部是JSON格式，简单理解为 拿到Data 转化相应的JSON丢入插件中，图表出现。

官网地址：<https://www.hcharts.cn/demo/highcharts>

- ECharts  百度图表，网上资料较多，由百度推出及维护。这没的说，公认的图表插件，好用实在免费. 

官网地址：[http://echarts.baidu.com](http://echarts.baidu.com/)

- Layer web弹出层组件，使用简便小巧并且免费开源，重要的是它采用MIT开源协议永久免费.  

官网地址：<http://layer.layui.com/>

- UEditor web编辑器组件，同样是百度旗下的，可支持定制，代码开源于Git Hub，使用也简单方便，主要用于论坛类。它主要是将你图片视频包括等文件转化为html代码后显示。

官网地址：<http://ueditor.baidu.com/website/index.html>

##### 综合类

- Bootstrap 后台模板，类似于导航那种，可以通过它找到一些资源.

网站地址：<http://www.jqueryfuns.com/>

- H+ 后台主题UI框架 一个很强大的网站，资源丰富.  啧啧，这个网站是一做PHP的同事告诉我的，但就是有些服务收费. 

网站地址：<http://www.zi-han.net/theme/hplus/>

- Jquery插件库，也类似于导航类，收藏还是不错滴，可以根据相应的功能进行搜索。资源不错呐虽然我也没用过，只是收藏. 

网站地址：<http://www.jq22.com/>

- dowebok 插件导航吧算，有的收费，我没用过找资料时觉着将来可能用得着就着手收藏啦，前端的可能用的多.

网站地址：<http://www.dowebok.com/>

- Bootstrap 模板，包含了很多Bootstrap1，Bootstrap2,.... 有教程等，跟第一个好像是一回事只是不是同一个网站

网站地址：<http://www.bootcss.com/>

- Web开发 金丝燕网，对于Java Web而言用处还是很大的，收藏用还是不错滴。

网站地址：<http://swiftlet.net/>

##### 实用类

- AjaxFileUpload 批量上传插件，实用性特别高，但相对来讲资料比较杂，并未成体系，很实用很实用的插件，尤其是在批量上传时用到。

参考网址：<http://fwhyy.com/2010/01/jquery-upload-plugin-uploadify-use-explanation/>

- open深度开源——服务器相关，网址实用性高，涵盖量比较广.

参考网址：<http://www.open-open.com/lib/list/76?pn=1>

## XML和约束模式

### 一、XML语法

xml 可扩展标记语言，w3c组织发布的，用于保存有关系的数据，作为配置文件，描述程序模块之间的关系

xml 文件开头必须包括下面的标签：

```
<?xml version="1.0" encoding="UTF-8" standalone="yes" ?>
```

encoding 用来指定XML文档的字符编码，一般都是 UTF-8

standalone 用来说明文档是否独立，默认是no，通常用不到这属性（如果是 yes 则表示XML既不需要约束文件来验证标记是否有效，也不需要XSL、CSS控制外观显示）

 

**元素：**由一个标记来定义，包括开始和结束标记以及其中的内容，如 <book>深入体验java web开发内幕</book> （标记可以嵌套；格式良好的xml文档有且仅有一个根标签，其他标签都是根标签的子孙标签；在xml中空行和空格都会被作为原始内容解析）。XML只可有一个根节点。

**命名规范：**区分大小写，<A></A>和<a></a>是两种不同的标签；不能以数字、下划线或者xml开头；不能包含空格、冒号：

**属性：**XML解析属性的速度比解析子标记快，属性值必须用双引或单引引起来，属性也可被改为子标签的形式存储

```
<input name="txt1" />
<input>
    <name>txt1</name>
</input>
```

**CDATA区：**其中的内容不会被xml解析引擎解析，而是作为原始内容显示，如 <![CDATA[这是是CDATA区的内容]]> 

**处理指令：**简称PI，用来指挥xml解析引擎如何解析xml，以“<?”开头，以“?>”结尾，例如：文档声明 <?xml version="1.0" ?> ； <?xml-stylesheet type="text/css" href="1.css" ?> ，它用来通知xml解析引擎使用css文件控制xml显示外观

### **二、XML约束模式**

约束模式定义了XML文档的标记和结构，类似于数据表结构。XML约束模式的内容也要遵循一定的语法规则，其中主流有2种：**XML DTD** 和 **XML Schema**

#### 2.1、XML DTD

DTD约束即可以作为一个单独的文件（以.dtd为后缀）编写，也可以在XML文件内编写。其中包括元素之间的关系定义、元素属性定义、实体和符号的定义。

1. 定义dtn文件bookshelf.dtd（“书”和括号“()”之间有空格，“名称”和“(#PCDATA)”之间同样也有）

```
<!ELEMENT 书架 (书+)>
<!ELEMENT 书 (名称,作者,售价)>
<!ELEMENT 名称 (#PCDATA)>
<!ELEMENT 作者 (#PCDATA)>
<!ELEMENT 售价 (#PCDATA)>
```

2. 在book.xml引入dtd约束文件



```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE 书架 SYSTEM "bookshelf.dtd" >
<书架>
    <书>
        <名称>深入体验java web开发内幕</名称>
        <作者>张孝祥</作者>
        <售价>59元</售价>
    </书>
</书架>
```



3. 在chrome浏览器中的显示如下

![img](https://images2015.cnblogs.com/blog/753478/201612/753478-20161217092121417-433671501.png)

 

**XML文档引用外部DTD约束的2种方式**

- 引用本地dtd文件 <!DOCTYPE 文档根结点 SYSTEM "DTD文件的URL"> 
- 引用公用dtd文件 <!DOCTYPE 文档根结点 PUBLIC "DTD名称" "DTD文件的URL"> 

```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">
```

也可以把dtd约束写在xml文件中



```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE 书架[
    <!ELEMENT 书架 (书+)>
    <!ELEMENT 书 (名称,作者,售价)>
    <!ELEMENT 名称 (#PCDATA)>
    <!ELEMENT 作者 (#PCDATA)>
    <!ELEMENT 售价 (#PCDATA)>
]>
<书架>
    <书>
        <名称>深入体验java web开发内幕</名称>
        <作者>张孝祥</作者>
        <售价>59元</售价>
    </书>
</书架>
```



 

#### 2.2、XML Schema

schema比dtd好，已经成为w3c组织的标准，正逐步取代dtd

**名称空间：**使用名称空间来区分每个约束模式文档，每个名称空间都用一个唯一的URI表示。

**名称空间声明：**在XML文件中为一个约束模式文档的名称空间指定一个临时的简称，这个简称将作为元素和属性的前缀名。名称空间声明和元素的属性定义非常类似，可以位于任何一个元素的开始标记中，并且一个元素中可以声明多个名称空间；名称空间声明的基本格式为 xmlns:前缀名称="URI" ，其中的前缀名称就是临时的简称。 （xmlns是xml namespace的简写）。默认名称空间 xmlns="URI" ，即省略掉前缀名称。

```
<html xmlns:xs="http://www.w3.org/2001/XMLSchema">
```

 

**使用名称空间引入XML Schema文档**

由上面的标记只能知道 "http://www.w3.org/2001/XMLSchema" 是代表某个名称空间的URI，并不能知道名称空间XML Schema文档的访问地址，那就无法对XML文档进行校验。

使用 xs:schemaLocation 以键值对的形式指定名称空间和其对应的xsd文件地址。使用之前，必须先引入xs的命名空间，这样才能使用 xs:schemaLocation 。因为 xmlns:xs="http://www.w3.org/2001/XMLSchema" 这个名称空间众所周知，所有无需指定它的访问地址。



```
<书架 xmlns="http://www.xxx.com/bookshelfSchema" 
　　　　xmlns:test="http://www.demo.com/testSchema" 
　　　　xmlns:xs="http://www.w3.org/2001/XMLSchema" 
　　　　xs:schemaLocation="http://www.xxx.com/bookshelfSchema http://www.xxx.com/xsd/bookshelf.xsd 
                          http://www.demo.com/testSchema http://www.xxx.com/xsd/test.xsd                          
       "
>
```
