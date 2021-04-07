---
title: Java成神之路-JavaScript（三）
tags: Java
category: Java
date: 2018-02-23 11:44:36
---
![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0128.jpg)

Java成神之路-JavaScript
<!--more-->
# JavaScript

## 基本语法

### 分类

ECMAScript  js基本语法与标准
DOM         Document Object Model文档对象模型
BOM         Browser Object Model浏览器对象模型

tips：DOM和BOM都是一套API（Application programing interface）

### 注释方式

```
style   /*  */
body    <!-- --!>
script  //
        /* */
        /**
        *   js说明文档注释
        */
```

### 简单指令

```
alert("");          提示框；
confirm("");        确认框，点击后会响应返回true或false；             
prompt();           弹出一个输入框；
document.write("");
console.log("");    在控制台打印相应的信息；
console.dir("");    在控制台打印出该对象的所有信息；
```

### 变量命名

数字（0-9）、字母（a-z，A-Z）、下划线（_）；

tips:应避免保留字和关键字；

### NaN和isNaN

isNaN(number),如果number不是数字，则为true；
Number(number),在转换为数字类型时，若number不是数字，则返回NaN；

### 转义字符

```
\       
\r  回车
\n  空格
\t  缩进
\\  反斜杠
```

### 逻辑短路、逻辑中断

```
true || 6;      逻辑或短路，左边为ture返回右值；
6   &&  true;   逻辑与短路，左边false返回右值；
```

### 优先级

```
    * / %
    +   -
    &&
    ||
    ?
tips：自上而下优先级越来越高
```

### 类型转换（type）

```
parseInt("12a3");   转为数字，尝试强转；
parseFloat("123.123");

data.toString();
String(data);

tips:变量声明未赋值，其值为undefined；
        对象为空，其值为null；

```

### 三元表达式

```
eg  :   a>b?a=1:a=2;

格式:
    判断条件？true的时候执行的操作：false的时候执行的操作；
```

### 数组Array

```
（1）、定义法
    构造函数：
            var arr = new Array("123","abc","xxx");
    字面量：
            var arr = ["123","646","abc"]; 
    数组长度：
            var arr = new Array(6);(数组长度为6)；
（2）、赋值
    arr[0]=1;
```

### 形参和实参

```
定义函数时，function funcA(a,b,c){}，其中的a、b、c即为形参；
调用函数时，funcA(1,2,3);其中的1、2、3即为实参；

tips：function里面有一个arguments对象，里面存有所有传进函数的实参；
```

### 函数function

```
（1）、函数命名
    1、  可以使用字符、数字、下划线、$；
    2、  不能以数字开头；
    3、  不能使用关键字和保留字；
    4、  区分大小写；
    5、  建议要有意义 --  动词+名字结构；
    6、  驼峰命名法；
    7、  函数名不能重名，后面写的重名函数会把前面写的函数给覆盖掉；

（2）、函数的返回值
返回值：
    当函数执行完毕之后，所得到的结果就是一个函数返回值
    任意函数都有返回值

1、  在函数内部没有显示的写有return的时候，函数的返回值是undefined；
2、  当函数内部有return，但是return后面没有跟着任何内容或者数据的时候，
函数的返回值是undefined，并且return后面的代码不会执行；
3、  当return后面跟着内容或者数据的时候，函数的返回值就是这个跟着的内容或者数据；


（3）、函数的四种形式：
    1、没有参数，没有return；
            通常在于封装一段过程；
    2、没有参数，有return；
            通常用于内部封装引用其他函数（闭包，回调）；
    3、有参数，没有return；
            通常用于执行操作的封装；
    4、有参数，有return；
            常见形式；

（4）、匿名函数
    匿名函数的name属性值为anonymous；
    函数仅用一次的情况，即用即废；

    eg:
        setTimeout(function(){
            console.log(this.name);
        },1000);
    tips:在1秒后在控制台打印出本函数的名称；

（5）、回调函数
    在一个函数当中，另一个函数作为参数传入该函数中，另一个的这个函数即为回调函数；
    eg:
        function atack(callback){
            return callback;
        }
    tips:在调用该函数时，指定callback是哪个函数；
        atack（func）；

（6）、短路运算
    作用：防止传入函数的数据不足，造成无法运行；
    eg：
        function getResult(a,b,fn) {
            fn && fn();
        }（通常使用逻辑与的短路来决定是否执行回调函数；）

        function getResult_2(a,b){
            a || 0;
        }（通常用逻辑或的短路来防止实参不足的情况，强行赋值；）

（7）、自执行函数
    （function func2(){

    }）()

    tips:在函数定义的结束最后写入一个（），该函数定义完成后直接被调用执行；

（8）、递归
    在函数执行的最后再一次的调用自身；

    tips:递归是一种非常耗资源的做法，通常为了简化运算，还会结合缓存进行；
    并且注意，递归必须要有结束判断条件（if），否则该函数被调用后就是死循环；
```

### 数据类型

```
（1）、简单数据类型
    string、number、boolean

（2）、复杂数据类型
    String、Number、Boolean、Array、Math、Date、Obeject、function、RegExp(正则表达式)

（3）、空数据类型
    * Null  ---→Null的数据类型会返回一个Object
    * undifined

    tips：用typeof可以进行判断数据类型；

    tips：定义的简单数据类型变量，其数据保存在变量中；
        而复杂数据类型，其变量保存的是数据所在的内存地址；
```

### 内置对象

```
Array、Date、Math、String；
```

### （Math）数学对象

```
向上取整        Math.ceil(number);
向下取整        Math.floor(number);
四舍五入        Math.round(number);

求多个数字之间的最大值     Math.max();
求多个数字之间的最小值     Math.min();

求x的y次幂      Math.pow(x,y);

求正弦值            Math.sin(x);
    example:
        求一个角度的正弦值，要求x必须是一个额弧度值
        角度和弧度的转换公式：
            弧度 = 角度 * 2 * Math.PI / 360;

        Math.sin(30*2*Math.PI/360)

Math.abs(x);    得到一个数字的绝对值

```

