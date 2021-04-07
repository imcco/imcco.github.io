---
title: JavaScript中的Bind，Call和Apply
tags: JavaScript
category: JavaScript
date: 2018-01-29 15:44:36
---
![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0105.jpg)

js 中的 *上下文 Context*，可以说是一个，你即使不知道，没听过，也不影响你继续写 js 代码的一个概念。但是，它又确确实实是无所不在的一个东西，是的，无所不在。
<!--more-->

### 上下文解释

所谓上下文，它是用来存储系统的一些初始化信息，例如在jboss中通过配置文件指定了数据源，那么在jboss启动的时候就把这个文件的相关信息上下文中，于是在我们使用这个数据源的时候，就需要先获得系统的上下文，然后再通过一定的方式将数据源取出来。至于在系统启动时，是如何将这些初始化信息加载到上下文中，而上下文在程序中具体又是以什么形式体现的，应该随着系统的不同而不同。

　　上下文即ServletContext,是一个全局的储存信息的空间，服务器启动，其就存在，服务器关闭，其才释放。所有用户共用一个ServletContext。所以，为了节省空间，提高效率，ServletContext中，要放必须的、重要的、所有用户需要共享的线程又是安全的一些信息。如，做一个购物类的网站，要从数据库中提取物品信息，如果用session保存这些物品信息，每个用户都访问一便数据库，效率就太低了；所以要用来Servlet上下文来保存，在服务器开始时，就访问数据库，将物品信息存入Servlet上下文中，这样，每个用户只用从上下文中读入物品信息就行了。

　　获取ServletContext的方法：

```java
 HttpSession session = request.getSession();
　 session.getServletContext();
```

从我自己的经验来看，对上下文的认识，算是分成了三个阶段，每一个阶段，都让我从外在的表现中，理解了一些更本质上的东西。

### 1. 第一阶段，不知

我最开始接触 js 的时候，看到了它的 `new` ，看到了它的 `this` ，很自然地会把 js 和其它的一些 OOP 语言等同起来看待，并且，好像，也是这么回事。比如：

```js
var Class = function(a){
  this.a = a;
  this.add = function(){
    this.a++;
  }
}

var ins = new Class(1);
ins.add();
console.log(ins.a); //2
```

上面的代码，可以按预期的那样，最后得到 `2` 的输出。

但是，如果仅仅是 *类，实例* 这种层面的认识，我无法解释下面的问题：

```js
var ins = new Class(1);
var func = ins.add;
func();
console.log(ins.a); //1
```

甚至解释不清楚下面的代码：

```js
var obj = {
  a: 1,
  add: function(){
    this.a++;
  }
}
obj.add();
console.log(obj.a); //2
```

这里可没有 *类* ，也没有 *实例* 。

我上面的最开始对 js 的认识当中，局限就在于，把 `this` 理解成了 *实例* 。也许在其它语言中（比如 Python 常用的实例方法第一个参数 `self`），是这样。但是在 js 中， `this` 跟 *实例* 完全没有关系。

### 2. 第二阶段，this

当我明白问题出在 `this` 上，或者说，当我终于理解了 `this` 这个东西之后，上面的代码，再也不会困扰我了。

我知道了， js 中有一个东西叫 *上下文* ，可惜的是，这时，我对上下文的概念，仅仅停留在 `this`上。

这时我的理解是： `this` 表示的是，函数调用时的 *上下文* 。

说得详细一点，就是 `this` 不是表示的 *实例* ，而是函数调用时的 *上下文* 。 *上下文* 这个东西，默认是 `window` ，即 *全局* 。但是，你可以明确地为函数指定一个 *上下文* 。回到 `this` 上，就是在定义时你根本不知道 `this` 是什么，因为在调用时，它可以是任何东西（因为 *上下文* 是可以人为指定的）。

回到刚开始的代码：

