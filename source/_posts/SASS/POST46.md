---
title: SASS是什么
tags: SASS
category: SASS
date: 2018-01-24 15:44:36
---
![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0104.jpg)

*SASS* 是 CSS 的一个预处理工具，简单来说，它通过自己的一套语法规则来写源码，然后通过编译器，得到 CSS 代码。
<!--more-->

# 1. SASS是什么

为什么需要这东西呢？因为如果每天都要写很多的 CSS ，那么里面重复的内容会很多，同时，CSS 本身并没有相应的逻辑抽像工具，这就造成在项目中要对样式做有效的管理，似乎没有什么好办法了。

*SASS* 本身是来源于 Ruby 的一个项目，它目前的标准，应该是一个叫 *libSass* 的 C 语言库，有了这个 C 语言的实现之后，各种语言就可以很方便地 bind 过去了，从官网上的信息看，目前 C, Go, Java, Javascript, Lua, .NET, Node, Perl, PHP, Python, Ruby, Scala 这些语言上都有实现。

*SASS* 支持两种格式的源码写法，一种叫 SASS ，另一种叫 SCSS ，区别只是前者使用缩进，后者使用大括号。（我就不明白了，为什么有人喜欢大括号，你写了大括号不也要缩进么）

# 2. 安装

这里，使用 Node 上的实现，叫 **node-sass** ：

```
sudo npm install -g node-sass

```

安装之后，除了相应的 node 模块，还会有一个命令行可执行程序，叫 `node-sass` ：

```
node-sass a.sass

```

通过 `--output-style` 参数可以控制输出的 CSS 的格式，我喜欢用：

```
node-sass --output-style compact a.sass

```

这样输出的 CSS ，是一行一条。（以前喜欢这种形式，是因为一行一条的格式，可以方便地在编辑器中使用列编辑作批量修改）

# 3. 变量, 赋值, 数据类型

SASS 中使用 *$* 开头来标识一个变量名，使用 *:* 完成赋值的操作：

```
$a: 123

```

数据类型有以下几种：

- *数字*

  `123` , `1.2` , `12px` , `12%`, 这些是数字类型。比较特殊的一点，可能在于， SASS 把带有 CSS 单位的标识也作为数字处理，在后面的运算符行为时，单位部分也会一并处理。


- *字符串*

  `abc` 或 `"abc"` 或 `'abc'` 。简单来说，单引号，双引号都可以，不要引号也可以。但是，因为 CSS 中大部分的描述都是不要引号的形式，比如 `color: red` ，而不是 `color: 'red'` ，所以，在引号的处理上，要小心。


- *颜色*

  `red` , `#abcd` , `rgb(123,255,67)` , `rgba(123, 255, 67, 0.5)` , CSS 中表示颜色的形式，在 SASS 中作为颜色类型处理。这里注意，第一个 `red` ，跟字符串的形式一样的。（我猜这类标识具有混合属性吧）


- *列表*

  `10px 2px 0 0` 或 `Helvtica, Arial, sans-serif` ，以空格，或者以逗号分割的数据形式，为列表。主要对应于 CSS 中的对应形式，比如 `margin: 10px 2px 0 0` ， `font-family: Helvtica, Arial, sans-serif` 。 同时，列表这种类型，在处理函数的参数时也有用。


- *映射*

  `(k1: v1, k2: v2)` ，映射其实就是一个嵌套的列表，形如 `(k1 v1), (k2 v2)` ，主要用于迭代的一些控制结构中。

# 4. 变量符号化

这里的“符号化”说法，意思就是“行内求值”。简单来说，就是把变量的值，在任何地方，转换为语法结构的一部分（因为按语法规则，“这里”是不能使用变量的）。举一个例子：

```
$a cls

.$a:
    color: red

```

这肯定是错误的。因为 “规则” / “选择器” 部分，是不允许使用变量的。但是你可以：

```
$a cls

.#{$a}
    color: red

```

这样，就是正确的了，编译出来是：

```
.cls { color: red; }

```

同时，还有：

```
$a: 'red'

.cls
    color: $a

```

这样写，虽然编译没有问题，但是结果：

```
.cls { color: 'red'; }

```

这是不对的。换成：

```
$a: 'red'

.cls
    color: #{$a}

```

就可以正确得到：

```
.cls { color: red; }

```

所以， **#{}** 的功能，算是非常有用且强大。

# 5. 嵌套规则与引用

