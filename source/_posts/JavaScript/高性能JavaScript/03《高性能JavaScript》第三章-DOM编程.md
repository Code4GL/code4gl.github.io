---
title: 03《高性能JavaScript》第三章 DOM编程
date: 2020-12-23 14:21:42
excerpt: 文档对象模型（DOM）是一个独立于语言的，用于操作`XML`和`HTML`文档操作的程序接口（API）。在浏览器中，主要用来与`HTML`文档打交道，同样也用在Web程序中获取`XML`文档，并使用DOM API来访问文档中的数据。
categories: JavaScript
tags:
    - 高性能JavaScript
    - 原生JS
---

![highPerformanceJs](/images/javascript/highPerformanceJs/highPerformanceJs.jpg)

## 3.1 浏览器中的DOM

文档对象模型（DOM）是一个独立于语言的，用于操作`XML`和`HTML`文档操作的程序接口（API）。在浏览器中，主要用来与`HTML`文档打交道，同样也用在Web程序中获取`XML`文档，并使用DOM API来访问文档中的数据。

### 天生就慢

简单理解，两个相互独立的功能只要通过接口彼此连接，就会产生消耗。将DOM和JavaScript各自想象成一个岛屿，它们之间用收费桥梁连接，JavaScript每次访问DOM都要途径这座桥，并交纳过桥费。访问DOM次数越多，费用就越高。
> 性能优化：尽可能减少过桥的次数，努力呆在JavaScript岛上。

## 3.2 访问与修改

- 描述
访问DOM元素是有代价的，修改元素则更为昂贵，尤其对HTML集合循环操作。
- 原因
它会导致浏览器重新计算页面的几何变化。
- 反例

```JavaScript
// 这个函数每次循环该元素都被访问两次：一次读取innerHTML属性值，另一次是重写它。
function innerHTMLLoop() {
    for (var count = 0; count < 15000; count++) {
        document.getElementById('here').innerHTML += 'a'; 
    }
}
```

- 正例

```JavaScript
// 用局部变量存储修改中的内容，在循环结束后一次性写入。
function innerHTMLLoop2() {
    var content = '';
    for (var count = 0; count < 15000; count++) {
        content += 'a';
    }
    document.getElementById('here').innerHTML += content;
}
```

### 3.2.1 innerHTML对比DOM方法

修改页面区域的最佳方案？
`innerHTML`：非标准但支持良好；
`DOM API`：类似`document.createElement()`原生DOM方法。
答案：性能相差无几。除开WebKit内核（Chrome和Safari）之外的所有浏览器中，`innerHTML`会更快一些。
> 性能优化：如果对一个性能有着苛刻要求的操作中更新一大段HTML，推荐使用innerHTML，因为它在绝大部分浏览器中运行的更快。但对于大多数日常操作而言，没有太大的区别。

### 3.2.2 节点克隆

使用DOM方法更新页面内容的另一个途径是克隆已有元素，而不是创建新元素。即使用`element.cloneNode()`替代`document.createElement()`。
> 性能优化：大多数浏览器中，节点克隆都更有效率，但效果也不是特别明显。

### 3.2.3 HTML集合

HTML集合是包含了DOM节点引用的类数组对象。HTML集以一种“假定实时态”实时存在，这意味着当底层文档对象更新时，它也会自动更新。
> 注意：此集合是类似数组的列表，并非数组。但提供了`length`属性，以及以数字索引方式访问列表中的元素

```JavaScript
// 以下方法返回的就是HTML集合。
document.getElementsByName()
document.getElementsByClassName()
document.getElementsByTagName()
document.images // 页面中所有的<img>元素
document.links // 所有的<a>元素
document.forms // 所有表单
document.forms[0].elements // 页面中第一个表单的所有字段
```

> 低效根源：事实上，HTML集合一直与文档保持着连接，每次你需要最新信息时，都会重复执行查询的过程，哪怕是只获取集合中的元素个数（length）也是如此。

#### 3.2.3.1 昂贵的集合