```js
var Class = function(a){
  this.a = a;
  this.add = function(){
    this.a++;
  }
}

var ins = new Class(1);
ins.add();
console.log(ins.a); //2
```

这段代码的结构之所以是 `2` ，不是因为 *实例* ，而是因为 *上下文* 。

首先说一下 `new` 。 `new` 在 js 中，不考虑原型链它的作用，相当于是先创建了一个空的对象，然后把这个空的对象，作为 *构造函数* 的 *上下文* ，再去执行 *构造函数* ，最后再返回这个当初的空对象。即：

```js
var what_new = function(func, a){
  var context = {};
  func.apply(context, [a]);
  return context;
}

var Class = function(a){
  this.a = a;
  this.add = function(){
    this.a++;
  }
}

var ins = what_new(Class, 1);
ins.add();
console.log(ins.a);
```

当然， `new` 除了上面的 `func.apply` 的作用之外， **它还会处理原型链** ，这里就不介绍了。上面的代码仅是为了说明 `new` 对于所谓的构造函数做了什么事。

有了上下文，就不难解释 `ins` 这个东西了。所谓的构造函数，只是在指定了 `this` 到底是哪一个对象之后，作了相应的赋值操作而已，最后得到这个对象的返回，经过了一些赋值操作，对象中就有了新的东西了。

同样，对于一个在定义时包含了 `this` 的函数，比如前面的例子：

```js
var obj = {
  a: 1,
  add: function(){
    this.a++;
  }
}

```

如果来一句：

```js
var func = obj.add;
func(); //undefined
func.apply({a: 0}) //1

```

这些都很容易明白了。 js 中的函数，都是一些很单纯的函数，所有的函数跟它在哪里定义完全没有关系（考虑闭包的情况除外）。所以上面的代码，虽然 `add` 函数是写在 `obj` 中的，但是，它跟你在 `window` 中写一个函数是 **完全一样** 的：

```js
var add = function(){this.a++}
var obj = {
  a: 1,
  add: add
}

```

既然 `add` 函数中有 `this` ，那么这个函数执行时的行为，就要小心一点了。所以上面明确地指定了一个上下文给它 `func.apply({a: 0})` 。

还是回到开始的代码：

```js
var obj = {
  a: 1,
  add: function(){
    this.a++;
  }
}

```

对于上面的代码，我知道了：

```js
obj.add();

```

和：

```js
var func = obj.add();
func();

```

会得到不一样的结果。并且知道，这个不一样的结果是上下文引起的，还知道，后者 `func()` 执行时，上下文是全局的 `window` 了。

我虽然知道是这样的一个情况，但是，为什么？执行同一个函数结果怎么就不一样了呢？

我在很长时间里，都没有去细细考虑过这个问题。不过，因为知道了“上下文是一个在定义时无意义，其具体值完全由执行时决定”这点之后，我都尽量避免去使用 `this` ，实在要用，在调用时，我都会通过 `apply` 或 `call` 明确指定上下文，这样，至少不会踩坑里。

### 3. 第三阶段，一切都是上下文

某天，我在网上看到了这样一段代码（原始出处不知道）：

```js
var bind = Function.prototype.call.bind(Function.prototype.bind)

```

这个新定义的 `bind` 函数具体做什么事先不管它，我好奇的是 `call.bind()` 这个调用。因为 `call` 这个函数，之前一直以为它是 `Function` 对象的一个方法（它本身也是一个函数），但是，如果按“对象的方法”这个角度去想的话，那对它绑定一个上下文（ `bind()` 的调用 ）不就完全没有意义了么？（因为对象的方法应该是跟上下文无关的）

不久之前，我在一条tweet上看到了这样一段js代码：

```js
       var bind = Function.prototype.call.bind(Function.prototype.bind); 
```

第一眼看上去，我能猜出它究竟是用来做什么的。它把x.y(z)转化成了y(x,z)。我欣喜万分的给我的同事看这段代码。他们问我这是什么意思。而我当我正要开口向他们解释时却发现不知道怎么说才好。我徘徊了一会然后郁闷的走开了。

