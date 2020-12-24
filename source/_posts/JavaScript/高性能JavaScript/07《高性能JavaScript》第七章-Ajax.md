---
title: 07《高性能JavaScript》第七章 Ajax
date: 2020-12-23 14:40:51
excerpt: Ajax从最基本层面来说，是一种与服务器通信而无须重载页面的方法；数据可以从服务器获取或者发送给服务器。有多种不同的方法建立这种通信通道，每种方法都有各自的优点和限制。
categories: JavaScript
tags:
    - 高性能JavaScript
    - 原生JS
---

![highPerformanceJs](/images/javascript/highPerformanceJs/highPerformanceJs.jpg)

## 7.1 数据传输

Ajax从最基本层面来说，是一种与服务器通信而无须重载页面的方法；数据可以从服务器获取或者发送给服务器。有多种不同的方法建立这种通信通道，每种方法都有各自的优点和限制。

### 7.1.1 请求数据

五种常用技术向服务器请求数据：

1. XMLHttpRequest (XHR)
2. Dynamic script tag insertion
3. iframes
4. Comet
5. Multipart XHR

现代高性能JavaScript使用的是：`XHR`、`动态脚本注入`和`Multipart XHR`，其他两种往往在极端情况下使用，不做讨论。

#### XMLHttpRequest

XHR是目前最常用的技术，它允许异步发送和接收数据。

```JavaScript
var url = '/data.php';
var params = [
 'id=934875',
 'limit=20'
];
var req = new XMLHttpRequest();
req.onreadystatechange = function() { 
    if (req.readyState === 4) {
    var responseHeaders = req.getAllResponseHeaders(); // 获取响应头信息
    var data = req.responseText; // 获取数据
    // 数据处理。。。
    }
}

req.open('GET', url + '?' + params.join('&'), true);
req.setRequestHeader('X-Requested-With', 'XMLHttpRequest'); // 设置请求头信息
req.send(null); // 发送请求
```

> 注意：由于XHR提供了高级的控制，所以浏览器对其增加了一些限制。你不能使用XHR从外域请求数据，而且低版本IE不仅不支持“流”，也不会提供`readyState`为3的状态。

#### 动态脚本注入

这种技术克服了XHR的最大限制：跨域请求数据。

```JavaScript
var scriptElement = document.createElement('script');
scriptElement.src = 'http://any-domain.com/javascript/lib.js';
document.getElementsByTagName_r('head')[0].appendChild(scriptElement);
```

但是与XHR相比，动态脚本注入提供的控制是有限的。不能设置请求头，只能用`GET`，不能设置超时处理等。最后一点特别重要，不能使用纯`XML`、纯`JSON`或其他格式数据，无论哪种格式，都必须封装在一个回调函数里。

```JavaScript
var scriptElement = document.createElement('script');
scriptElement.src = 'http://any-domain.com/javascript/lib.js';
document.getElementsByTagName_r('head')[0].appendChild(scriptElement); 
function jsonCallback(jsonString) {
    var data = ('(' + jsonString + ')');
    // 数据处理。。。
}
```

> 注意：使用这种技术从那些你无法直接控制的服务器上请求数据时需要小心。JavaScript没有任何权限和访问控制的概念，因此你使用动态脚本注入添加到页面的任何代码都可以完全控制整个页面。引入外部来源的代码时务必多加小心。

#### Multipart XHR

MXHR是一项最新的技术，它允许客户端只用一个HTTP请求就可以从服务器向客户端传送多个资源。它通过在服务器将资源打包成双方约定的字符串分割的长字符串并发送给客户端。
这个技术有一些缺点，其中最大的缺点是这种方式获取的资源不能被浏览器缓存。但某些情况下，MXHR依然能显著提高整体性能：

1. 页面包含了大量其他地方用不到的资源，即不需要缓存，尤其是图片；
2. 网站已经在每个页面中使用一个独立打包的JavaScript或CSS文件以减少HTTP请求；因为对每个页面来说这些文件都是唯一的，所以不需要缓存中读取数据，触发重载页面；

### 7.1.2 发送数据

有时并不关心接收数据，只需要向服务器发送数据。当数据只需要发送到服务器时，有两种技术使用广发：`XHR`和`信标（beacons）`。

1. XMLHttpRequest
虽然XHR主要用于从服务器获取数据，但也可以将数据传回服务器。
`GET`：适用于传输少量数据。因为对于少量数据而言，GET请求只需要往服务器发送一个数据包；而POST至少要发送两个数据包（头信息和正文）。
`POST`：POST适合发送大数据到服务器。因为它不关心额外数据包的数量；另一个原因是IE对URL长度有限制（2048个字符），URL太长了就不能使用GET请求。
2. Beacons
这个技术非常类似动态脚本注入。它使用JavaScript创建一个新的`Image`对象，并把`src`属性设外服务器上脚本的URL。该URL包含了我们要通过GET请求传回的键值对数据。

```JavaScript
var url = '/status_tracker.php';
var params = [
 'step=2',
 'time=1248027314'
];
(new Image()).src = url + '?' + params.join('&');
```

服务器接收到数据并保存下来，无须向客户端返回任何信息，因此没有图片会显示出来。

## 7.2 数据格式

当考虑数据传输技术时，我们必须考虑：功能集、兼容性、性能及方向。而当考虑数据格式时，唯一需要比较的标准就是：速度。

### 7.2.1 XML

当Ajax最开始流行时，它选择XML作为数据格式。当时它有很多优势：极佳的通用性、格式严格、易于操作。那时JSON还没有正式作为交换格式，几乎所有的服务端语音都有操作XML的类库。