- 描述
将集合复制到数组中，进行操作数组来代替操作集合。
- 原因
读取集合中的`length`比读取普通数组中的`length`要慢很多。
- 反例

```JavaScript
var coll = document.getElementsByTagName('div');
// 慢
function loopCollection() {
    for (var count = 0; count < coll.length; count++) {

    }
}
```

- 正例

```JavaScript
function toArray(coll) {
    for (var i = 0, a = [], len = coll.length; i < len; i++) {
        a[i] = coll[i];
    }
    return a;
}
var coll = document.getElementsByTagName('div');
var ar = toArray(coll);
// 快
function loopCopiedArray() {
    for (var count = 0; count < arr.length; count++) {

    }
}
```

多数情况下只需要遍历一个相对较小的集合，那么缓存`length`就够了。

```JavaScript
// 此函数运行得与loopCopiedArray()一样快。
function loopCacheLengthCollection() {
    var coll = document.getElementsByTagName('div'),
    len = coll.length;
    for (var count = 0; count < len; count++) {

    }
}
```

> 注意：这会因为额外的步骤带来消耗，而且会多遍历一次集合，因此需要评估在特定条件下数组拷贝是否有帮助。

#### 3.2.3.2 访问集合元素时使用局部变量

当遍历一个集合时，第一优化原则是把集合存储在局部变量中，并把`length`缓存在循环外部，然后食用局部变量替代这些需要多次读取的元素。