### （Array）数组对象

```
（1）、arr1.concat(arr2);
        数组拼接，结果为将arr2拼接到arr1的最后；

（2）、arr.join()；
        数组字符串输出，括号内可以指定元素连接的符号；
        eg:
            arr=["a","b","c","d"];
            console.log(arr.join("|"));     (结果为"a|b|c|d")

（3）、arr.pop();
        切除数组的最后一个元素，返回值为该元素；

（4）、arr.slice(start,end)
        获取，获取数组的指定片段，start必须有，如果参数为负数则从末尾开始选取；
        返回值为该片段组成的，一个新的数组；

（5）、arr.push
        添加，用于向数组的末尾添加新的元素，参数可以是多个；
        返回值为数组的新长度；

（6）、arr.splice
        1、用于向数组中指定的索引添加元素；
            arr.splice(2, 0, "William","asdfasdf");
                在第2个元素开始，删除的元素个数（可以为0，为0到结尾），
                加入元素为"William"、"asdfasdf"；

        2、用于替换数组中的元素；
            arr.splice(2,1,"William")；          

        3、用于删除数组中的元素；
             arr.splice(2,2);

（7）、arr.indexOf(element);
        查找，在数组中查找element，返回值为索引，如果没有该元素返回-1；

（8）、arr.sort(function);
        排序，function为一个函数；
            eg:
                function sortNumber(a,b){
                    return a-b;
                }
                arr.sort(sortNumber);(从小到大排序)

    tips：如果a-b改成b-a，那么执行的操作为从大到小；
    tips:字符串对象（String）的方法与Array的方法类似；

```

### （Date）日期对象

```javascript
date.getTime()
date.getMilliseconds()
date.getSeconds()
date.getMinutes()
date.getHours()
date.getDay()
date.getDate()
date.getMonth()
date.getFullYear()

tips:很多，查文档

```

### （String）对象

```javascript
charAt(index)
str[index]          获取字符串指定位置的字符

concat()        拼接字符串
---------------------------
slice(start,end)/
substring(start,end)    截取从start开始，end结束的字符，
                返回一个新的字符串，若start为负数，那么从最后一个字符开始；

substr(start,length)    截取从start开始，length长度的字符，得到一个新的的字符串
---------------------------

indexOf(char)       获取指定字符第一次在字符串中的位置
lastIndexOf(char)   获取指定字符最后一次出现在字符串中的位置

trim()      去除字符串前后的空白
---------------------------
toUpperCase()
toLocaleUpperCase()     转换为大写

toLowerCase()
toLocaleLowerCawse()    转换为小写
---------------------------

replace()       替换字符
split()         分割字符串为数组

```

### 自定义对象

```
对象：无序属性的集合；
    特征：属性（key）；
    行为：方法（value）；

js是基于对象的弱类型语言；

继承：基于类，子类可以从父类得到的特征；    

工厂模式：定义一个function构造函数，作为对象，要创建对象直接调用该构造函数，加new关键字；

构造函数：定义对象的函数，里面存有该对象拥有的基本属性和方法；
    命名首字母大写，this会自动指代当前对象；

访问对象属性：
    obj[key];
    obj.key;

遍历对象：
    for(key in obj){
        key         为属性名；
        obj[key]    为属性值（value）；
    }

```

### JSON

```
{
   "name" : "李狗蛋",
   "age" : 18,
   "color" : "yellow"
}

1、  所有的属性名，必须使用双引号包起来；
2、  字面量侧重的描述对象，JSON侧重于数据传输；
3、  JSON不支持undefined；
4、  JSON不是对象，从服务器发来的json一般是字符串，
通过JSON.parse(jsonDate.json)可以将其转换成js对象；

```

### JS解析

```
（1）、作用域
全局作用域：整个代码所有地方都可以调用；
局部作用域：在函数内部声明的变量，只可以在函数内部使用；

（2）、变量提升和函数提升
预解析：在解析的时候，var和function都会被提升到代码的最顶端；
    但是赋值操作不会被提升，定义和函数才会被提升；
    if里面的变量定义也会被提升，但是赋值操作不会；

```

### 其他细节（tips）

```javascript
(1)、元素由对象组成的数组进行排序
    eg：
        var data = [
            {title: "老司机", count: 20},
            {title: "诗人", count: 5},
            {title: "歌手", count: 10},
            {title: "隔壁老王", count: 30},
            {title: "水手", count: 7},
            {title: "葫芦娃", count: 6},
        ];
            //该数组的元素都为对象。我们需求为根据count的值给数组重新排序。
            //解决方案：使用sort方法，对传入的函数做手脚。

        function sortArr(a,b){
            return a.count > b.count;   
        }
        data.sort(sortArr);

            //原本的a和b的比较方法变成a.count和b.count；
            //原本的比较方法可以参见17，数组对象
            //至此，data将以count值从小到大排列。

    tips:Array对象的sort方法传入的为比较函数，比较函数里return排序比较的方法；
        原始的sort方法传入的函数内部return的值为a>b，
        通过修改sort传入的函数，可以实现对元素为对象的数组的排序！
```

## JavaScript的内部对象

按创建方式不同分为：使用变量声明的隐性对象，使用new创建的显性对象

### 隐性对象

在赋值和声明后就是一个隐性对象，隐性对象不支持prototype属性，也无法随意扩展对象属性。

### 显性对象

显性对象支持prototype属性，支持新建对象属性。

### JavaScript提供了十一种内部对象

**Boolean对象**

Boolean对象是一种数据类型，提供构造函数可以创建布尔数据类型的对象

objboolean=new Boolean();

**Funcation对象**

JavaScript函数就是一个funcation对象，Funcation对象是函数，如果函数有参数，这些传入的参数都是argument对象

**Global对象**

Global对象不能使用new来创建，在脚本语言初始化时会自动创建此对象。

**Number对象**

Number对象，用于创建数值类型的变量

**Object对象**

使用Object对象创建自定义对象

**RegExp对象**

JavaScript的正则表达式对象