编写良好的代码会向人们传达它的作用。在读完Functional Javascript和 JavaScript Allongé （两本都是相当好的书）这两本书之后，再加上我在Javascript函数式编程方面有些经验，弄懂上面这段代码的意思毫无压力。但是应该怎么向没有函数式编程经验的人解释呢（正如大多数人关心的那样）？

我决定亲自来完成这个任务，通过简单地例子和注释。我的结果如下所示：

```js
  //设立一个简单地对象作为“上下文”
var context = { foo: "bar" };

//一个在this上下文中指向foo变量的函数
function returnFoo () {
  return this.foo;
}

// 变量在作用域中不存在，因此显示undefined
returnFoo(); // => undefined

// 如果我们把它绑定在context上下文中
var bound = returnFoo.bind(context);

// 现在的作用域中有这个变量了
bound(); // => "bar"

//
// 这就是Function.prototype.bind的作用.    
//由于returnFoo也是函数，因此它继承了function的原型
//
// 如果你觉得享受，接着往下读，下面更精彩
//

// 有许多方法将函数绑定在一个上下文中
// Call和Apply让你能在上下文中调用函数
returnFoo.call(context); // => bar
returnFoo.apply(context); // => bar

// 将函数添加到对象中
context.returnFoo = returnFoo;
context.returnFoo(); // => bar

//
// 现在我们来玩一点诡异的东西
//

// Array.prototype 中有一个叫做slice的方法
// 对一个数组调用slice，可以返回一个从start index到end index的数组
[1,2,3].slice(0,1); // => [1]

// 因此我们把Array.slice赋值给一个本地变量slice
var slice = Array.prototype.slice;

//现在的slice是"自由的"，由于Array.prototype中的slice一般指定了上下文
//或者默认为this,此时slice将不起作用
slice(0, 1); // => TypeError: can't convert undefined to object
slice([1,2,3], 0, 1); // => TypeError: ...

// 但是如果我们使用call或者apply，slice又将在一个上下文中执行
slice.call([1,2,3], 0, 1); // => [1]

// Apply和Call差不多，知识参数要放在一个数组中
slice.apply([1,2,3], [0,1]); // => [1]

// 使用call没错了，那么能不呢使用bind呢？
// 没错，我们来把"call"绑定在slice上
slice = Function.prototype.call.bind(Array.prototype.slice);

// 现在slice可以把第一个参数作为上下文了
slice([1,2,3], 0, 1); // => [1]

//
// 很酷，对吧。现在再来完成一件事
//

// 现在我们对bind本身做一件刚才对silce做的事
var bind = Function.prototype.call.bind(Function.prototype.bind);

// 在这里总结一下，好好想想
// 发生了什么事? 我们改变了call，
// 返回一个接收一个函数和一个上下文作为ic桉树的函数
//并且返回了一个完全绑定的函数

// 回到最初的例子
var context = { foo: "bar" };
function returnFoo () {
  return this.foo;
}

// 现在来使用神奇的"bind"函数
var amazing = bind(returnFoo, context);
amazing(); // => bar
```

其中以 `slice` 函数举的例子让我恍然大悟：

- 上下文控制不仅仅是 `apply` / `call`，所有的点 `.` ，都是在指定上下文。
- js 中的函数比我想像的还要纯，根本没有“对象中的方法”这个东西，即使是“原生对象”中。（它仅仅起一个名字空间的作用）

所有的函数调用，都有两层意义，比如 `c.f()`：

- `f` 这个函数，它在 `c` 中。（名字空间的问题）
- 把 `c` 作为 `f` 的上下文，去调用 `f` 。（前提是 `f` 没有绑定过上下文）

如果 `c` 没有，则默认是 `window` 。

所有的，js 中所有的函数调用，都是如此。即使是 `f.call(context, x)` ，我之前只看到了第一层意义（ `f` 中有一个 `call` 方法可以使用），则忽略了第二层意义 —— 把 `f` 作为 `call` 的上下文。