CSS 简单来说，只有两部分内容，一是规则，或者说选择器。二是属性描述。DOM 本身是一个层级结构，所以我们在书写规则时，也是层级下降的方式，这样，如果直接写 CSS ，就不可避免地要写很多重复的前置规则，比如：

```
.project > .header > .title { color: red; }
.project > .header > .content { color: blue; }

```

SASS 最直观的一个功能，就是可以以一种嵌套的层级形式，来处理规则的书写：

```
.project
    > .header
        > .title
            color: red
        > .content
            color: blue

```

这样不光可以少写很多代码，而且最重要的，是容易维护。

这种嵌套的层级结构，有一个特别的功能，就是使用 **&** 可以作为当前选择的引用，这个功能，一般用于伪类：

```
.project
  > .header
    > .title
      color: red
      
      > a
        color: yellow
        
        &:hover
          text-decoration: none

```

编译得到：

```
.project > .header > .title { color: red; }
.project > .header > .title > a { color: yellow; }
.project > .header > .title > a:hover { text-decoration: none; }

```

# 6. 运算符, 逻辑判断, 控制结构

SASS 虽然不是完整的一门编程语言，但是，运算符和控制结构的概念还是有的。

这里先说一下， **SASS 中可以使用 @debug 作标准输出。**

```
$a: 1px + 1px
$b: 1px * 20
$c: 10px - 2px
$d: 10px / 2
$e: 10 + 2 - 3 + 0px
$e2: 10 + 2 - 3 + 0%
$e3: (10 + 2) * 3 / 100 * 100%

@debug $a, $b, $c, $d, $e, $e2, $e3

```

四则运算时，单位需要匹配，或者，你也可以在最后再处理单位。

控制结构方面，目前有 *@for*, *@each*, *@while*, *@if* 。

- *@for*

  单纯的数字范围的迭代：`@for $i from 1 through 10  @debug $i`


- *@each*

  对列表的迭代：`@each $i in 1,2,3,4  @debug $i`支持多值匹配：`@each $a, $b, $c in (a, b, c), (red, 1, blue)  @debug $a, $b, $c`

- *@while*

  `$i: 0@while $i < 10  @debug $i  $i: $i + 1`


- *@if*

  这里随便把逻辑判断一起用了：`$i: 0@while $i < 10  @if $i == 0    @debug zero  @if $i > 2    @debug gtwo  @else    @debug ltwo  @if $i != 0    @debug other  $i: $i + 1`

# 7. 函数定义

先说清楚这里的函数定义，传入参数，返回值，返回值。

```
@function add($a, $b)
  @return $a + $b

@debug add(1, 2)

```

函数的参数，跟 Python 一样，可以有默认值，可以有关键词参数：

默认值：

```
@function add($a, $b:3)
  @return $a + $b

@debug add(1)

```

关键词参数：

```
@function add($a:2, $b:3)
  @return $a + $b

@debug add($b:1)

```

# 8. @mixin, 宏

*Mixin* 是官方的叫的名字， *宏* 是我自己叫的，因为，这东西的行为就像“宏”一样，可以生成代码片段。

*Mixin* 是 SASS 中地位很重要，还特别给它了一种简写形式。

```
@mixin em
  color: red

.header.em
  @include em

```

简写形式：

```
=em
  color: red

.header.em
  +em

```

生成：

```
.header.em { color: red; }

```

Mixin 也可以带参数：

```
=em-back($color:yellow, $font-size: 14px)
  color: red
  background-color: $color
  font-size: $font-size


.header.true
  +em-back($font-size: 20px)

```

生成：

```
.header.true { color: red; background-color: yellow; font-size: 20px; }

```

还可以直接传入代码块：

```
=em
  color: red
  @content


.header.true
  +em
    font-size: 14px

```

生成：

```
.header.true { color: red; font-size: 14px; }

```

注意， *Mixin* 的结果是代码块，所以，它不光可以返回属性块，还可以连带规则块一起处理：

```
=em
  span
    color: red

.header.true
  +em

```

生成：

```
.header.true span { color: red; }

```

引用 *&* 也可以照常使用：

```
=hover
  &:hover
    @content

.header.true
  color: white

  +hover
    color: red

```

生成：

```
.header.true { color: white; }
.header.true:hover { color: red; }

```

配合 *@at-root* 和 *&* ：

```
=hover
  @at-root
    &
      @content

.header.true
  color: white

  +hover
    color: red

```

