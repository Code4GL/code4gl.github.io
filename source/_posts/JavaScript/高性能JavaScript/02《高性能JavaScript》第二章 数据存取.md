---
title: 02高性能JavaScript》第二章 数据存取
date: 2020-12-23 14:12:27
excerpt: 《高性能JavaScript》第二章 数据存取
categories: JavaScript
tags:
    - 高性能JavaScript
    - 原生JS
---

![highPerformanceJs](/images/javascript/highPerformanceJs/highPerformanceJs.jpg)

JavaScript四种数据存储位置：

1. 字面量：字面量只代表自身，不存储在特定位置。JavaScript中的字面量有：`字符串`、`数字`、`布尔值`、`对象`、`数组`、`函数`、`正则表达式`，以及`null`和`undefined`值；
2. 本地变量：使用关键字`var`定义的数据存储单元；
3. 数组元素：存储在JavaScript数组对象内部，以数字作为索引；
4. 对象成员：存储在JavaScript对象内部，以字符串作为索引。

> 性能消耗：字面量、本地变量 < 数组元素、对象成员。

## 2.1 管理作用域

### 2.1.1 作用域链和标识符解析

以函数（函数也是一种对象）为例，每一个函数都拥有代码可以访问的属性和一系列不能通过代码访问而仅供JavaScript引擎存取的内部属性。其中有一个内部属性是`[[Scope]]`。
> 作用域链：内部属性`[[Scope]]`包含了一个函数被创建的作用域中对象的集合，被称为作用域链。它决定着哪些数据能被函数访问。
作用域链中的每个对象被称为一个可变对象，每个可变对象都以“键值对”的形式存在。当一个函数创建后，它的作用域链被填充以对象，这些对象代表创建此函数的环境中可访问的数据。

```JavaScript
function add(num1,num2){
    var sum = num1 + num2;
    return sum;
}
```

作用域链产生过程：

1. 函数创建时：它的作用域链中填入一个单独的可变对象，此全局对象代表了所有全局范围定义的变量。此全局对象包含诸如窗口、浏览器和文档之类的访问接口。
![highPerformanceJs02](/images/javascript/highPerformanceJs/highPerformanceJs02/scope-chain.png)
2. 函数执行时：会建立一个称为执行环境（执行期上下文）的内部对象，定义了一个函数执行时的环境。当函数执行完毕，执行环境就被销毁。一个执行环境有它自己的作用域链，用于标识符解析。
3. 执行环境被创建时：它的作用域链被初始化，连同运行函数的`[[Scope]]`属性中所包含的对象。这些值按照它们出现在函数中的顺序，被复制到执行环境的作用域链中。这个过程一旦完成，一个被称作“活动对象”的新对象就为执行环境创建好了。
![highPerformanceJs02](/images/javascript/highPerformanceJs/highPerformanceJs02/scope-chain1.png)

> 性能优化：在函数运行过程中，每遇到一个变量，标识符识别过程要决定从哪里获得或者存储数据。此过程搜索执行环境的作用域链，查找同名的标识符。搜索工作从作用域链的头部开始，也就是当前运行函数的活动对象。如果找到了，那么就使用这个具有指定标识符的变量；如果没找到，搜索工作将进入作用域链的下一个对象。此过程持续运行，直到标识符被找到，或者没有更多对象可用于搜索，这种情况下标识符将被认为是未定义的。函数运行时每个标识符都要经过这样的搜索过程，正是这种搜索过程影响了性能。

### 2.1.2 标识符解析的性能

- 描述
在没有优化 JavaScript 引擎的浏览器中，最好尽可能使用局部变量。一个好的经验法则是：如果某个跨作用域的值在函数中被引用一次以上，那么久把它存到局部变量里。
- 原因
标识符解析是有代价的，事实上没有哪种电脑操作可以不产生性能开销。在执行环境的作用域链中，一个标识符所处的位置越深，它的读写速度就越慢。所以，函数中局部变量的访问速度总是最快的，而全局变量通常是最慢的（优化的 JavaScript 引擎在某些情况下可以改变这种状况）。

- 反例

```JavaScript
// 引用三次document
function initUI(){
    var bd = document.body,
    links = document.getElementsByTagName_r("a"),
    i = 0,
    len = links.length;
    
    while(i < len){
        update(links[i++]);
    }
    document.getElementById("go-btn").onclick = function(){
        start();
    };
    bd.className = "active";
}
```

- 正例

```JavaScript
// 引用一次document
function initUI(){
    var doc = document,
    bd = doc.body,
    links = doc.getElementsByTagName_r("a"), 
    i = 0,
    len = links.length;
    
    while(i < len){
        update(links[i++]);
    }
    doc.getElementById("go-btn").onclick =    function(){
        start();
    };
    bd.className = "active";
}
```