5-2JavaScript的string对象

### 创建string对象

var obj="javascript";或var obj2=new string("JavaScript");两种方式

#### string对象提供了一系列的格式编排方法

####String 对象方法

方法 描述

anchor() 创建 HTML 锚。

big() 用大号字体显示字符串。

blink() 显示闪动字符串。

bold() 使用粗体显示字符串。

fixed() 以打字机文本显示字符串。返回<tt>string</tt>中内容

fontcolor() 使用指定的颜色来显示字符串。

fontsize() 使用指定的尺寸来显示字符串。

fromCharCode() 从字符编码创建一个字符串。

link() 将字符串显示为链接。

italics() 使用斜体显示字符串。

localeCompare() 用本地特定的顺序来比较两个字符串。

slice() 提取字符串的片断，并在新的字符串中返回被提取的部分。

small() 使用小字号来显示字符串。

strike() 使用删除线来显示字符串。

sub() 把字符串显示为下标。

sup() 把字符串显示为上标。

toSource() 代表对象的源代码。

toString() 返回字符串。

valueOf() 返回某个字符串对象的原始值。


```javascript
 <script  type ="text/javascript">
        var obj = "JavaScript程序设计";
        document.write("anchor():" + obj.anchor() + "<br/>");
        document.write("big():" + obj.big() + "<br/>");
        document.write("blink():" + obj.blink() + "<br/>");
        document.write("bold():" + obj.bold() + "<br/>");
        document.write("fixed():" + obj.fixed() + "<br/>");
        document.write("fontcolor(red):" + obj.fontcolor("red") + "<br/>");
        document.write("fontsize(6):" + obj.fontsize(6) + "<br/>");
        document.write("italics()" + obj.italics() + "<br/>");
        document.write("link()" + obj.link("https://home.cnblogs.com/u/cyjy/") + "<br/>");
        document.write("small()" + obj.small() + "<br/>");
        document.write("strike():" + obj.strike() + "<br/>");
        document.write("sub():" + obj.sub() + "<br/>");
        document.write("sup():" + obj.sup() + "<br/>");
        
    </script>
```