生成：

```
.header.true { color: white; }
.header.true { color: red; }

```

*Mixin* 本身是可以嵌套的：

```
=em
  color: red

=hover
  +em
  @content

.header.true
  +hover
    background-color: aqua

```

生成：

```
.header.true { color: red; background-color: aqua; }

```

# 9. @extend 续写

前面说的 *@mixin* 是处理抽象的可复用代码，现在说的 *@extend* 是处理具体的已存在的可复用代码。其实两者的机制都是 Mixin ，这里说的 “续写” 是我自己叫的一个名字，因为我实在不想叫“继承”。

## 9.1. 最简单情况

*@extend* 的作用，是通过指定标识符，在当前块中引用对应的属性定义。

```
.a
  color: red

.b
  @extend .a
  background-color: blue

```

生成：

```
.a, .b { color: red; }
.b { background-color: blue; }

```

这里，你也可以看成是生成了：

```
.a { color: red; }
.b { color: red; }
.b { background-color: blue; }

```

或者：

```
.a { color: red; }
.b { color: red; background-color: blue; }

```

这里的行为，可以注意一点，用 **@extend 的块自己本身一定还有一条定义** ，这里的就是最前面的 `.b` 有自己单独的一条。如果 `.b` 中没有 `background-color` ，也可以看成是 `.b` 会有两条相同的，仅有 `color` 的属性定义。

## 9.2. 复合规则

当然，这是最简单的情况，复杂一点的：

```
.g.a
  color: red

.b
  @extend .a
  background-color: blue

```

生成的是：

```
.g.a, .g.b { color: red; }
.b { background-color: blue; }

```

这里的行为，可以总结为， **@extend 部分，不会更改原有的规则限制**。这里就是 `.a` 有 `.g` 这个限制，那么引用 `.a` 的 `.b` ，在引用部分，也有 `.g` 的限制。

把 `.a` 换成 `.g` 的话：

```
.g.a
  color: red

.b
  @extend .g
  background-color: blue

```

那么生成的就是：

```
.g.a, .a.b { color: red; }
.b { background-color: blue; }

```

上面的例子，把 `.g.a` 换成 `.g .a` 结果相同。

这里的：

```
.g.a, .a.b { color: red; }

```

也可以看成是：

```
.g.a, .b.a { color: red; }

```

## 9.3. 伪类

伪类本身是一个限定，所以，如果碰到伪类的情况，就把它看成是一个普通类限定， *@extend* 就只是指定了一个复合条件而已。

先看复合条件的情况：

```
.g .x.y.z
  color: red

.m
  @extend .x.y

```

生成：

```
.g .x.y.z, .g .z.m { color: red; }

```

即：

```
.g .x.y.z, .g .m.z { color: red; }

```

伪类同样：

```
.g img.user:hover
  color: red

.b
  @extend img:hover

```

把 `:hover` 看成 `.hover` 就好了：

```
.g img.user:hover, .g .user.b { color: red; }

->

.g img.user:hover, .g .b.user { color: red; }

```

不过伪类一般会这么用吧：

```
.g img.user:hover
  color: red

.b
  &:hover
    @extend img:hover

```

生成：

```
.g img.user:hover, .g .user.b:hover { color: red; }

->

.g img.user:hover, .g .b.user:hover { color: red; }

```

## 9.4. 序列规则下的使用

前面的例子， *@extend* 所在的规则，本身都比较简单。最复杂的情况，就是 *@extend* 所在的规则是多重限定，并且 *@extend* 指定的条件是多重限定，同时，目标定义时，又有多重限定，最后，目标可能还有多个。

四个点， *@extend* 所在的块， *@extend* 本身， *@extend* 可能碰到的目标， *@extend* 可能碰到的多个目标。

当然，现实情况不会这样，太复杂了 SASS 自己也搞不定。所以， SASS 中， *@extend* 本身的规则，是不允许有“序列条件”的。

```
.x a
  color: red

.header > a
  @extend .x a

```

这种情况，不被允许。

*@extend* 本身简单了一点，但是情况还是有些伤脑筋：

```
.x > .side .m.a
  color: red

.y .m.a
  background-color: yellow

.header .title a
  @extend .m.a

```

当 *@extend* 的目标有多个时，我们一个一个来看就好了，先看 `.x > .side .m.a` 。