### 2.1.3 改变作用域链

一般来说，一个执行环境的作用域链不会被改变。但是，有两个语句可以在运行时临时改变执行环境作用域链。分别是`with`和`try-catch`。
> `with`语句用来个对象的所有属性创建一个变量。在其他语言中，类似的功能通常用来避免书写一些重复的代码。

```JavaScript
// 通过把document对象传递给with语句，一个包含了document对象所有属性的新的可变变量被置于作用域链的头部。
// 这使得document对象访问速度非常快，而局部变量的访问速度就变慢了。所以，要避免使用with语句。
function initUI(){
    with (document){ // 避免!
        var bd = body,
        links = getElementsByTagName_r("a"),
        i = 0,
        len = links.length;
        while(i < len){
            update(links[i++]);
        }
        getElementById("go-btn").onclick = function(){
            start();
        };
        bd.className = "active";
    }
}
```

> `try-catch`语句用来捕获异常。当`try`代码块中发生错误，执行过程会自动跳转到`catch`子句，然后把异常对象推入一个变量对象并置于作用域首位。

```JavaScript
// 如果使用得当，try-catch语句是非常用的语句。因此不建议完全弃用。
try {
    methodThatMightCauseAnError();
} catch (ex){
    alert(ex.message); // 作用域链在此处改变
}
```

![highPerformanceJs02](/images/javascript/highPerformanceJs/highPerformanceJs02/scope-chain2.png)

> 性能优化：尽量简化代码来使得`catch`子句对性能的影响。推荐的做法将错误委托给一个函数来处理，如`handleError(e);`，由于只执行一条语句，且没有局部变量的访问，作用域链的临时改变就不会影响代码性能。

### 2.1.4 动态作用域

无论是`with`语句还是`try-catch`语句的`catch`子句，以及包含`eval()`的函数，都被认为是动态作用域。一个动态作用域只存在于代码执行过程中，因此无法通过静态分析（察看代码结构）来确定（是否存在动态作用域）。

```JavaScript
function execute(code) {
    eavl(code);
    function subroutine(){
        return window;
    }
    var w = subroutine(); // w是什么？
};
```

由于使用了`eval()`，函数`execute()`看上去像动态作用域。变量`w`的值会随`code`的值而改变。大部分情况下，`w`的值等同于全局的`window`对象。但是考虑如下情况：

```JavaScript
execute("var window = {};")
```

以上代码中，`execute()`中的`eval()`创建了一个局部变量`window`，因此`w`等同于局部变量`window`，而非全局的`window`对象。只有执行了这段代码才会发现问题，这意味着`window`标识符的真实值是无法被预知的。
> 性能优化：只有确实有必要时，才推荐使用动态作用域。

### 2.1.5闭包、作用域和内存

闭包是 JavaScript 最强大的特性之一，它允许函数访问局部范围之外的数据。

```JavaScript
function assignEvents(){
    var id = "xdi9592";
    // 给一个DOM元素设置点击事件，这个点击事件函数就是一个闭包。
    document.getElementById("save-btn").onclick = function(event){
        // 为了让这个闭包访问id，必须创建一个特定的作用域链。
        saveDocument(id);
    };
}
```

当`assignEvents()`函数执行时，一个包含了变量`id`以及其他数据的活动对象被创建。他成为执行环境作用域链中的第一个对象，而全局变量紧随其后。当闭包被创建是，它的[[Scope]]属性被初始化为这些对象。

![highPerformanceJs02](/images/javascript/highPerformanceJs/highPerformanceJs02/scope-chain3.png)

> 闭包会导致内存泄漏：由于闭包的`[[Scope]]`属性包含与执行环境作用域链相同的对象引用，会产生副作用。通常，一个函数的活动对象与执行环境一同销毁。当涉及闭包时，由于引用仍然存在于闭包的`[[Scope]]`属性中，因此活动对象就无法销毁了，因为引用仍然存在于闭包的[[Scope]]属性中。这意味着脚本中的闭包与非闭包函数相比，需要更多内存开销。在大型网页应用中，这可能是个问题，尤其在 Internet Explorer 中更被关注。IE 使用非本地 JavaScript 对象实现 DOM 对象，闭包可能导致内存泄露。

当闭包代码执行时，会创建一个执行环境，它的作用域链与属性`[[Scope]]`中所引用的两个相同的作用域对象一起被初始化，然后一个活动对象为闭包自身所创建。