```xml
<?xml version="1.0" encoding='UTF-8'?>
<users total="4">
    <user id="1"> 
        <username>alice</username>
        <realname>Alice Smith</realname>
        <email>alice@alicesmith.com</email>
    </user>
    <user id="2">
        <username>bob</username>
        <realname>Bob Jones</realname>
        <email>bob@bobjones.com</email>
    </user>
    <user id="3">
        <username>carol</username>
        <realname>Carol Williams</realname>
        <email>carol@carolwilliams.com</email>
    </user>
    <user id="4">
        <username>dave</username>
        <realname>Dave Johnson</realname>
        <email>dave@davejohnson.com</email>
    </user>
</users>
```

可以对其进行优化：

```xml
<?xml version="1.0" encoding='UTF-8'?>
<users total="4">
    <user id="1-id001" username="alice" realname="Alice Smith" email="alice@alicesmith.com" />
    <user id="2-id001" username="bob" realname="Bob Jones" email="bob@bobjones.com" />
    <user id="3-id001" username="carol" realname="Carol Williams" email="carol@carolwilliams.com" />
    <user id="4-id001" username="dave" realname="Dave Johnson" email="dave@davejohnson.com" />
</users>
```

> 性能优化：简化版的XML更有效率，但是比那些最快的格式依然慢上一个数量级。在高性能Ajax中，XML没有立足之地。

### 7.2.2 JSON

JSON是一种JavaScript对象和数组直接量编写的轻量级且易于解析的数据格式。

```json
[
    {"id":1, "username":"alice", "realname": "Alice Smith", "email":"alice@alicesmith.com"},
    {"id":2, "username":"bob", "realname": "Bob Jones", "email":"bob@bobjones.com"},
    {"id":3, "username":"carol", "realname": "Carol Williams","email":"carol@carolwilliams.com"},
    {"id":4, "username":"dave", "realname": "Dave Johnson", "email":"dave@davejohnson.com"}
]
```

可以进行简化：

```json
[
    { "i": 1, "u": "alice", "r": "Alice Smith", "e": "alice@alicesmith.com" },
    { "i": 2, "u": "bob", "r": "Bob Jones", "e": "bob@bobjones.com" },
    { "i": 3, "u": "carol", "r": "Carol Williams", "e": "carol@carolwilliams.com" },
    { "i": 4, "u": "dave", "r": "Dave Johnson", "e": "dave@davejohnson.com" }
]
```

进一步简化：

```json
[
    [ 1, "alice", "Alice Smith", "alice@alicesmith.com" ],
    [ 2, "bob", "Bob Jones", "bob@bobjones.com" ],
    [ 3, "carol", "Carol Williams", "carol@carolwilliams.com" ],
    [ 4, "dave", "Dave Johnson", "dave@davejohnson.com" ]
]
```

在不断的简化过程中，可读性越来越差，也更脆弱。但是文件尺寸却小得多：大约只有标准JSON的一半。解析也必须按照数据的顺序进行。

```JavaScript
function parseJSON(responseText) {
    var users = [];
    var usersArray = ('(' + responseText + ')');
    for (var i = 0, len = usersArray.length; i < len; i++) {
        users[i] = {
            id: usersArray[i][0],
            username: usersArray[i][1],
            realname: usersArray[i][2],
            email: usersArray[i][3]
        };
    }
    return users;
}
```

### 7.2.3 HTML

一种可考虑的技术是在服务器端构建好整个HTML再传回客户端，JavaScript可以通过`innerHTML`属性把它插入页码相应位置。但问题是HTML是一种臃肿的数据格式，比XML更复杂。因此，作为一种数据格式，它既缓慢，又臃肿。

### 7.2.4 自定义格式

理想的数据格式应该只包含必要的结构，以便你可以分解出每个独立的字段。

```json
1:alice:Alice Smith:alice@alicesmith.com;
2:bob:Bob Jones:bob@bobjones.com; 
3:carol:Carol Williams:carol@carolwilliams.com;
4:dave:Dave Johnson:dave@davejohnson.com
```

这种格式非常简洁，“数据/结构”比例相当高，比其他任何格式都高（除了纯文本）。只需要简单地调用字符串`split()`方法并传入分隔符作为参数即可，复杂一点的加上循环就好了。JavaScript中的循环和`split()`方法都是相当快的。

### 7.2.5 数据格式总结

通常来说，数据格式越轻量级越好，`JSON`和`字符分隔的自定义`是最好的。

## 7.3 Ajax性能指南

### 7.3.1 缓存数据

`最快的Ajax请求就是没有请求。`可以有两种方法来实现：

1. 在服务端，设置HTTP头信息以确保响应会被浏览器缓存；
2. 在客户端，把获取到的信息存储在本地，以避免再次请求。

### 7.3.2 了解Ajax类库的局限

所有得JavaScript库都允许你访问一个Ajax对象，它屏蔽浏览器之间的差异，给你一个统一的接口。大多数情况下这非常好，因为它使你可以关注你的项目，而不是那些古怪的浏览器上XHR的工作细节。然而，为了给你一个统一的接口，这些库必须简化接口，因为不是所有浏览器都实现了每个功能。这使得你不能访问XMLHttpRequest的完整功能。
> 性能优化：直接操作XHR对象减少了函数的开销，进一步提升了性能。但是，如果放弃Ajax类库，那么你可能在一些古怪的浏览器上遇到一些问题。

**欢迎大佬纠错指导，欢迎交流学习。**

>GitHub：[https://github.com/Code4GL](https://github.com/Code4GL)
公众号：code_everything
QQ：771841496
邮箱：guanli1991@163.com

![code_everything](/images/code_everything.jpg)
