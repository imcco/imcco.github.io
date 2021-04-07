---
title: 10 种最常见的 Javascript 错误
tags: JavaScript
category: JavaScript
date: 2018-02-09 21:44:36
---
![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0119.jpg)

我们查看了数千个项目的数据库，发现了 JavaScript 中频度最高的 10 种错误。我们会告诉你什么原因导致了这些错误，以及如何防止这些错误发生。如果你能够避免落入这些 “陷阱”，你将会成为一个更好的开发者。
<!--more-->
> 原文：https://rollbar.com/blog/top-10-javascript-errors/

数据才是王道，我们收集并分析了出现频次排前 10 的 JavaScript 错误。 Rollbar 会收集每个项目的所有错误，并总结每个错误发生的次数。我们通过根据 “指纹”（rollbar 用到的一种算法，详见：https://rollbar.com/docs/grouping-algorithm/）对错误进行分组。基本上，如果第二个错误只是第一个错误的重复，我们会把两个错误分到同一组。这会给用户一个很好的概括，而不是像在日志文件中看到的那样直接一大堆让人感觉到十分压迫的 dump。

我们专注于最有可能影响您和您的用户的错误。为此，我们通过研究各种不同公司的项目集来对于错误进行排列。如果我们只查看每个错误发生的总次数，那么客户量大的的项目产生的错误可能会压倒其他错误，导致实际收集到的是与大多数读者无关的错误数据集。

### ** JavaScript 错误 Top 10：**