![highPerformanceJs02](/images/javascript/highPerformanceJs/highPerformanceJs02/scope-chain4.png)

注意闭包中使用的两个标识符，`id`和`saveDocument`存在于作用域链第一个对象之后。这是闭包最主要的性能关注点：在频繁访问跨作用域的标识符时，每次访问都会带来性能的损失。
> 性能优化：在脚本编程中，最好小心使用闭包。如果非要使用，可以遵循上篇的处理来减轻闭包对执行速度的影响：将常用的跨作用域变量储存在局部变量中，然后直接访问局部变量。

## 2.2 对象成员

### 2.2.1 原型

JavaScript中的对象是基于原型的。原型是其他对象的基础，定义并实现了一个新创建的对象所必须包含的成员列表。

```JavaScript
var book = {
    title: "High Performance JavaScript",
    publisher: "Yahoo! Press"
};
alert(book.toString()); // "[object Object]"
```

一个对象通过一个内部属性绑定到它的原型。Firefox，Safari，和 Chrome 向开发人员开放这一属性，称作`__proto__`；其他浏览器不允许脚本访问这一属性。任何时候你创建一个内置类型的实例，这些实例自动拥有一个 Object 作为它们的原型。因此，对象可以有两种成员类型：实例成员（own成员）和原型成员。实例成员直接存在于对象实例中，原型成员则从对象原型继承而来。

![highPerformanceJs02](/images/javascript/highPerformanceJs/highPerformanceJs02/scope-chain5.png)

> 性能优化：当`book.toString()`被调用时，对从对象实例开始搜索名为“toString”的成员。一旦book中没有名为“toString”的成员，那么会继续搜索其原型对象，直到`book.toString()`方法被找到并且执行。

### 2.2.2 原型链

对象的原型决定了实例的类型。默认情况下，所有对象都是对象`Object`的实例，并继承了所有基本方法。

```JavaScript
function Book(title, publisher){
    this.title = title;
    this.publisher = publisher;
}
Book.prototype.sayTitle = function(){
    alert(this.title);
};
var book1 = new Book("High Performance JavaScript", "Yahoo! Press");
var book2 = new Book("JavaScript: The Good Parts", "Yahoo! Press");
alert(book1 instanceof Book); // true
alert(book1 instanceof Object); // true
book1.sayTitle(); // "High Performance JavaScript"
alert(book1.toString()); // "[object Object]"
```

实例book1的原型`__proto__`是`Book.prototype`，而Book.prototype的原型是`Object`。这是原型链的创建过程，book1和book2继承了原型链中的所有成员。

![highPerformanceJs02](/images/javascript/highPerformanceJs/highPerformanceJs02/scope-chain6.png)

> 性能优化：搜索实例成员比从字面量或局部变量中读取数据的代价更高，再加上遍历原型链带来的开销，这让性能问题更为严重。

### 2.2.3 嵌套成员

每次遇到点操作符，嵌套成员会导致JavaScript引擎搜索所有对象成员。对象嵌套的越深，读取速度就越慢。

```JavaScript
location.href // 最快
window.location.href // 中等
window.location.href.toString() // 最慢
```

> 性能优化：推荐使用局部变量来替代属性以避免多次查找带来的性能开销。

### 2.2.4 缓存对象成员值

- 描述
通常来说，在函数中如果要多次读取同一个对象属性，最佳做法是将属性值保存到局部变量中。
- 原因
局部变量来替代属性以避免多次查找带来的性能开销。特别是处理嵌套对象成员时，这样做会明显提升执行速度。

- 反例

```JavaScript
function toggle(element){
    if (YAHOO.util.Dom.hasClass(element, "selected")){
        YAHOO.util.Dom.removeClass(element, "selected");
        return false;
    } else {
        YAHOO.util.Dom.addClass(element, "selected");
        return true;
    }
}
```

- 正例

```JavaScript
function toggle(element){
    var Dom = YAHOO.util.Dom;
    if (Dom.hasClass(element, "selected")){ 
        Dom.removeClass(element, "selected");
        return false;
    } else {
        Dom.addClass(element, "selected");
        return true;
    }
}
```

> 提示：这种优化不推荐用于对象的成员方法。因为许多对象方法使用`this`来判断执行环境，把一个对象方法保存在局部变量会导致`this`绑定到`window`，而`this`的值的改变会使得JavaScript引擎无法正确解析它的对象成员，而导致程序出错。

**欢迎大佬纠错指导，欢迎交流学习。**

>GitHub：[https://github.com/Code4GL](https://github.com/Code4GL)
公众号：code_everything
QQ：771841496
邮箱：guanli1991@163.com

![code_everything](/images/code_everything.jpg)