```JavaScript
// 较慢
function collectionGlobal() {
    var coll = document.getElementsByTagName('div'),
    len = coll.length,
    name = '';
    for (var count = 0; count < len; count++) {
        name = document.getElementsByTagName('div')[count].nodeName;
        name = document.getElementsByTagName('div')[count].nodeType;
        name = document.getElementsByTagName('div')[count].tagName;
    }
    return name;
};
// 较快
function collectionLocal() {
    var coll = document.getElementsByTagName('div'),
    len = coll.length,
    name = '';
    for (var count = 0; count < len; count++) {
        name = coll[count].nodeName;
        name = coll[count].nodeType;
        name = coll[count].tagName;
    }
    return name; 
};
// 最快
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

### 3.2.4 遍历DOM

#### 3.2.4.1 获取DOM元素

你可以使用`childNodes`得到元素集合，或者用`nextSibling`来获取每个相邻元素。
> 性能优化：不同浏览器中，这两种方法运行时间几乎相等。在性能要求极高时，在老版本IE中更推荐`nextSibling`方法来查找DOM节点。

#### 3.2.4.2 元素节点

DOM元素诸如`childNodes`、`firstChild`、`nextSibling`并不区分元素节点和其他类型节点。多数情况下我们只需要访问元素节点，因此可能需要检查和过滤掉非元素节点。这些类型检查和过滤其实是比必要的操作。

![能区分元素节点和其他节点的DOM属性](https://upload-images.jianshu.io/upload_images/18236822-841abaafbe9e7c9f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> 性能优化：使用左边的属性代替右边，效率更快。

#### 3.2.4.3 选择器API

使用CSS选择器也是一种定位节点的便利途径，因为开发者都熟悉CSS。最新浏览器提供了一个名为`querySelectorAll()`的原生DOM方法，这比使用JavaScript和DOM来遍历查找元素要快很多。

```JavaScript
// 使用querySelectorAll()
var elements = document.querySelectorAll('#menu a');
// 不使用querySelectorAll()
var elements = document.getElementById('menu').getElementsByTagName_r('a');
```

如果要处理大量组合查询，使用`querySelectorAll()`会更有效率。

```JavaScript
var errs = document.querySelectorAll('div.warning, div.notice');
```

还可以使用另一个方法`querySelector()`来获取第一个匹配的节点。
> 注意：使用之前应该先检查一下浏览器是否支持，如果不支持，也许应该把库升级到最新版本了。

## 3.3 重绘与重排

浏览器下载完页面的所有组件：HTML标签、JavaScript、CSS、图片后，会解析并生成两个内部数据结构：
`DOM树`：表示页面结构。DOM树中的每一个需要显示的节点在渲染中至少存在一个对应的节点，隐藏的DOM元素在渲染树中没有对应的节点；
`渲染树`：表示DOM节点如何显示。渲染树中的节点被称为“帧”或“盒”。
一旦DOM树和渲染树构建完成，浏览器就开始绘制页面元素了。当DOM的变化影响了元素的几何属性（宽和高）时，浏览器就需要重新计算元素的集合属性，同样其他元素的几何属性和位置也会受到影响。

重排（reflow）：浏览器会使渲染树中受到影响的部分失效，并重新构造渲染树的过程；
重绘（repaint）：完成重排后，浏览器会冲洗绘制受影响的部分到屏幕中的过程。
> 性能优化：重绘和重排操作的代价都是昂贵的，它们会导致Web应用的UI反应迟钝。所以应尽可能减少这类操作的发送。

### 3.3.1 重排何时发生

当页面布局和几何属性改变时就需要重排，以下情况会发生重排：

1. 添加或删除可见的DOM元素；
2. 元素位置改变；
3. 元素尺寸改变；
4. 内容改变；
5. 页面渲染器初始化；
6. 浏览器窗口尺寸改变。

### 3.3.2 渲染树变化的排队与刷新

由于每次重排都会产生计算消耗，大多数浏览器通过队列化修改并批量执行来优化重排过程。但以下获取布局信息的操作会强制刷新队列：

```JavaScript
offsetTop, offsetLeft, offsetWidth, offsetHeight
scrollTop, scrollLeft, scrollWidth, scrollHeight
clientTop, clientLeft, clientWidth, clientHeight
getComputedStyle() (currentStyle in IE)（在 IE 中此函数称为 currentStyle）
```

> 性能优化：应避免使用以上的属性修改样式。

### 3.3.3 最小化重绘和重排

#### 3.3.3.1 改变样式

- 描述
重排和重绘的代价可能非常昂贵，因此减少此类操作会提升性能。
- 反例

```JavaScript
var el = document.getElementById('mydiv');
el.style.borderLeft = '1px';
el.style.borderRight = '2px';
el.style.padding = '5px';
```

-正例

```JavaScript
// 合并改变，一次处理。
var el = document.getElementById('mydiv');
el.style.cssText = 'border-left: 1px; border-right: 2px; padding: 5px;';
// 保留现有样式。
el.style.cssText += '; border-left: 1px;';
// 修改样式名称，尽管可能带来轻微的性能影响，因为改变类时需要检查级联样式。
var el = document.getElementById('mydiv');
el.className = 'active';
```

#### 3.3.3.2 批量修改DOM

通过以下步骤可以减少一系列DOM操作带来重绘和重排的次数：

1. 使元素脱离文档流（触发重排）；
2. 对其引用多重改变；
3. 把元素带回文档中（触发重排）。

使DOM脱离文档的三种方法：

1. 影藏元素，修改应用，重新显示；
2. 使用文档片段在当前DOM之外构建一个子树，再把它拷贝回文档中；
3. 将原始元素拷贝到一个脱离文档的节点中，修改副本，完成后再替换原始元素。

```JavaScript
// 更新节点数据通用函数
function appendDataToElement(appendToElement, data) {
    var a, li;
    for (var i = 0, max = data.length; i < max; i++) {
        a = document.createElement('a');
        a.href = data[i].url;
        a.appendChild(document.createTextNode(data[i].name));
        li = document.createElement('li');
        li.appendChild(a);
        appendToElement.appendChild(li); 
    }
};
// 第一种方案
var ul = document.getElementById('mylist');
ul.style.display = 'none';
appendDataToElement(ul, data);
ul.style.display = 'block';
// 第二种方案
var fragment = document.createDocumentFragment();
appendDataToElement(fragment, data);
document.getElementById('mylist').appendChild(fragment);
// 第三种方案
var old = document.getElementById('mylist');
var clone = old.cloneNode(true);
appendDataToElement(clone, data);
old.parentNode.replaceChild(clone, old);
```

> 性能优化：推荐尽可能使用第二种方案：文档片段。因为它们所产生的DOM和重排次数最少。

### 3.3.4 缓存布局信息

- 描述
最好的做法是尽量减少布局信息的获取次数，获取后把它赋值给局部变量，然后再操作局部变量。
- 原因
当你查询布局信息时，浏览器会返回最新值，会刷新队列并应用所有变更。
- 反例

```JavaScript
// 低效的
myElement.style.left = 1 + myElement.offsetLeft + 'px';
myElement.style.top = 1 + myElement.offsetTop + 'px';
if (myElement.offsetLeft >= 500) {
    stopAnimation();
}
```

- 正例

```JavaScript
current++
myElement.style.left = current + 'px';
myElement.style.top = current + 'px';
if (current >= 500) { 
    stopAnimation();
}
```

### 3.3.5 让元素脱离动画流

用展开/折叠的方式来显示和影藏部分页面是一种常见的交互模式。它通常包括展开区域的几何动画，并将页面其他部分推向下方。
使用以下步骤可以避免页面中大部分重排：

1. 使用绝对位置定位页面上的动画元素，将其脱离文档流；
2. 让元素动起来。当它扩大时覆盖部分页面。但是这只是页面的一个小区域重绘过程，不会产生重排和重绘页面的大部分内容；
3. 当动画结束时回复定位，从而只会下移一次文档的其他元素。

### 3.3.6 IE和:hover

从IE 7开始，IE允许使用`:hover`这个CSS伪选择器。然而，当你有大量元素使用`:hover`，那么会降低响应速度。此问题在IE 8上更为明显。
> 性能优化：在元素很多的情况下，比如表格，应该避免使用`:hover`来高亮显示鼠标所在行。

## 3.4 事件委托

根据DOM标准，每个时间都要经历三个阶段：

1. 捕获；
2. 到达目标；
3. 冒泡。
事件委托只需要冒泡即可。使用时事件代理，只需要给外层元素绑定一个处理器，就可以处理在其子元素上触发的所有时间。

![一个DOM树的例子](https://upload-images.jianshu.io/upload_images/18236822-da4ed35aa134f0c1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

当用户点击了“menu #1”链接，点击事件首先被`<a>`元素收到。然后它沿着 DOM 树冒泡，被`<li>`元素收到，然后是`<ul>`，以此类推，一直到达文档的顶层直至`window`。这使得你可以添加一个事件处理器，来接收所有子节点产生的事件消息。
也许你想为图中的文档提供一个渐进增强的`Ajax`体验。

```JavaScript
document.getElementById('menu').onclick = function(e) {
    // 浏览器target
    e = e || window.event;
    var target = e.target || e.srcElement;
    var pageid, hrefparts;
    // 只关心hrefs，非链接点击则退出
    // exit the function on non-link clicks
    if (target.nodeName !== 'A') {
        return;
    }
    // 从链接中找到页面ID
    hrefparts = target.href.split('/');
    pageid = hrefparts[hrefparts.length - 1];
    pageid = pageid.replace('.html', ''); 
    // 更新页面
    ajaxRequest('xhr.php?page=' + id, updatePageContents);
    // 浏览器组织默认行为并取消冒泡
    if (typeof e.preventDefault === 'function') {
        e.preventDefault();
        e.stopPropagation();
    } else {
        e.returnValue = false;
        e.cancelBubble = true;
    }
};
```

如你所见，事件委托技术并不复杂，你只需检查事件是否来自你所预期的元素。如果去掉跨浏览器兼容部分，代码会更简洁。
跨浏览器兼容部分包括：

1. 访问事件对象，并判断事件源；
2. 取消文档树中的冒泡（可选）；
3. 组织默认动作（可选，但本例需要，因为需要捕获并阻止打开链接）。

**欢迎大佬纠错指导，欢迎交流学习。**

>GitHub：[https://github.com/Code4GL](https://github.com/Code4GL)
公众号：code_everything
QQ：771841496
邮箱：guanli1991@163.com

![code_everything](/images/code_everything.jpg)
