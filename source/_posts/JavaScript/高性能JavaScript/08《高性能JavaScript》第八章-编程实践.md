---
title: 08《高性能JavaScript》第八章 编程实践
date: 2020-12-23 14:44:00
excerpt: 《高性能JavaScript》第八章 编程实践
categories: JavaScript
tags:
    - 高性能JavaScript
    - 原生JS
---

![image](https://upload-images.jianshu.io/upload_images/18236822-ed739b07cf9eed5e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 8.1 避免双重求值

JavaScript像其他很多脚本语言一样，允许你在程序中提取一个包含代码的字符串，然后动态执行它。有四个标准方法可以实现：`eval()`、`Function()`、`setTimeout()`和`setInterval()`。

```JavaScript
var num1 = 5,
num2 = 6,
// eval()
result = eval("num1 + num2"),
// Function()
sum = new Function("arg1", "arg2", "return arg1 + arg2");
// setTimeout()
setTimeout("sum = num1 + num2", 100);
// setInterval()
setInterval("sum = num1 + num2", 100);
```

当你在JavaScript代码中执行另一段JavaScript代码时，都会导致双重求值的性能消耗。此代码首先会以正常的方式求值，然后在执行过程中对包含于字符串中的代码发起另一个求值运算。

```JavaScript
// 较慢
var item = array[0];
// 较快
var item = eval("array[0]");
```

> 性能优化：大多数情况下，没必要使用`eval()`和`Function()`，因此最好避免使用；在使用`setTimeout()`和`setInterval()`的时候，建议传入函数而不是字符串作为第一个参数。

## 8.2 使用Object/Array直接量

使用直接量创建对象和数组。

```JavaScript
// 较慢
var myObject = new Object(); // 创建对象
myObject.name = "Nicholas";
myObject.count = 50;
myObject.flag = true;
myObject.pointer = null;
var myArray = new Array(); // 创建数组
myArray[0] = "Nicholas";
myArray[1] = 50;
myArray[2] = true;
myArray[3] = null;
// 较快
var myObject = { // 创建对象
    name: "Nicholas",
    count: 50,
    flag: true,
    pointer: null
};
var myArray = ["Nicholas", 50, true, null]; // 创建数组
```

## 8.3 避免重复工作

计算机科学领域最主要的性能优化之一就是：避免无谓的工作。有两个意思：别做无关紧要的工作，别重复已经完成的工作。第一个部分容易在代码重构时发现，第二部分往往难以界定。

```JavaScript
function addHandler(target, eventType, handler){
    if (target.addEventListener){ //DOM2 Events
        target.addEventListener(eventType, handler, false);
    } else { //IE
        target.attachEvent("on" + eventType, handler);
    }
}
function removeHandler(target, eventType, handler){
    if (target.removeEventListener){ //DOM2 Events
        target.removeEventListener(eventType, handler, false);
    } else { //IE
        target.detachEvent("on" + eventType, handler);
    }
}
```

上面代码隐藏的性能问题在于：每次函数调用时都做了重复工作。

### 8.3.1 延迟加载

延迟加载意味着在信息使用前不会做任何操作。

```JavaScript
function addHandler(target, eventType, handler){
    // 复写现有函数
    if (target.addEventListener){ //DOM2 Events
        addHandler = function(target, eventType, handler){
            target.addEventListener(eventType, handler, false);
        };
    } else { //IE
        addHandler = function(target, eventType, handler){
            target.attachEvent("on" + eventType, handler);
        };
    }
    // 调用新函数 
    addHandler(target, eventType, handler);
}
function removeHandler(target, eventType, handler){
    // 复写现有函数
    if (target.removeEventListener){ //DOM2 Events
        removeHandler = function(target, eventType, handler){
            target.addEventListener(eventType, handler, false);
        };
    } else { //IE
        removeHandler = function(target, eventType, handler){
            target.detachEvent("on" + eventType, handler);
        };
    }
    // 调用新函数
    removeHandler(target, eventType, handler);
}
```

调用延迟加载函数时，第一次总会消耗较长的时间，但随后调用相同的函数就会更快，因为不需要再执行检测逻辑。

### 8.3.2 条件预加载

条件预加载会在脚本加载期间提前检测，而不会等到函数被调用。检测的操作依然只有一次，只是它在过程中来的更早。

```JavaScript
var addHandler =
    document.body.addEventListener
    ?
    function(target, eventType, handler){
    target.addEventListener(eventType, handler, false);}
    :
    function(target, eventType, handler){
    target.attachEvent("on" + eventType, handler);};
var removeHandler =  
    document.body.removeEventListener
    ?
    function(target, eventType, handler){
    target.removeEventListener(eventType, handler, false);}
    :
    function(target, eventType, handler){
    target.detachEvent("on" + eventType, handler);};
```

## 8.4 使用速度更快的部分

### 8.4.1 位操作

JavaScript中的数字按照IEEE-754标准64位格式存储。在位运算中，数字被转换为有符号32位格式。每种运算符均直接操作在这个32位数上实现结果。尽管需要转换，这个过程与JavaScript中其他数学和布尔运算相比还是非常快。
如果你还不熟悉数字的二进制表示法，用JavaScript的`toString()`方法并传入参数2，就很容易地把数字转换为字符串形式的二进制表达式。

```JavaScript
var num1 = 25, num2 = 3;
alert(num1.toString(2)); //"11001"
alert(num2.toString(2)); // "11"
```

JavaScript 中有四种位逻辑操作符：

1. Bitwise AND `位与`：两个操作数的位都是 1，结果才是 1；
2. Bitwise OR `位或`：有一个操作数的位是 1，结果就是 1；
3. Bitwise XOR `位异或`：两个位中只有一个 1，结果才是 1；
4. Bitwise NOT `位非`：遇 0 返回 1，反之亦然。

```JavaScript
//bitwise AND
var result1 = 25 & 3; //1
alert(result.toString(2)); //"1"
//bitwise OR
var result2 = 25 | 3; //27
alert(resul2.toString(2)); //"11011"
//bitwise XOR
var result3 = 25 ^ 3; //26
alert(resul3.toString(2)); //"11000"
//bitwise NOT
var result = ~25; //-26
alert(resul2.toString(2)); //"-11010"
```

### 8.4.2 原生方法

无论你如何优化JavaScript代码，都永远不会比JavaScript引擎提供的原生方法更快。道理很简单：JavaScript的原生部分在你写代码之前已经存在浏览器中了，并且都是低级语音编写的，诸如C++。这意味着这些方法会编译成机器码，成为浏览器的一部分，所以不会像自己写的JavaScript代码那样受到各种限制。

![常见数学常量](https://upload-images.jianshu.io/upload_images/18236822-6032e94740c59ad9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![数学运算方法](https://upload-images.jianshu.io/upload_images/18236822-934029637ae41b26.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

另外，别忘了之前讨论过的原生的`querySelector()`和`querySelectorAll()`方法，平均耗时是基于JavaScript的CSS查询的10%。

**欢迎大佬纠错指导，欢迎交流学习。**

>GitHub：[https://github.com/Code4GL](https://github.com/Code4GL)
公众号：code_everything
QQ：771841496
邮箱：guanli1991@163.com

![code_everything](/images/code_everything.jpg)