简单来说，我们可以相像 `call` 这个函数，它的代码大概是这样的（可变参数的问题先不管）：

```js
var call = function(context, a){
    var new_func = this.bind(context);
    retur new_func(a);
}

```

它的作用，就是把 *指定的上下文（context）* 作为 *自己的上下文（this）* 的 *上下文* ，然后再调用 *自己的上下文（绑定上下文之后的 this）* 。

上面一句话有些纠结哈，主要搞明白多种上下文的关系， `f.call(context, x)` 当中， *自己的上下文*上面是 `f` 。 *指定的上下文* 上面是 `context` 。

再看 `f.call(context, x)` 这个代码，结合“函数是单纯”这点，我想到，即使是原生对象的那些方法， **也不过是把一些单纯的函数放到了 prototype 中而已** ，比如把 `call` 函数放到了 `Function.prototype` 当中。

至此，再看 `c.f()` ， `a.b.c()` 这些，不要去想是调用 `c` 对象中的 `f` 方法（这么说没错，但是名字空间的问题是显而易见的嘛），而是想成，调用时把 `c` 作为 `f` 的上下文。

好了，回到开始的那行例子：

```js
var new_bind = Function.prototype.call.bind(Function.prototype.bind)

```

这个就非常好理解了（为了描述方便，我改成 `new_bind` 了），把 `bind` 作为上下文绑定到 `call`中。

这里注意一下，绑定了上下文的 `call` 函数，还是 `call` 函数，但是 “此 `call` 已经非彼 `call`” 了。

所以：

```js
new_bind != Function.prototype.call

```

虽然调用形式上， `new_bind` 和 `call` 完全一样，但是他们的上下文行为不一样：

- `call` 是未绑定状态，所以 `f.call()` 会在执行时把 `f` 作为上下文绑定到 `call` 函数中。
- `new_bind` 是已绑定状态，所以 `f.new_bind()` 对 `new_bind()` 的执行完全没影响。

我们可以以这样的流程来帮助我们理解：

```js
new_bind => call => bind.call => bind.call(f, context) => f.bind(context)

```

一步一步解释：

- `new_bind => call`

  `new_bind` 在形式上就是 `call` 。

- `call => bind.call`

  只是这个 `call` ，是指定了 `bind` 作为它的上下文的。既然是 `bind` 作为它的上下文，那我们可以写成是 `bind.call` 的样式。

- `bind.call(f, context) => f.bind(context)`

  `new_bind` 的调用 `new_bind(f, context)` 就相当于是 `bind.call(f, context)` 。考虑 `call` 函数之前的行为： `f.call(context, a)` 是把 `context` 作为 `f` 的上下文，也就是 `context.f(a)` ，那么 `bind.call(f, context)` 对应的就是 `f.bind(context)` 。

- `f.bind(context)`

  不用多说了吧，把 `context` 绑定到 `f` 上，返回一个绑定了上下文的新函数。

完全是最基本的代数推导嘛，形式上，上下文前置总是没有问题的。

### 4. 结语

我一直认同，要理解 js 的东西，从函数式语言入手，非常合适。硬要往面向对象的那套东西上套，太纠结了（我不管概念上到底什么样才叫面向对象，原生没有类定义，没有继承，没有实例化，就别扯这些就完了。对了，我认为原型追溯那不叫继承哈）。

当然，我不知道弄明白了最后那个“代数推导”到底有什么好处，也许没有，因为就算不明白这些也不影响我写了很多可以正常工作的 js 代码嘛。只是，我以后再写，思路上的可能会有一些不同了。比如代码组织的形式上，可以尝试把很多的小函数做到不同的“名字空间”中，然后再在业务层面，通过 *Mixin* 来拼出不同的业务对象。这些函数中可能到处充斥着 `this` ，我能控制好它们了。