![img](http://ovi3ob9p4.bkt.clouddn.com/js/js001.jpg)

为了便于阅读，我们将每个错误描述都缩短了。接下来，让我们深入到每一个错误，来确定什么会导致它，以及如何避免创建它。

### **1. Uncaught TypeError: Cannot read property**

如果你是一个 JavaScript 开发人员，可能你看到这个错误的次数比你敢承认的要多（LOL…）。当你读取一个未定义的对象的属性或调用其方法时，这个错误会在 Chrome 中出现。 您可以很容易的在 Chrome 开发者控制台中进行测试（尝试）。

![img](http://ovi3ob9p4.bkt.clouddn.com/js/js002.jpg)

发生这种情况的原因很多，但常见的一种是在渲染 UI 组件时对于状态的初始化操作不当。

我们来看一个在真实应用程序中发生的例子：我们选择 React，但该情况也同样适用于 Angular、Vue 或任何其他框架。

```js
class Quiz extends Component {
  componentWillMount() {
    axios.get('/thedata').then(res => {
      this.setState({items: res.data});
    });
  }
  render() {
    return (
      <ul>
        {this.state.items.map(item =>
          <li key={item.id}>{item.name}</li>
        )}
      </ul>
    );
  }
}
```

这里有两件重要的事情要实现：

1. 组件的状态（例如 this.state）从 undefined 开始。
2. 当异步获取数据时，不管它是在构造函数componentWillMount还是componentDidMount中获取的，组件在数据加载之前至少会呈现一次，当 Quiz 第一次呈现时，this.state.items 是未定义的。 这又意味着 ItemList 将 items 定义为 undefined，并且在控制台中出现错误 - “Uncaught TypeError: Cannot read property ‘map’ of undefined”。

这很容易解决。最简单的方法：在构造函数中用合理的默认值来初始化 state。

```js
class Quiz extends Component {
  // Added this:
  constructor(props) {
    super(props);
    // Assign state itself, and a default value for items
    this.state = {
      items: []
    };
  }
  componentWillMount() {
    axios.get('/thedata').then(res => {
      this.setState({items: res.data});
    });
  }
  render() {
    return (
      <ul>
        {this.state.items.map(item =>
          <li key={item.id}>{item.name}</li>
        )}
      </ul>
    );
  }
}
```

在你的应用程序中的具体代码可能是不同的，但我们希望我们已经给你足够的线索，以解决或避免在你的应用程序中出现的这个问题。如果还没有，请继续阅读，因为我们将在下面覆盖更多相关错误的示例。

### **2. TypeError: ‘undefined’ is not an object**

这是在 Safari 中读取属性或调用未定义对象上的方法时发生的错误。您可以在 Safari Developer Console 中轻松测试。这与 1 中提到的 Chrome 的错误基本相同，但 Safari 使用了不同的错误消息提示语。

![img](http://ovi3ob9p4.bkt.clouddn.com/js/js003.jpg)

### **3. TypeError: null is not an object**

这是在 Safari 中读取属性或调用空对象上的方法时发生的错误。 您可以在 Safari Developer Console 中轻松测试。

![img](http://ovi3ob9p4.bkt.clouddn.com/js/js004.jpg)

有趣的是，在 JavaScript 中，null 和 undefined 是不一样的，这就是为什么我们看到两个不同的错误信息。undefined 通常是一个尚未分配的变量，而 null 表示该值为空。 要验证它们不相等，请尝试使用严格的相等运算符 ===：

![img](http://ovi3ob9p4.bkt.clouddn.com/js/js005.jpg)

在现实世界的例子中，这种错误可能发生的一种场景是：如果在加载元素之前尝试在 JavaScript 中使用元素。 因为 DOM API 对于空白的对象引用返回值为 null。

任何执行和处理 DOM 元素的 JS 代码都应该在创建 DOM 元素之后执行。 JS 代码按照 HTML 中的规定从上到下进行解释。 所以，如果 DOM 元素之前有一个标签，脚本标签内的 JS 代码将在浏览器解析 HTML 页面时执行。 如果在加载脚本之前尚未创建 DOM 元素，则会出现此错误。

在这个例子中，我们可以通过添加一个事件监听器来解决这个问题，这个监听器会在页面准备好的时候通知我们。 一旦 addEventListener被触发，init() 方法就可以使用 DOM 元素。

```js
<script>
  function init() {
    var myButton = document.getElementById("myButton");
    var myTextfield = document.getElementById("myTextfield");
    myButton.onclick = function() {
      var userName = myTextfield.value;
    }
  }
  document.addEventListener('readystatechange', function() {
    if (document.readyState === "complete") {
      init();
    }
  });
</script>
<form>
  <input type="text" id="myTextfield" placeholder="Type your name" />
  <input type="button" id="myButton" value="Go" />
</form>
```

### **4. (unknown): Script error**

当未捕获的 JavaScript 错误（通过window.onerror处理程序引发的错误，而不是捕获在try-catch中）被浏览器的跨域策略限制时，会产生这类的脚本错误。 例如，如果您将您的 JavaScript 代码托管在 CDN 上，则任何未被捕获的错误将被报告为“脚本错误” 而不是包含有用的堆栈信息。这是一种浏览器安全措施，旨在防止跨域传递数据，否则将不允许进行通信。

要获得真正的错误消息，请执行以下操作：

1. 发送 ‘Access-Control-Allow-Origin’ 头部

将 Access-Control-Allow-Origin 标头设置为 * 表示可以从任何域正确访问资源。 如有必要，您可以将域替换为您的域：例如，Access-Control-Allow-Origin：www.example.com。 但是，处理多个域会变得棘手，如果你使用 CDN，可能由此产生更多的缓存问题会让你感觉到这种努力并不值得。 在这里看到更多。

这里有一些关于如何在各种环境中设置这个头文件的例子：

Apache

在 JavaScript 文件所在的文件夹中，使用以下内容创建一个 .htaccess 文件：

```
Header add Access-Control-Allow-Origin "*"
```

Nginx

将 add_header 指令添加到提供 JavaScript 文件的位置块中：

```
> location ~ ^/assets/ {
>
>     add_header Access-Control-Allow-Origin *;
> }
```
HAProxy

将以下内容添加到您为 JavaScript 文件提供资源服务的后端：

```
> rspadd Access-Control-Allow-Origin: *
```

2. 在 <script 中设置 crossorigin="anonymous"

在您的 HTML 代码中，对于您设置了Access-Control-Allow-Origin header 的每个脚本，在 script 标签上设置crossorigin =“anonymous”。在脚本标记中添加 crossorigin 属性之前，请确保验证上述 header 正确发送。 在 Firefox 中，如果存在crossorigin属性，但Access-Control-Allow-Origin头不存在，则脚本将不会执行。

### **5. TypeError: Object doesn’t support property**

这是您在调用未定义的方法时发生在 IE 中的错误。 您可以在 IE 开发者控制台中进行测试。

![img](http://ovi3ob9p4.bkt.clouddn.com/js/js006.jpg)

这相当于 Chrome 中的 “TypeError：”undefined“ is not a function” 错误。 是的，对于相同的逻辑错误，不同的浏览器可能具有不同的错误消息。

对于使用 JavaScript 命名空间的 Web 应用程序，这是一个 IE l浏览器的常见的问题。 在这种情况下，99.9％ 的原因是 IE 无法将当前名称空间内的方法绑定到 this 关键字。 例如：如果你 JS 中有一个命名空间 Rollbar 以及方法 isAwesome 。 通常，如果您在 Rollbar 命名空间内，则可以使用以下语法调用isAwesome方法：

```
> this.isAwesome();
```

Chrome，Firefox 和 Opera 会欣然接受这个语法。 另一方面 IE，不会。 因此，使用 JS 命名空间时最安全的选择是始终以实际名称空间作为前缀。

```
> Rollbar.isAwesome();
```

### **6. TypeError: ‘undefined’ is not a function**

当您调用未定义的函数时，这是 Chrome 中产生的错误。 您可以在 Chrome 开发人员控制台和 Mozilla Firefox 开发人员控制台中进行测试。

![img](http://ovi3ob9p4.bkt.clouddn.com/js/js007.jpg)

随着 JavaScript 编码技术和设计模式在过去几年中变得越来越复杂，回调和关闭中的自引用范围也相应增加，这是这种/那种混淆的相当常见的来源。

考虑这个代码片段：

```js
function testFunction() {
  this.clearLocalStorage();
  this.timer = setTimeout(function() {
    this.clearBoard();    // what is "this"?
  }, 0);
};
```

执行上面的代码会导致以下错误：“Uncaught TypeError：undefined is not a function”。 你得到上述错误的原因是，当你调用setTimeout()时，实际上是调用window.setTimeout()。 因此，在窗口对象的上下文中定义了一个传递给setTimeout()的匿名函数，该函数没有clearBoard()方法。

一个传统的，旧浏览器兼容的解决方案是简单地将您的 this 保存在一个变量，然后可以由闭包继承。 例如：

```js
function testFunction () {
  this.clearLocalStorage();
  var self = this;   // save reference to 'this', while it's still this!
  this.timer = setTimeout(function(){
    self.clearBoard();  
  }, 0);
};
```

或者，在较新的浏览器中，可以使用bind()方法传递适当的引用：

```js
function testFunction () {
  this.clearLocalStorage();
  this.timer = setTimeout(this.reset.bind(this), 0);  // bind to 'this'
};
function testFunction(){
    this.clearBoard();    //back in the context of the right 'this'!
};
```

### **7. Uncaught RangeError: Maximum call stack**

这是 Chrome 在一些情况下会发生的错误。 一个是当你调用一个不终止的递归函数。您可以在 Chrome 开发者控制台中进行测试。

![img](http://ovi3ob9p4.bkt.clouddn.com/js/js007.jpg)

此外，如果您将值传递给超出范围的函数，也可能会发生这种情况。 许多函数只接受其输入值的特定范围的数字。 例如：Number.toExponential(digits) 和 Number.toFixed(digits) 接受 0 到 20 的数字，Number.toPrecision(digits) 接受 1 到 21 的数字。

```js
var a = new Array(4294967295);  //OK
var b = new Array(-1); //range error
var num = 2.555555;
document.writeln(num.toExponential(4));  //OK
document.writeln(num.toExponential(-2)); //range error!
num = 2.9999;
document.writeln(num.toFixed(2));   //OK
document.writeln(num.toFixed(25));  //range error!
num = 2.3456;
document.writeln(num.toPrecision(1));   //OK
document.writeln(num.toPrecision(22));  //range error!
```

### **8. TypeError: Cannot read property ‘length’**

这是 Chrome 中发生的错误，因为读取未定义变量的长度属性。 您可以在 Chrome 开发者控制台中进行测试。

![img](http://ovi3ob9p4.bkt.clouddn.com/js/js008.jpg)

您通常会在数组中找到定义的长度，但是如果数组未初始化或者变量名称在另一个上下文中隐藏，则可能会遇到此错误。让我们用下面的例子来理解这个错误。

```js
var testArray = ["Test"];
function testFunction(testArray) {
    for (var i = 0; i < testArray.length; i++) {
      console.log(testArray[i]);
    }
}
testFunction();
```

当你用参数声明一个函数时，这些参数变成了函数作用域内的本地参数。这意味着即使你函数外有名为 testArray 的变量，在一个函数中具有相同名字的参数也会被视为本地参数。

您有两种方法可以解决您的问题：

1. 删除函数声明语句中的参数（事实上你想访问那些声明在函数之外的变量，所以你不需要函数的参数）：

```js
var testArray = ["Test"];
/* Precondition: defined testArray outside of a function */
function testFunction(/* No params */) {
    for (var i = 0; i < testArray.length; i++) {
      console.log(testArray[i]);
    }
}
testFunction();
```

2. 用声明的数组调用该函数：

```js
var testArray = ["Test"];
function testFunction(testArray) {
   for (var i = 0; i < testArray.length; i++) {
      console.log(testArray[i]);
    }
}
testFunction(testArray);
```

### **9. Uncaught TypeError: Cannot set property**

当我们尝试访问一个未定义的变量时，它总是返回 undefined，我们不能获取或设置任何未定义的属性。 在这种情况下，应用程序将抛出 “Uncaught TypeError: Cannot set property”。

例如，在 Chrome 浏览器中：

![img](http://ovi3ob9p4.bkt.clouddn.com/js/js009.jpg)

如果测试对象不存在，错误将会抛出 “Uncaught TypeErrorUncaught TypeError: Cannot set property”。

**10. ReferenceError: event is not defined**

当您尝试访问未定义的变量或超出当前范围的变量时，会引发此错误。 您可以在 Chrome 浏览器中轻松测试。

![img](http://ovi3ob9p4.bkt.clouddn.com/js/js010.jpg)

如果在使用事件处理系统时遇到此错误，请确保使用传入的事件对象作为参数。像 IE 这样的旧浏览器提供了一个全局变量事件，但并不是所有浏览器都支持。像 jQuery 这样的库试图规范化这种行为。尽管如此，最好使用传入事件处理函数的函数。

```js
function myFunction(event) {
    event = event.which || event.keyCode;
    if(event.keyCode===13){
       alert(event.keyCode);
    }
}
```

### **结论**

我们希望你学到了新的东西，可以避免将来的错误，或者本指南帮助你解决了头痛的问题。

尽管如此，即使有最佳实践，生产中也会出现意想不到的错误。能够查看影响用户的错误，并拥有快速解决问题的好工具，这一点非常重要。推荐 Rollbar。
