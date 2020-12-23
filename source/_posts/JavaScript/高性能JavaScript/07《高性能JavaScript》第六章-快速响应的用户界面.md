---
title: 07《高性能JavaScript》第六章 快速响应的用户界面
date: 2020-12-23 14:38:57
excerpt: 《高性能JavaScript》第六章 快速响应的用户界面
categories: JavaScript
tags:
    - 高性能JavaScript
    - 原生JS
---

![image](https://upload-images.jianshu.io/upload_images/18236822-ed739b07cf9eed5e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 6.1 浏览器UI线程

浏览器UI线程：用于执行JavaScript和更新用户界面的进程。UI线程的工作基于一个简单的队列系统，任务会被保存到队列中直到进程空闲。

```html
<html>
    <head>
        <title>Browser UI Thread Example</title>
    </head>
    <body>
        <button onclick="handleClick()">Click Me</button>
        <script type="text/javascript">
            function handleClick(){
                var div = document.createElement("div");
                div.innerHTML = "Clicked!";
                document.body.appendChild(div);
            }
        </script>
    </body>
</html>
```

![UI线程处理流程](https://upload-images.jianshu.io/upload_images/18236822-33b7154924cc7ebf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 6.1.1 浏览器限制

浏览器限制了JavaScript任务的运行时间，它确保某些恶意代码不能通过永不停止的密集操作锁住用户的浏览器或计算机。此类限制分为两种：调用栈大小限制（第四章讨论过）和长时间运行脚本限制。

![运行超过500万条语句时，IE警告对话框](https://upload-images.jianshu.io/upload_images/18236822-774e70f3cc68fa7d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

对于不同浏览器限制也不一样：

1. IE：默认500万条语句；
2. Firefox：默认限制10秒；
3. Safari：默认限制5秒；
4. Chrome：没有单独限制，替代做法是依赖其通用崩溃检测系统来处理；
5. Opera：没有限制，鉴于Opera架构，运行结束时不会导致系统不稳定。

### 6.1.2 多久才算太久

单个JavaScript操作花费的总时间不应该超过`100毫秒`。这个数字源自Robert Miller在1968年的研究。如果页面响应超过`100毫秒`，用户会感到自已与界面失去联系。因此，限制所有的JavaScript任务在`100毫秒`或更短的时间内完成，才能给用户一个连续的体验。

## 6.2 使用定时器让出时间片段

尽管尽了最大努力，有时也难免出现复杂的JavaScript任务不能在100毫秒内完成。这时候，最理想的做法是让出UI线程的控制权，使得UI可以更新。

### 6.2.1 定时器基础

在JavaScript中可以使用`setTimeout()`和`setInterval()`创建定时器。定时器和UI线程的交互方式有助于把运行耗时较长的脚本拆分为较短的片段。

> 注意：定时器传入的时间参数表示任务被添加到UI线程的时间，而不是一定会在这段时间后执行，它会在队列中等待其他前面所有任务执行完毕才会执行。

```JavaScript
var button = document.getElementById("my-button");
button.onclick = function(){
    oneMethod();
    setTimeout(function(){
        document.getElementById("notice").style.color = "red";
    }, 250);
};
```

![250秒后加入队列](https://upload-images.jianshu.io/upload_images/18236822-2884cddde6910f94.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> 注意：定时器代码只有在创建它的函数执行完成之后，才有可能被执行。

```JavaScript
var button = document.getElementById("my-button");
button.onclick = function(){
    oneMethod();
    setTimeout(function(){
        document.getElementById("notice").style.color = "red";
    }, 50); 
    anotherMethod();
};
```

![定时器中代码耗时超过设定时间50秒](https://upload-images.jianshu.io/upload_images/18236822-9b44ec9ed569cc79.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 6.2.2 定时器的精度

JavaScript定时器的延迟通常不太精准，相差大约几毫秒。正因为如此，定时器不能用于测量实际时间。设置定时器延迟小于`15毫秒`会导致IE锁定，所以延迟的最小值建议设为`25毫秒`，以确保至少有15毫秒延迟。

### 6.2.3 使用定时器处理数组

在之前讨论的循环优化技术使用后，还不能满足性能要求的时候，下一步优化就是选用定时器。它的基本方法是把循环工作分解到一系列定时器中。
是否可以用定时器取代循环的两个决定性因素：

1. 处理过程是否必须同步？
2. 数据是否必须按顺序处理？
如果这两个答案都是：`否`，那么代码将适用于定时器分解任务。

```JavaScript
function processArray(items, process, callback){
    var todo = items.concat(); // 克隆原数组
    setTimeout(function(){
    process(todo.shift()); // 取得数组的下一个元素并进行处理
    if (todo.length > 0){ // 如果还有需要处理的元素，创建新的定时器
        setTimeout(arguments.callee, 25); // arguments.callee该值指向当前运行的匿名函数
    } else {
        callback(items);
    }
    }, 25);
}
```

```JavaScript
// 使用
var items = [123, 789, 323, 778, 232, 654, 219, 543, 321, 160];
function outputValue(value){
    console.log(value);
}
processArray(items, outputValue, function(){
    console.log("Done!");
});
```

> 注意：使用定时器的副作用是处理数组的总时长增加了，但为了避免锁定浏览器给用户带来不好的体验，这种取舍是必要的。

### 6.2.4 分割任务

我们通常会把一个复杂的任务分解成一系列子任务。

```JavaScript
function saveDocument(id){
    // 保存文档
    openDocument(id)
    writeText(id);
    closeDocument(id);
    // 将成功信息更新至页面
    updateUI(id);
}
```

```JavaScript
function multistep(steps, args, callback){
    var tasks = steps.concat(); // 克隆数组
    setTimeout(function(){
        // 执行下一个任务
        var task = tasks.shift();
        task.apply(null, args || []);
        // 检查是否还有其他任务 
        if (tasks.length > 0){
            setTimeout(arguments.callee, 25);
        } else {
            callback();
        }
    }, 25);
}
```

```JavaScript
// 使用
function saveDocument(id){
    var tasks = [openDocument, writeText, closeDocument, updateUI];
    multistep(tasks, [id], function(){
        alert("Save completed!");
    });
}
```

### 6.2.5 记录代码运行时间

有时每次只执行一个任务的效率不高。如果每次定时器只处理一项，且每项之间产生25毫秒的延迟，这对性能来说是个浪费。如果每个定时器能批处理多个任务，那么效率就上来了。
还记得前面讨论过JavaScript连续运行100毫秒，不会影响用户体验。建议把这个数字减半，不让JavaScript代码持续运行`50毫秒`，这样确保代码永远不会影响用户体验。

```JavaScript
function timedProcessArray(items, process, callback){
    var todo = items.concat(); // 克隆原始数组
    setTimeout(function(){
        var start = +new Date(); // +可以把Date转换成数字，方便计算
        do {
            process(todo.shift());
        } while (todo.length > 0 && (+new Date() - start < 50));
        if (todo.length > 0){
            setTimeout(arguments.callee, 25);
        } else {
            callback(items);
        } 
    }, 25);
}
```

该函数增加了一个`do-while`循环，保证一个定时器里任务总耗时不超过50毫秒，既增加了效率有不影响用户体验。

### 6.2.6 定时器与性能

定时器会让JavaScript的代码整体性能发生翻天覆地的变化，但过度使用也会对性能造成负面影响。保证同一时间只有一个定时器存在，才能避免定时器导致的性能问题。

## 6.3 Web Workers

自JavaScript诞生以来，还没有办法在浏览器UI线程外运行代码。Web Workers改变了这一状况，它引入了一个接口，能使代码运行且不占用浏览器UI线程时间。

### 6.3.1 Worker运行环境

由于Web Workers没有绑定UI线程，意味着它们不能访问浏览器的许多资源。Web Workers从外部线程中修改DOM会导致用户界面出现错误，但是每个Web Worker都有自己的全局运行环境：

1. 一个`navigator`对象，只包含四个属性：appName,appVersion,userAgent,和platform；
2. 一个`location`对象（和window里的一样，只是所有属性都是只读的）；
3. 一个`self`对象指向全局worker对象；
4. 一个`importScripts()`方法，使工人线程可以加载外部 JavaScript 文件；
5. 所有`ECMAScript`对象，诸如Object，Array，Data，等等；
6. `XMLHttpRequest`构造器；
7. `setTimeout()`和`setInterval()`方法；
8. `close()`方法可立即Worker运行。

由于Web Workers有着不同的全局运行环境，因此无法从JavaScript代码中创建。需要创建一个完全独立的JavaScript文件，包含了需要在Worker中运行的代码。然后引入这个文件：

```JavaScript
var worker = new Worker("code.js");
```

此代码一旦执行，将为这个文件创建一个新的线程和一个新的Worker运行环境。该文件会被异步下载，直到文件下载并执行完成后才会启动Worker。

### 6.3.2 与Worker通信

Worker与网页代码通过事件接口进行通信。网页代码通过`postMessage()`方法给Worker传递数据；Worker有一个`onmessage`事件处理器接收信息，并可以用自己的`postMessage()`方法把数据传回网页代码。

```JavaScript
// 网页代码
var worker = new Worker("code.js");
worker.onmessage = function(event){
    alert(event.data);
};
worker.postMessage("Nicholas");
```

```JavaScript
// code.js内部代码
self.onmessage = function(event){
    self.postMessage("Hello, " + event.data + "!");
};
```

> 注意：消息系统是网页和Worker通信的唯一途径。

### 6.3.3 加载外部文件

Worker通过`importScripts()`方法加载外部JavaScript文件，该方法接收一个或多个JavaScript文件URL作为参数。

```JavaScript
// code.js内部代码
importScripts("file1.js", "file2.js");
self.onmessage = function(event){
    self.postMessage("Hello, " + event.data + "!");
};
```

### 6.3.4 实际应用

Web Workers适用于哪些处理纯数据，或者与浏览器UI无关的长时间运行脚本。比如：

1. 编码/解码大字符串；
2. 复杂数学运算（包括图像或视频处理）；
3. 大数组排序。

> 性能优化：任何超过100毫秒的处理过程，都应考虑Worker方案而不是定时器方案。前提是浏览器支持Web Workers。

**欢迎大佬纠错指导，欢迎交流学习。**

>GitHub：[https://github.com/Code4GL](https://github.com/Code4GL)
公众号：code_everything
QQ：771841496
邮箱：guanli1991@163.com

![code_everything](/images/code_everything.jpg)