![img](https://images2015.cnblogs.com/blog/1029626/201702/1029626-20170222144134882-756115220.png)

#### 字符串的长度与大小写

length属性是用于获取字符串的长度

toLocaleLowerCase() 把字符串转换为小写。

toLocaleUpperCase() 把字符串转换为大写。

toLowerCase() 把字符串转换为小写。

toUpperCase() 把字符串转换为大写。



```javascript
<script  type ="text/javascript">
         var obj = "JavaScript";
         var obj2 = new String("程序设计");
         document.write("英文测试字符串：" + obj  + "<br/>");
         document.write("中文测试字符串：" + obj2 + "<br/>");
         document.write("英文测试字符串length：" + obj.length + "<br/>");
         document.write("中文测试字符串length：" + obj2.length+ "<br/>");
         document.write("英文测试字符串小写：" + obj.toLowerCase() + "<br/>");
         document.write("英文测试字符串大写：" + obj.toUpperCase() + "<br/>");
    </script>
```



![img](https://images2015.cnblogs.com/blog/1029626/201702/1029626-20170222145606601-1803819854.png)

#### **获取字符串的指定字符**

charAt(index) 返回在指定位置的字符。

charCodeAt(index) 返回在指定的位置的字符的 Unicode 编码。



```javascript
<script  type ="text/javascript">
         var obj = "JavaScript";
         var obj2 = new String("程序设计");
         document.write("英文测试字符串：" + obj + "<br/>");
         document.write("中文测试字符串：" + obj2 + "<br/>");
         document.write("英文测试字符串.charAt(4)：" + obj.charAt(4) + "<br/>");
         document.write("英文测试字符串charCodeAt(4)：" + obj.charCodeAt(4) + "<br/>");
    </script>
```



![img](https://images2015.cnblogs.com/blog/1029626/201702/1029626-20170222145959945-2050085451.png)

#### **子字符串的搜索**

indexOf(string,index) 检索字符串。返回第一次找到字符串的索引位置，没有找到返回-1，传入的string是要搜索的参数，index为要搜索的索引位置

lastIndexOf(string) 从后向前搜索字符串。

match(string) 找到一个或多个正则表达式的匹配。没有找到返回null

search(string) 检索与正则表达式相匹配的值。

 



```javascript
<script  type ="text/javascript">
        var obj = "JavaScript";
        var obj2 = new String("程序设计");
        document.write("英文测试字符串indexOf('a')：" + obj.indexOf('a') + "<br/>");
        document.write("英文测试字符串indexOf('a',2)：" + obj.indexOf('a',2) + "<br/>");
        document.write("中文测试字符串.indexOf('程序')：" + obj2.indexOf('程序') + "<br/>");
        document.write("英文测试字符串.lastIndexOf('a') ：" + obj.lastIndexOf('a') + "<br/>");
        document.write("英文测试字符串.match('Java')：" + obj.match('Java') + "<br/>");
        document.write("中文测试字符串.match('程序')：" + obj2.match('程序') + "<br/>");
        document.write("英文测试字符串.search('Java')：" + obj.search('Java') + "<br/>");
        document.write("中文测试字符串.search('学校')：" + obj2.search('学校') + "<br/>");
    </script>
```

![img](https://images2015.cnblogs.com/blog/1029626/201702/1029626-20170222152320148-611445502.png)

#### 子字符串的处理

replace(string1，string2) 替换与正则表达式匹配的子串。将string1换成string2

split() 把字符串分割为字符串数组。返回数组对象。

 substr(index,length) 从起始索引号提取字符串中指定数目的字符。从index开始取出length个字符串

substring(index1，index2) 提取字符串中两个指定的索引号之间的字符。

concat(string) 连接字符串。将string字符串添加到string对象的字符串之后.



```javascript
 <script  type ="text/javascript">
        var obj = "JavaScript";
        var obj2 = new String("程序设计");
        document.write("英文测试字符串：" + obj + "<br/>");
        document.write("中文测试字符串：" + obj2 + "<br/>");
        document.write("英文测试字符串.replace('Script', '')" + obj.replace('Script', '') + "<br/>");
        document.write("中文测试字符串.split('序')" + obj2.split('序') + "<br/>");
        document.write("英文测试字符串.substr(2,4)" + obj.substr(2, 4) + "<br/>");
        document.write("英文测试字符串obj2.substring(2,5)" + obj2.substring(2, 5) + "<br/>");
    </script>
```



![img](https://images2015.cnblogs.com/blog/1029626/201702/1029626-20170222153835195-446460131.png)

将12/5/2012变为2012-5-12；

 var obj = "12/5/2012";          
​      var obj = obj.replace(/\//g,"-"); 
  var obj2=obj.replace(/(\d{2})-(\d{1}|\d{2})-(\d{4})/g,'$3-$2-$1');
​      alert(obj2); 

 

 

#### **JavaScript的Array对象**

 JavaScript数据类型中没有数组，而是使用Array对象来创建数组，每一个数组元素事实上就是Array对象的属性。

创建一维数组

 new Array();

new Array(*size*);

new Array(element0, element1, ..., elementn);



```javascript
<script  type ="text/javascript">
        var arr = new Array(1,2,3,4);
        var arr2 = new Array(3);
        arr2[0] = "one";
        arr2[1] = "two";
        arr2[2] = "three";
        //用循环显示数组值
        for (var i = 0; i <=arr.length; i++)
        {
            document.write(arr[i] + "<br/>");
        }
        for (var i = 0; i <3; i++) {
            document.write(arr2[i] + "<br/>");
        }
    </script>
```



![img](https://images2015.cnblogs.com/blog/1029626/201702/1029626-20170222155826616-1708664414.png)

 

### **Array对象的属性与方法**

length属性获取数组长度

方法 描述
concat(arry)	连接两个或更多的数组，并返回结果。将参数合并到当前的数组中
join()	把数组的所有元素放入一个字符串。元素通过指定的分隔符进行分隔。

pop() 删除并返回数组的最后一个元素
push()	向数组的末尾添加一个或更多元素，并返回新的长度。
shift()	删除并返回数组的第一个元素
slice()	从某个已有的数组返回选定的元素
splice()	删除元素，并向数组添加新元素。
toSource()	返回该对象的源代码。
toString()	把数组转换为字符串，并返回结果。
reverse() 颠倒数组中元素的顺序。*sort*(arry)将数组的所有元素排序**

```javascript
<script  type ="text/javascript">
        var arr = new Array(1,2,3,4);
        var arr2 = new Array(3);
        arr2[0] = "one";
        arr2[1] = "two";
        arr2[2] = "three";
        function showarr(arr) {
            for (var i = 0; i < arr.length; i++) {
                document.write(arr[i] + ",");

            }
        }
        document.write("数组长" + arr2.length + "<br/>");
        document.write(arr2.join() + "<br/>");
        arr2.reverse();//反转数组
        document.write("<br/>");
        showarr(arr);
        document.write("<br/>");
        arr = arr.concat(arr2);//连接两个数组
        showarr(arr);
    </script>
```



![img](https://images2015.cnblogs.com/blog/1029626/201702/1029626-20170222162535820-755406468.png)

**JavaScript的多维数组**



```javascript
<script  type ="text/javascript">
          var arr = new Array(3);
          for (var i = 0; i < 3; i++)
          {
              arr[i] = new Array(2);
          }
          arr[0][0] = "1";
          arr[0][1] = "2";
          arr[1][0] = "3";
          arr[1][1] = "4";
          arr[2][0] = "5";
          arr[2][1] = "6";
          for (var j= 0; j< arr.length;j++)
          {
              for (i = 0; i < arr[i].length; i++) {
                  document.write(arr[j][i]);
              }
                  document.write("<br/>");
              
          }
    </script>
```



![img](https://images2015.cnblogs.com/blog/1029626/201702/1029626-20170222163949507-2080988031.png)

#### **JavaScript的Date对象**

Date对象可以获取计算机的系统时间和日期，并且提供相关的方法将它转化为所需的日期数据。

创建Date对象

var youDate=new Date()

Date 对象会自动把当前日期和时间保存为其初始值。

Date() 返回当日的日期和时间。
getDate()	从 Date 对象返回一个月中的某一天 (1 ~ 31)。
getDay()	从 Date 对象返回一周中的某一天 (0 ~ 6)，也就是星期日到星期六。
getMonth()	从 Date 对象返回月份 (0 ~ 11)。
getFullYear()	从 Date 对象以四位数字返回年份。
getYear()	请使用 getFullYear() 方法代替。
getHours()	返回 Date 对象的小时 (0 ~ 23)。
getMinutes()	返回 Date 对象的分钟 (0 ~ 59)。
getSeconds()	返回 Date 对象的秒数 (0 ~ 59)。
getMilliseconds()	返回 Date 对象的毫秒(0 ~ 999)。
getTime()	返回 1970 年 1 月 1 日至今的毫秒数。

```javascript
<script  type ="text/javascript">
        var youDate = new Date();
        document.write("系统日期：" + youDate.getDate());
        document.write("<br/>");
        document.write("系统时间：" + youDate.getHours() + ":" + youDate.getMinutes()+":"+youDate.getSeconds());
    </script>
```

![img](https://images2015.cnblogs.com/blog/1029626/201702/1029626-20170222165139788-1213666040.png)

**设置时间和日期**

setDate() 设置 Date 对象中月的某一天 (1 ~ 31)。
setMonth()	设置 Date 对象中月份 (0 ~ 11)。
setFullYear()	设置 Date 对象中的年份（四位数字）。
setYear()	请使用 setFullYear() 方法代替。
setHours()	设置 Date 对象中的小时 (0 ~ 23)。
setMinutes()	设置 Date 对象中的分钟 (0 ~ 59)。
setSeconds()	设置 Date 对象中的秒钟 (0 ~ 59)。
setMilliseconds()	设置 Date 对象中的毫秒 (0 ~ 999)。
setTime()	以毫秒设置 Date 对象。

 JavaScript的Date对象可以获取系统时间，只需定时执行JavaScript函数就可以建立一个网页时钟，同时需要使用setTimeout（），参数中可以设置间隔多少时间来执行函数，clearTimeout（）可以清除定时器

#### **JavaScript的Math对象**

Math对象不同于其他JavaScript对象，Math对象是由脚本语言引擎所创建的，不需要使用new来创建。

max(x,y) 返回 x 和 y 中的最高值。
min(x,y)	返回 x 和 y 中的最低值。
pow(x,y)	返回 x 的 y 次幂。
random()	返回 0 ~ 1 之间的随机数。

需要获得更大的随机数乘以相关的倍数就可以了

```javascript
<script  type ="text/javascript">
        var num = Math.round(Math.random() * 100);
        document.write("0~100之间的随机数"+num);
    </script>
```

![img](https://images2015.cnblogs.com/blog/1029626/201702/1029626-20170222171339913-1190372691.png)

**JavaScript的Error对象**

try catch finally处理例外。

**JavaScript对象的共享属性和方法**

属性 constructor属性可以获取创建对象使用的构造函数的函数名称

tostring（）方法和value（）可以显示对象的内容。



## js中的DOM操作汇总

### **一、DOM创建**

DOM节点（Node）通常对应于一个标签，一个文本，或者一个HTML属性。DOM节点有一个`nodeType`属性用来表示当前元素的类型，它是一个整数：

1. Element，元素
2. Attribute，属性
3. Text，文本

DOM节点创建最常用的便是document.createElement和`document.createTextNode`方法：

```javascript
var node1 = document.createElement('div');
var node2 = document.createTextNode('hello world!');
```

### **二、DOM查询**

```javascript
// 返回当前文档中第一个类名为 "myclass" 的元素
var el = document.querySelector(".myclass");

// 返回一个文档中所有的class为"note"或者 "alert"的div元素
var els = document.querySelectorAll("div.note, div.alert");

// 获取元素
var el = document.getElementById('xxx');
var els = document.getElementsByClassName('highlight');
var els = document.getElementsByTagName('td');
Element也提供了很多相对于元素的DOM导航方法：

// 获取父元素、父节点
var parent = ele.parentElement;
var parent = ele.parentNode;//只读，没有兼容性问题
var offsetParent=ele.offsetParent;//只读，找到最近的有定位的父节点。
　　　　　　　　　　　　　　　　　　　　　//没有定位父级时，默认是body;但在IE7以下，如果当前元素没有定位属性，返回body，如果有，返回HTML;
　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　 //如果当前元素某个父级触发了haslayout，则返回触发了haslayout这个元素。

// 获取子节点，子节点可以是任何一种节点，可以通过nodeType来判断
var nodes = ele.children;//标准下、非标准下都只含元素类型，但对待非法嵌套的子节点，处理方式与childNodes一致。   
var nodes = ele.childNodes;//非标准下：只包含元素类型，不会包含非法嵌套的子节点。
　　　　　　　　　　　　　　　　//标准下：包含元素和文本类型，会包含非法嵌套的子节点。 

//获取元素属性列表
var attr = ele.attributes;

// 查询子元素
var els = ele.getElementsByTagName('td');
var els = ele.getElementsByClassName('highlight');

// 当前元素的第一个/最后一个子元素节点
var el = ele.firstChild;//对待标准和非标准模式，如childNods
var el = ele.lastChild;
var el = ele.firstElementChild;//非标准不支持
var el = ele.lastElementChild;
// 下一个/上一个兄弟元素节点
var el = ele.nextSibling;
var el = ele.previousSibling;
var el = ele.nextElementSibling;
var el = ele.previousElementSibling;
```


**兼容的获取第一个子元素节点方法：**

var first=ele.firstElementChild||ele.children[0];

### **三、DOM更改**



```javascript
// 添加、删除子元素
ele.appendChild(el);
ele.removeChild(el);

// 替换子元素
ele.replaceChild(el1, el2);

// 插入子元素
parentElement.insertBefore(newElement, referenceElement);

//克隆元素
ele.cloneNode(true) //该参数指示被复制的节点是否包括原节点的所有属性和子节点
```

### **四、属性操作**



```javascript
// 获取一个{name, value}的数组
var attrs = el.attributes;

// 获取、设置属性
var c = el.getAttribute('class');
el.setAttribute('class', 'highlight');

// 判断、移除属性
el.hasAttribute('class');
el.removeAttribute('class');

// 是否有属性设置
el.hasAttributes();     
```

### innerHTML与outerHTML的区别？

 比如对于这样一个HTML元素：`<div>content<br/></div>`。

- `innerHTML`：内部HTML，`content<br/>`；
- `outerHTML`：外部HTML，`<div>content<br/></div>`；
- `innerText`：内部文本，`content`；
- `outerText`：内部文本，`content`；

上述四个属性不仅可以读取，还可以赋值。`outerText`和`innerText`的区别在于`outerText`赋值时会把标签一起赋值掉，另外`xxText`赋值时HTML特殊字符会被转义。 下图来源于：<http://walsh.iteye.com/blog/261966>

![img](http://harttle.com/assets/img/blog/javascript/dom-content.gif)

### jQuery的html()与innerHTML的区别？

jQuery的`.html()`会调用`.innerHTML`来操作，但同时也会`catch`异常，然后用`.empty()`, `.append()`来重新操作。 这是因为IE8中有些元素(比如input等)的`.innerHTML`是只读的。见：<http://stackoverflow.com/questions/3563107/jquery-html-vs-innerhtml>

**引用资料：**

http://stackoverflow.com/questions/3563107/jquery-html-vs-innerhtml

http://walsh.iteye.com/blog/261966

http://harttle.com/2015/10/01/javascript-dom-api.html

## javascript中的BOM

浏览器对象模型BOM，提供了访问浏览器的接口。这些功能大多和网页内容无关，多年来，由于缺乏规范导致BOM中的不同方法在不同浏览器中的实现有所差异，直到html5，才将BOM的主要方面纳入规范。

BOM常用的特性包括：

### **一、window对象**

window对象在浏览器中具有双重角色：它既是ECMAscript规定的全局global对象，又是javascript访问浏览器窗口的一个接口。

1. 获取窗口相对于屏幕左上角的位置

```javascript
//获取窗口相对于屏幕左上角的位置
var leftPos=(typeof window.screenLeft==='number')?window.screenLeft:window:screenX;
var topPos=(typeof window.screenLeft==='number')?window.screenTop:window:screenY;
```

需要注意的一点是，在IE，opera中，screenTop保存的是页面可见区域距离屏幕左侧的距离，而chrome,firefox,safari中，screenTop/screenY保存的则是整个浏览器区域距离屏幕左侧的距离。也就是说，二者差了一个浏览器工具栏的像素高度。

2. 移动窗口，调整窗口大小

```javascript
window.moveTo(0,0)
window.moveBy(20,10)
window.resizeTo(100,100);
window.resizeBy(100,100);
```

注意，这几个方法在浏览器中很可能会被禁用。

3. 获得浏览器页面视口的大小

```javascript
var pageWith=document.documentElement.clientWidth||document.body.clientWidth;
var pageHeight=document.documentElement.clientHeight||document.body.clientHeight;
```

4. 导航和打开窗口

window.open()既可以导航到特定的URL，也可以打开一个新的浏览器窗口，其接收四个参数，要加载的url，窗口目标（可以是关键字_self,_parent,_top,_blank）,一个特性字符串，以及一个表示新页面是否取代浏览器历史记录中当前加载页面的布尔值。通常只需要传递第一个参数。

注意，在很多浏览器中，都是阻止弹出窗口的。

5. 几个时序相关的函数（原来这是BOM的实现，而非ECMAjavascript的实现）

setInterval()

setTimeout()

6. 系统对话框，这些对话框外观由操作系统/浏览器设置决定，css不起作用，所以很多时候可能需要自定义对话框

alert():带有一个确定按钮

confirm()：带有一个确定和取消按钮

prompt():显示OK和Cancel按钮之外，还会显示一个文本输入域

 

### **二、location对象**

location对象提供了当前窗口加载的文档的相关信息，还提供了一些导航功能。事实上，这是一个很特殊的对象，location既是window对象的属性，又是document对象的属性。

```javascript
location.hash　　#contents　　返回url中的hash，如果不包含#后面的内容，则返回空字符串

location.host　　www.wrox.com:80　　返回服务器名称和端口号

location.port　　80　　返回端口号

location.hostname　　www.wrox.com　　返回服务器名称

location.href　　http://www.wrox.com　　返回当前加载页面的完整url

location.pathname　　/index.html　　返回url中的目录和文件名

location.protocol http　　返回页面使用的协议

location.search　　?q=javascript　　返回url中的查询字符串
```

改变浏览器的位置：

```javascript
location.href=http://www.baidu.com

location.replace('http://www.baidu.com')//不会在历史记录中生成新纪录，用户不能回到前一个页面。

location.reload()：//重置当前页面，可能从缓存，也可能从服务器；如果强制从服务器取得，传入true参数

```



### **三，navigator对象**

这个对象代表浏览器实例，其属性很多，但常用的不太多。如下：

```javascript
navigator.userAgent：//用户代理字符串，用于浏览器监测中、

navigator.plugins://浏览器插件数组，用于插件监测

navigator.registerContentHandler //注册处理程序，如提供RSS阅读器等在线处理程序。
```



### **四、history对象**

history对象保存着用户上网的历史记录，使用go()实现在用户的浏览记录中跳转：

```javascript
history.go(-1) 等价于history.back()
history.go(1) 等价于 history.forward()
history.go(1) //前进两页
history.go('wrox.com')
```

## JS函数的几种定义方式分析

本文实例讲述了JS函数的几种定义方式。分享给大家供大家参考，具体如下：

JS函数的定义方式比较灵活，它不同于其他的语言，每个函数都是作为一个对象被维护和运行的。

先看几种常用的定义方式：

```js
function func1([参数]){
    /*函数体*/
}
var func2=function([参数]){
    /*函数体*/
};
var func3=function func4([参数]){
    /*函数体*/
};
var func5=new Function();
```

上述

- 第一种方式是最常用的方式，不用多说。
- 第二种是将一匿名函数赋给一个变量，调用方法：func2([函数]);
- 第三种是将func4赋给变量func3，调用方法：func3([函数]);或func4([函数]);
- 第四种是声明func5为一个对象。

再看看它们的区别：

```javascript
function func(){
  //函数体
}
//等价于
var func=function(){
  //函数体
}
```

但同样是定义函数，在用法上有一定的区别。

```js
<script>
//这样是正确的
func(1);
function func(a)
{
  alert(a);
}
</script>
```

```js
<script>
//这样是错误的，会提示func未定义，主要是在调用func之前没有定义
func(1);
var func = function(a)
{
  alert(a);
}
//这样是正确的，在调用func之前有定义
var func = function(a)
{
  alert(a);
}
func(1);
</script>
```

用同样的方法可以去理解第三种定义方式。

第四种定义方式也是需要声明对象后才可以引用。

### JS定义（声明）函数的几种方式

#### 1. 函数式声明

```js
function funName(arg1, arg2)
{
    alert(arg1 + “,” + arg2);  
}
```

特点：此种方式可定义命名的函数变量，而无需给变量赋值，这是一种独立的结构，不能嵌套在非功能模块中。函数名在自身作用域和父作用域内是可获取的（其他域是娶不到的）。当解析器读取js代码时，会先读取函数的声明，此种方式定义的函数在执行任何代码之前都可以访问（调用）。

#### **2、 函数表达式（函数字面量）**

```js
var fun = function(arg1, arg2){
        alert(arg1 + “,” + arg2);
}

var fun = function funName(arg1,arg2){
    alert(arg1 + “,” + arg2);
}

(function hello(){
        alert(“HelloWorld!!!”);
})(); // 自调用
```

特点：地中方式是将函数定义为表达式语句的一部分。函数可以是命名的也可以是匿名的。而且必须等到解析器执行到它所在的代码行才能真正被解释执行。

#### 3、 函数构造法，参数必须加引号

```js
var fun = new Function(‘arg1’, ‘arg2’, ‘alert(arg1 + “,” + arg2)’);
```

特点：从技术角度讲，这是一个函数表达式。但是一般不推荐这种方式。

#### **4、 对象直接量**

```js
var obj = {
        name : “”,  
        getName : function(){
                	return this.name;
							},
		setName : function(name){
        			this.name = name;
								}
		};
```

特点：将方法的定义看做为一个对象的成员变量，此时对象的变量值为一个方法，通过访问该对象的属性名称，达到调用方法的效果。

#### **5、 原型继承**

```js
var obj = new Function();
obj.prototype = {
        name : “”,   
        getName : function(){   
                return this.name;
							},
		setName : function(name){
       			 this.name = name;
								}
};
```

特点：定义了一个函数对象，在其原型对象中定义方法。在使用prototype的方法时，必须实例化该对象才能调用其方法。

#### **6、 工厂模式**

```js
function obj(){
	var temp = new Object();
	temp.name = “”;
	temp.getName = function(){
       			 return this.name;
							};
	temp.setName(name){
       			this.name = name;
					};
		return temp;
			}
```

特点：工厂模式是软件工程领域一种广为人知的设计模式，由于在ECMAScript中无法创建类，因此用函数封装以特定接口创建对象。即在一个函数内创建一个对象，给对象赋予属性和方法再将其对象返回。

以上介绍的几种js中创建方法常用的方式，根据其特点和理解可以根据实际情况使用不同的方式声明函数。

## js全局函数

### parseInt(String,radix):返回转换成整数的值。

　　 注意：当参数radix的值为0，或者没有设置这个参数，parseInt()会根据string来判断数字的基数。

　　　　　当忽略radix，JavaScript默认数字的基数规则为：

1. 如果string以0x开头，parseInt()会把string的其余部分解析为十六进制的整数。
2. 如果string以0开头，那么ECMAScript v3允许parseInt()的一个实现把其后的字符解析为八进制或十六进制的数。
3. 如果string以1~9的数字开头，parseInt()将把它解析为十进制的整数。
4. 如果字符串以合法字符开始，parseInt()会截取合法字符。
5. String开头和结尾的空格是允许的。
6. 如果字符串的第一个字符不能被转换为数字，parseInt()会返回NaN。


7. 在字符串以"0"为开始时旧的浏览器默认使用八进制基数。ECMAScript 5，默认的是十进制的基数。

### parseFloat(string):返回转换成浮点型的值。

　　该函数指定字符串中的首个字符是否是数字。如果是，则对字符串进行解析，直到到达数字的末端为止，然后以数字返回该数字，而不是作为字符串。

### isFinite(value):检测某个是是否是无穷值。

　　如果value是NaN或者+Infinity或者-Infinity的数，isFinite()返回false。

### isNaN(value):检测某个值是否是NaN。

　　isNaN()检测是否是非数字值，如果值为NaN返回true，否则返回false。

### encodeURI(uri):将字符串编码为URI。

　　使用 decodeURI() 方法可以编码URI（通用资源标识符:Uniform Resource Identifier,简称"URI")。

　　对以下在 URI 中具有特殊含义的 ASCII 标点符号，encodeURI() 函数是不会进行转义的： , / ? : @ & = + $ #

　　(可以使用 encodeURIComponent() 方法分别对特殊含义的 ASCII 标点符号进行编码。)

### decodeURI(uri):解码某个编码的URI。

### encodeURIComponent(uri):将字符串编码为URI组件

　　该方法不会对 ASCII 字母和数字进行编码，也不会对这些 ASCII 标点符号进行编码： - _ . ! ~ * ' ( ) 。

　　其他字符（比如 ：;/?:@&=+$,# 这些用于分隔 URI 组件的标点符号），将由一个或多个十六进制的转义序列替换的。

### decodeURIComponent():解码一个编码的URI组件

### escape():对字符串进行编码

　　该函数可对字符串进行编码，这样就可以在所有的计算机上读取该字符串。

　　该方法不会对 ASCII 字母和数字进行编码，也不会对下面这些 ASCII 标点符号进行编码： * @ - _ + . / 。其他所有的字符都会被转义序列替换。

　　 该函数不能用于编码 URIs(通用资源标识符（UniformResourceIdentifier,简称"URI"））

### unescape():解码由escape()编码的字符串

### eval():将JavaScript字符串当作脚本来执行

　　如果参数是一个表达式，eval() 函数将执行表达式。如果参数是Javascript语句，eval()将执行 Javascript 语句。

　　eval()函数是一种由函数执行的动态代码，比直接执行脚本慢很多。

　　慎重使用eval()函数,尽量不用，保证程序的安全性。

### Number(object):把对象的值转换为数字

　　如果对象的值无法转换为数字，Number()函数返回NaN。

　　如果参数的Date对象，Number()返回从1970年1月1日到现在所经历的毫秒数

### String():把对象的值转换为字符串

　　String() 函数返回与字符串对象的toString()方法值一样。

### 顶层函数（全局函数）

| 函数                                                         | 描述                                               |
| ------------------------------------------------------------ | -------------------------------------------------- |
| [decodeURI()](http://www.w3school.com.cn/jsref/jsref_decodeURI.asp) | 解码某个编码的 URI。                               |
| [decodeURIComponent()](http://www.w3school.com.cn/jsref/jsref_decodeURIComponent.asp) | 解码一个编码的 URI 组件。                          |
| [encodeURI()](http://www.w3school.com.cn/jsref/jsref_encodeuri.asp) | 把字符串编码为 URI。                               |
| [encodeURIComponent()](http://www.w3school.com.cn/jsref/jsref_encodeURIComponent.asp) | 把字符串编码为 URI 组件。                          |
| [escape()](http://www.w3school.com.cn/jsref/jsref_escape.asp) | 对字符串进行编码。                                 |
| [eval()](http://www.w3school.com.cn/jsref/jsref_eval.asp)    | 计算 JavaScript 字符串，并把它作为脚本代码来执行。 |
| [getClass()](http://www.w3school.com.cn/jsref/jsref_getClass.asp) | 返回一个 JavaObject 的 JavaClass。                 |
| [isFinite()](http://www.w3school.com.cn/jsref/jsref_isFinite.asp) | 检查某个值是否为有穷大的数。                       |
| [isNaN()](http://www.w3school.com.cn/jsref/jsref_isNaN.asp)  | 检查某个值是否是数字。                             |
| [Number()](http://www.w3school.com.cn/jsref/jsref_number.asp) | 把对象的值转换为数字。                             |
| [parseFloat()](http://www.w3school.com.cn/jsref/jsref_parseFloat.asp) | 解析一个字符串并返回一个浮点数。                   |
| [parseInt()](http://www.w3school.com.cn/jsref/jsref_parseInt.asp) | 解析一个字符串并返回一个整数。                     |
| [String()](http://www.w3school.com.cn/jsref/jsref_string.asp) | 把对象的值转换为字符串。                           |
| [unescape()](http://www.w3school.com.cn/jsref/jsref_unescape.asp) | 对由 escape() 编码的字符串进行解码。               |

### 顶层属性（全局属性）

| 方法                                                         | 描述                                   |
| ------------------------------------------------------------ | -------------------------------------- |
| [Infinity](http://www.w3school.com.cn/jsref/jsref_infinity.asp) | 代表正的无穷大的数值。                 |
| [java](http://www.w3school.com.cn/jsref/jsref_java.asp)      | 代表 java.* 包层级的一个 JavaPackage。 |
| [NaN](http://www.w3school.com.cn/jsref/jsref_nan.asp)        | 指示某个值是不是数字值。               |
| [Packages](http://www.w3school.com.cn/jsref/jsref_Packages.asp) | 根 JavaPackage 对象。                  |
| [undefined](http://www.w3school.com.cn/jsref/jsref_undefined.asp) | 指示未定义的值。                       |

### 全局对象描述

全局对象是预定义的对象，作为 JavaScript 的全局函数和全局属性的占位符。通过使用全局对象，可以访问所有其他所有预定义的对象、函数和属性。全局对象不是任何对象的属性，所以它没有名称。

在顶层 JavaScript 代码中，可以用关键字 this 引用全局对象。但通常不必用这种方式引用全局对象，因为全局对象是作用域链的头，这意味着所有非限定性的变量和函数名都会作为该对象的属性来查询。例如，当JavaScript 代码引用 parseInt() 函数时，它引用的是全局对象的 parseInt 属性。全局对象是作用域链的头，还意味着在顶层 JavaScript 代码中声明的所有变量都将成为全局对象的属性。

全局对象只是一个对象，而不是类。既没有构造函数，也无法实例化一个新的全局对象。

在 JavaScript 代码嵌入一个特殊环境中时，全局对象通常具有环境特定的属性。实际上，ECMAScript 标准没有规定全局对象的类型，JavaScript 的实现或嵌入的 JavaScript 都可以把任意类型的对象作为全局对象，只要该对象定义了这里列出的基本属性和函数。例如，在允许通过 LiveConnect 或相关的技术来脚本化 Java 的 JavaScript 实现中，全局对象被赋予了这里列出的 java 和 Package 属性以及 getClass() 方法。而在客户端 JavaScript 中，全局对象就是 Window 对象，表示允许 JavaScript 代码的 Web 浏览器窗口。

## JavaScript 事件

**事件是可以被 JavaScript 侦测到的行为。**

JavaScript 使我们有能力创建动态页面。事件是可以被 JavaScript 侦测到的行为。

网页中的每个元素都可以产生某些可以触发 JavaScript 函数的事件。比方说，我们可以在用户点击某按钮时产生一个 onClick 事件来触发某个函数。事件在 HTML 页面中定义。

### 事件举例：

- 鼠标点击
- 页面或图像载入
- 鼠标悬浮于页面的某个热点之上
- 在表单中选取输入框
- 确认表单
- 键盘按键

注意：事件通常与函数配合使用，当事件发生时函数才会执行。

## onload 和 onUnload

当用户进入或离开页面时就会触发 onload 和 onUnload 事件。

onload 事件常用来检测访问者的浏览器类型和版本，然后根据这些信息载入特定版本的网页。

onload 和 onUnload 事件也常被用来处理用户进入或离开页面时所建立的 cookies。例如，当某用户第一次进入页面时，你可以使用消息框来询问用户的姓名。姓名会保存在 cookie 中。当用户再次进入这个页面时，你可以使用另一个消息框来和这个用户打招呼："Welcome John Doe!"。

## onFocus, onBlur 和 onChange

onFocus、onBlur 和 onChange 事件通常相互配合用来验证表单。

下面是一个使用 onChange 事件的例子。用户一旦改变了域的内容，checkEmail() 函数就会被调用。

```
<input type="text" size="30" id="email" onchange="checkEmail()">

```

## onSubmit

onSubmit 用于在提交表单之前验证所有的表单域。

下面是一个使用 onSubmit 事件的例子。当用户单击表单中的确认按钮时，checkForm() 函数就会被调用。假若域的值无效，此次提交就会被取消。checkForm() 函数的返回值是 true 或者 false。如果返回值为true，则提交表单，反之取消提交。

```
<form method="post" action="xxx.htm" onsubmit="return checkForm()">
```

## onMouseOver 和 onMouseOut

onMouseOver 和 onMouseOut 用来创建“动态的”按钮。

下面是一个使用 onMouseOver 事件的例子。当 onMouseOver 事件被脚本侦测到时，就会弹出一个警告框：

```
<a href="http://www.w3school.com.cn"
onmouseover="alert('An onMouseOver event');return false">

<img src="w3school.gif" width="100" height="30">

</a>
```
