---
title: 11JavaScript性能优化汇总
date: 2020-12-23 14:47:28
excerpt: 本文主要针对《高性能JavaScript》书中提到的优化方案进行总结，只列出文中推荐最高效的方案，其他方案的信息请查看之前的文章。
categories: JavaScript
tags:
    - 高性能JavaScript
    - 原生JS
---

![highPerformanceJs](/images/javascript/highPerformanceJs/highPerformanceJs.jpg)

本文主要针对《高性能JavaScript》书中提到的优化方案进行总结，只列出文中推荐最高效的方案，其他方案的信息请查看之前的文章。

## 第一章 加载和执行

### 1.1优化原则

1. 脚本放在`<body>`标签底部加载，即`</body>`前面；
2. 将多个脚本合并到一个脚本中引用，避免一个页面引用多个脚本；
3. 给`<script>`标签添加`defer`属性延迟加载脚本；
4. 根据需求，在合适的时间动态加载各个脚本；

### 1.2代码示例

先添加动态加载所需的代码，然后加载初始化页面所需的剩下的代码。

```JavaScript
<script type="text/javascript">
    function loadScript(url, callback){
        var script = document.createElement("script")
        script.type = "text/javascript";
        if (script.readyState){ // IE
            script.onreadystatechange = function(){
                if (script.readyState == "loaded" || script.readyState == "complete"){
                    script.onreadystatechange = null;
                    callback();
                }
            };
        } else { // Others
            script.onload = function(){
                callback();
            };
        }
        script.src = url;
        document.getElementsByTagName("head")[0].appendChild(script);
    }
    loadScript("the-rest.js", function(){
        Application.init();
    });
</script>
```

## 第二章 数据存取

### 2.1优化原则

1. 若跨作用域的值引用一次以上，请将其存入局部变量中重复使用；
2. 避免使用`with`语句，谨慎使用`try-catch`语句，因为它们会改变作用域链；
3. 除非确实有必要，才去使用动态作用域；
4. 小心使用闭包，防止内存泄漏；
5. 对象成员嵌套越深，读取速度就越慢；
6. 将对象成员、数组元素像跨域变量一样存入局部变量来使用，同时避免用在对象的成员方法上，因为`this`的存在。

### 2.2代码示例

```JavaScript
// 引用一次document
function initUI(){
    var doc = document,
    bd = doc.body,
    links = doc.getElementsByTagName("a"), 
    i = 0,
    len = links.length;
    while(i < len){
        update(links[i++]);
    }
    doc.getElementById("go-btn").onclick = function(){
        start();
    };
    bd.className = "active";
}
```

## 第三章 DOM编程

### 3.1优化原则

1. 减少DOM访问次数，将运算尽量留在JavaScript端来处理；
2. 在对性能要求苛刻的情况下，推荐使用`innerHTML`而非DOM原生方法更新一大段HTML；
3. 使用`cloneNode`代替`createElement`来更新页面，性能稍微好点；
4. 将HTML集合拷贝到数组中，访问它的属性会更快；
5. 使用能区分元素节点的API获取目标节点，避免取出其他类型的节点；
6. 利用CSS选择器，使用`querySelectorAll`或`querySelector`来获取节点效率更快；
7. 尽量少的改变元素的几何属性，防止页面重排或重绘；
8. 避免使用能刷新渲染队列的属性，如`offsetTop`等；
9. 合并多次对DOM和样式的修改，然后一次处理掉；
10. 大量数据下，避免使用`:hover`这个CSS伪选择器；
11. 使用事件委托处理DOM事件，减少事件处理器的数量。

### 3.2代码示例

```JavaScript
function collectionNodesLocal() {
    var coll = document.getElementsByTagName('div'),
    len = coll.length,
    name = '',
    el = null;
    for (var count = 0; count < len; count++) {
        el = coll[count];
        name = el.nodeName;
        name = el.nodeType;
        name = el.tagName;
    }
    return name;
};
```

## 第四章 算法和流程控制

### 4.1优化原则

1. 避免使用`for-in`循环，使用其他循环代替它；
2. 最小化循环中的属性查找，并使用倒序循环；
3. 使用`达夫设备`模式减少迭代次数；
4. 在运行速度要求严格时，避免使用`forEach`循环；
5. 根据离散值和值域，以及条件判断次数，选择使用`if-else`或`switch`；
6. 使用查找表代替条件语句，即将返回值集合存入数组中，操作数据获取对应的值；
7. 递归可能会导致浏览器的调用栈大小限制问题，将递归改为迭代可以避免；
8. 使用Memoization缓存计算结果，避免重复计算。

### 4.2代码示例

```JavaScript
//最小化属性查找并反转：比原始版本快50%~60%
for (var i=items.length; i--; ){
    process(items[i]);
}
var j = items.length;
while (j--){
    process(items[j]]);
}
var k = items.length-1;
do {
    process(items[k]);
} while (k--);
```