前面说过， *@extend* 不会改变原来的限定，所以， `.x > .side` 这个条件是不会变的。但是，这里的情况有些不一样，因为， *@extend* 所在的块，本身还有一个 `.header .title` 的前置限定条件。

那么现在的问题就是，对于 `.header .title a` 中的这个 **a** ，它有两组前置限定条件了。

这里的两组条件，其实跟前面是一回事。最开始的一条，就说了，别忘了在 *@extend* 所在的块本身，还会有一条定义生成。不同的是，这里 *@extend* 所在的块本身，会被附加限定条件。

所以，单看：

```
.x > .side .m.a
  color: red

.header .title a
  @extend .m.a

```

- 第一步： *目标照写*， `.x > .side .m.a { color: red }` 。
- 第二步： *自己照写*， `.header .title .x > .side a { color: red }` 。
- 第三步： *目标 -> @extend*， `.x > .side .header .title a { color: red }` 。

换作最简单情况的话：

```
.em
  color: red

.side .content
  @extend .em

```

- 第一步： *目标照写*， `.em { color: red }` 。
- 第二步： *自己照写*， `.side .content { color: red }` 。
- 第三步： *目标 -> @extend*， `.side .content { color: red }` 。

## 9.5. 总结

总结起来，对于：

```
[PRE_A] TARGET_A
    color: red

[PRE_B] TARGET_B
    @extend TARGET_A

```

结果就是：

- `[PRE_A] TARGET_A { color: red }`

- `... TARGET_B { color: red }`

- ```
  ... TARGET_B { color: red }
  ```

  ​

  然后放

   

  ```
  [PRE_A] [PRE_B]
  ```

   

  和

   

  ```
  [PRE_B] [PRE_A]
  ```

   

  两种情况，得到：

  ​

- `[PRE_A] TARGET_A { color: red }`

- `[PRE_A] [PRE_B] TARGET_B { color: red }`

- `[PRE_B] [PRE_A] TARGET_B { color: red }`

看之前的第二个目标实例：

```
.y .m.a
  background-color: yellow

.header .title a
  @extend .m.a

```

自然就是生成：

```
.y .m.a { background-color: yellow }
... a { background-color: yellow }
... a { background-color: yellow }

```

补充上：

```
.y .m.a { background-color: yellow }
.header .title .y a { background-color: yellow }
.y .header .title a { background-color: yellow }

```

## 9.6. 抽象块

SASS 还真是把 *@extend* 搞得复杂哦。 SASS 中还有一种“抽象块”的概述，相对于“抽象类”去理解吧。就是，定义的规则块，只是用来被 *@extend* 的，它自己不会出现在最终的 CSS 中，这种块，使用 *%name* 结尾来标识：

```
.x .y a%only
  color: red

.title div .link
  @extend %only

```

结果就是：

```
.x .y a { color: red; }
... a.link { color: red; }
... a.link { color: red; }

```

补充：

```
.x .y a { color: red; }
.x .y .title div a.link { color: red; }
.title div .x .y a.link { color: red; }

```

最后去掉不要的：

```
.x .y .title div a.link { color: red; }
.title div .x .y a.link { color: red; }

```

抽象块最后 `a.link` 的处理，跟普通 *@extend* 行为不一样，普通的 *@extend* 不会保留 `a` ，只有 `.link` 。

# 10. @import 引入其它文件

*@import* ，首先有原本的功能，即可以引入一段其它的 CSS 。

同时， SASS 中的 *@import* 也可以引入自己的 SASS 文件，比如：

```
@import "reset.sass"

.ok
  @extend .em

```

`reset.sass` 中有定义 `.em { color: red; }` ，最后编译就可以得到：

```
.em, .ok { color: red; }

```

*@import* 的其它使用形式还有：

- `@import "a", "b"` 引入多个文件
- `@import "a" screen` 使用媒体查询
- `@import url("http://fonts.googleapis.com/css?family=#{$family}")` 使用变量

`@import` 可以在规则中使用，它的行为类似单纯的宏替换，比如：

```
.a1
  @import "a.sass" 

```

# 11. @media 媒体查询

*@media* 的写法与其在 CSS 中是一样的，SASS 中， *@media* 的额外能力，就是你可以把它写在规则下面，编译的时候，会自动整理代码，把 *@media* 部分抽到最外面去：

```
.a
  color: red
  @media print
    color: black

```

会生成：

```
.a { color: red; }
@media print { .a { color: black; } }

```