```JavaScript
// 升级版Duff’s Device：尽管这种方式用两次循环代替之前一次循环，但移除了循环体中的switch语句，速度比原始更快。
var i = items.length % 8;
while(i){
    process(items[i--]);
}
i = Math.floor(items.length / 8);
while(i){
    process(items[i--]);
    process(items[i--]);
    process(items[i--]);
    process(items[i--]);
    process(items[i--]);
    process(items[i--]);
    process(items[i--]);
    process(items[i--]);
}
```

```JavaScript
// 将返回值存入数组
var results = [result0, result1, result2, result3, result4, result5, result6, result7, result8, result9, result10]
// 利用数组返回当前结果
return results[value];
```

## 第五章 字符串和正则表达式

### 5.1优化原则

1. 以原有字符串作为基础合并字符串，避免使用临时字符串；
2. 避免使用`Array.prototype.join`合并数组中的字符串；
3. 避免使用`String.prototype.concat`合并字符串；
4. 尽可能具体正则表达式中化分隔符之间的字符串匹配模式；
5. 使相邻的字元互斥，避免嵌套量词对同一字符串的相同部分多次匹配，通过重复利用预查的原子组去除不必要的回溯。

### 5.2代码示例

```JavaScript
// 以下优化会提速10%~40%，原因是这样做避免使用了临时字符串。
str += "one";
str += "two";
等价于
str = str + "one" + "two";
等价于
str = ((str + "one") + "two")
```

## 第六章 快速响应的用户界面

### 6.1优化原则

1. 源于Robert Moller 1968年的研究，界面反应时间超过`100毫秒`，用户就会感到自己与界面失去联系；
2. 使用定时器释放UI控制权，定时器延迟至少`25毫秒`，保证UI更新；
3. 使用定时器处理数组循环，增加了处理总耗时，但避免了UI锁定带来糟糕的用户体验；
4. 将耗时较长的任务分割成多个小任务，避免不好的体验；
5. 一个定时器可以同时处理多个任务，但需保证一轮总耗时不超过`50毫秒`；
6. 使用Web Workers在UI线程外执行JavaScript代码。

### 6.2代码示例

```JavaScript
// 一个定时器处理多个小任务
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

## 第七章 Ajax

### 7.1优化原则

1. 使用JSON或JSON-P代替其他数据结构；
2. 自定义数据格式非常简洁，下载和解析都很快，但不易阅读；
3. 使用`Expires`头信息将数据缓存在浏览器；

### 7.2优化原则

```JavaScript
// 简化后的JSON数据
[
    [ 1, "alice", "Alice Smith", "alice@alicesmith.com" ],
    [ 2, "bob", "Bob Jones", "bob@bobjones.com" ],
    [ 3, "carol", "Carol Williams", "carol@carolwilliams.com" ],
    [ 4, "dave", "Dave Johnson", "dave@davejohnson.com" ]
]
```

```JavaScript
// 自定义数据结构
1:alice:Alice Smith:alice@alicesmith.com;
2:bob:Bob Jones:bob@bobjones.com; 
3:carol:Carol Williams:carol@carolwilliams.com;
4:dave:Dave Johnson:dave@davejohnson.com
```

## 第八章 编程实践

### 8.1优化原则

1. 避免使用`eval()`和`Function()`，小心使用`setTimeout()`和`setInterval()`，因为可能会造成双重求值；
2. 使用数组和对象直接量，避免使用`new`；
3. 使用延迟加载或条件预加载来避免重复工作；
4. 优先使用位操作和原生方法来提高性能。

### 8.2代码示例

```JavaScript
var myObject = { // 创建对象
    name: "Nicholas",
    count: 50,
    flag: true,
    pointer: null
};
var myArray = ["Nicholas", 50, true, null]; // 创建数组
```

```JavaScript
// 延迟加载
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
```

```JavaScript
// 条件预加载
var addHandler =
    document.body.addEventListener
    ?
    function(target, eventType, handler){
    target.addEventListener(eventType, handler, false);}
    :
    function(target, eventType, handler){
    target.attachEvent("on" + eventType, handler);};
```

## 第九章 构建并部署高性能JavaScript应用

### 9.1优化原则

1. 使用构建工具自动化构建工程；
2. 使用工具合并多个JavaScript文件；
3. 使用工具预处理JavaScript文件；
4. 使用工具压缩JavaScript；
5. `Accept-Encoding`头进行HTTP压缩；
6. 使用内容分发网络CDN提升响应速度。

## 第十章 工具

主要介绍常见的性能分析和网络分析工具，不过由于时间关系，这些工具大多数已经过时了，在此就不多做介绍了。

**欢迎大佬纠错指导，欢迎交流学习。**

>GitHub：[https://github.com/Code4GL](https://github.com/Code4GL)
公众号：code_everything
QQ：771841496
邮箱：guanli1991@163.com

![code_everything](/images/code_everything.jpg)
