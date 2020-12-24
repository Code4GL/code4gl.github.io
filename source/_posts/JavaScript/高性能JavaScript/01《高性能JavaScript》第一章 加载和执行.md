---
title: 01《高性能JavaScript》第一章 加载和执行
date: 2020-12-23 14:06:00
excerpt: 《高性能JavaScript》第一章 加载和执行
categories: JavaScript
tags:
    - 高性能JavaScript
    - 原生JS
---

![highPerformanceJs](/images/javascript/highPerformanceJs/highPerformanceJs.jpg)

## 1.1 脚本位置

- 描述

将所有`<script>`标签尽可能放到`<body>`标签底部，以减少对整个页面下载的影响。

- 原因

UI渲染和JavaScript运行共用一个线程，`<script>`标签运行时，线程就被占用，直到JavaScript脚本运行完毕，才会继续进行UI渲染。此时，UI渲染和用户交互就处于阻塞状态。

- 反例

```html
<html>
    <head>
        <title>脚本位置</title>
        <script type="text/javascript" src="file.js"></script> <!--Bad，不推荐-->
    </head>
    <body>
        <!--其他代码-->
    </body>
</html>
```

- 正例

```html
<html>
    <head>
        <title>脚本位置</title>
    </head>
    <body>
        <!--其他代码-->
        <script type="text/javascript" src="file.js"></script> <!--Good，推荐-->
    </body>
</html>
```

## 1.2 组织脚本

- 描述

若需要引用多个`<script>`，将多个`<script>`标签合并到一个`<script>`中再引用，可以减少性能消耗。

- 原因

每个`<script>`初始下载时都会阻塞页面渲染，`<script>`标签数量越多，阻塞次数越多。不仅仅针对外链脚本，内嵌脚本同样适用。对于外链脚本，还有这样的提升：Http请求会带来额外的性能开销，因此下载单个100KB的文件比下载4个25KB的文件更快。`<script>`文件合并可以通过打包工具来实现。
> 提示：把一段内嵌脚本放在引用外链样式表的`<link>`标签之后会导致页面阻塞去等待样式表的下载，这样做是为了确保内嵌脚本在执行时能获得最精确的样式信息。因此，建议永远不要把内嵌脚本紧跟在`<link>`标签后面。

- 反例

```html
<html>
    <head>
        <title>组织脚本</title>
        <link rel="stylesheet" type="text/css" href="styles.css"></link>
        <script type="text/javascript" src="file.js"></script> <!--Bad，不推荐-->
    </head>
    <body>
        <!--其他代码-->
        <script type="text/javascript" src="file1.js"></script> <!--Bad，不推荐-->
        <script type="text/javascript" src="file2.js"></script> <!--Bad，不推荐-->
    </body>
</html>
```

- 正例

```html
<html>
    <head>
        <title>组织脚本</title>
    </head>
    <body>
        <!--其他代码-->
        <script type="text/javascript" src="files.js"></script> <!--Good，推荐-->
    </body>
</html>
```

## 1.3 无阻塞的脚本

### 1.3.1 延迟的脚本

- 描述

HTML4为`<script>`标签定义了一个扩展属性：defer。该属性只有IE 4+和Firefox 3.5+的浏览器支持，所以它不是一个理想的解决方案。

- 原因

Defer属性指明本元素所含脚本不会修改DOM，因此代码能安全地延迟执行。页面解析到带有defer属性的`<script>`时开始下载，但并不会执行，直到DOM加载完成（onload事件被触发前），文件下载并不会阻塞浏览器其他进程。
> 注意：HTML5规范中引入了async属性，用于异步加载脚本。async与defer的相同点都是采用并行下载，在下载过程中不会产生阻塞。区别在于执行时机，async加载完成之后自动执行，defer需要等待页面完成后执行。

- 反例

```html
<html>
    <head>
        <title>延迟的脚本</title>
    </head>
    <body>
        <!--其他代码-->
        <script type="text/javascript" src="file.js"></script> <!--Bad，不推荐-->
    </body>
</html>
```

- 正例

```html
<html>
    <head>
        <title>延迟的脚本</title>
    </head>
    <body>
        <!--其他代码-->
        <script type="text/javascript" src="file.js" defer></script> <!--Good，推荐-->
    </body>
</html>
```

### 1.3.2 动态脚本元素

- 描述

使用DOM创建`<script>`标签加载脚本文件。这种技术的重点在于：无论何时启动下载，文件的下载和执行过程不会阻塞页面其他进程。

- 原因

DOM允许JavaScript创建HTML中的几乎所有内容。
> 提示：用这种方式创建的`<script>`标签添加到`<head>`标签里比添加到`<body>`里更保险，尤其是在页面加载过程中执行代码时更是如此。

- 正例

```javascript
// 用此方法加载file.js文件即可
function loadScript(url,callback){
    var script = document.createElement("script");
    script.type = "text/javascript";
    if(script.readyState){ // IE浏览器
        script.onreadystatechange = function(){
            if(script.readyState == "loaded" || script.readyState == "complete"){
                script.onreadystatechange = null;
                callback();
            }
        };
    }else{ // 其他浏览器
        script.onload = function(){
            callback();
        };
    }
    script.src = url;
    document.getElementsByTagName("head")[0].appendChild(script);
}
```

### 1.3.3 XHR脚本注入

- 描述

使用XML HttpRequest对象获取脚本并注入页面。

- 原因

通过XHR发送GET请求获取脚本文件，再通过创建动态`<script>`元素将代码注入页面。
> 优点：1、可以加载JavaScript代码但不立即执行。由于是在`<script>`标签之外返回的，因此下载后不会自动执行；2、同样的代码在所有主流浏览器中都能运行。
局限：JavaScript文件必须与所有请求页面处于相同的域，这就意味着JavaScript文件不能从CDN下载。因此，大型应用通常不会采用XHR脚本注入技术。

- 正例

```javascript
var xhr = new XMLHttpRequest();
xhr.open("get","file.js",true);
xhr.onreadystatechange = function(){
    if(xhr.readyState == 4){
        if(xhr.status >= 200 || xhr.status < 300 || xhr.status == 304){ // 304表示从缓存中读取
            var script = document.createElement("script");
            script.type = "text/javascript";
            script.text = xhr.responseText;
            document.body.appendChild(script);
        }
    }
}
xhr.send(null);
```

### 1.3.4 推荐的无阻塞模式

- 描述

1、先添加动态加载所需的代码；2、然后加载初始化页面所需的剩下的代码。

- 原因

因为第一部分代码尽量精简，甚至可能只包含loadScript()函数，它下载执行都很快，所以不会对页面有太多影响。一旦初始化代码就位，就用它来加载剩余的JavaScript。

- 正例

```html
<html>
    <head>
        <title>推荐的无阻塞模式</title>
    </head>
    <body>
        <!--其他代码-->
        <script type="text/javascript" src="loader.js"></script> <!--提供loadScript方法-->
        <script type="text/javascript">
            loadScript("file.js",function(){
                Application.init();
            });
        </script> <!--Good，推荐-->
    </body>
</html>
```

**欢迎大佬纠错指导，欢迎交流学习。**

>GitHub：[https://github.com/Code4GL](https://github.com/Code4GL)
公众号：code_everything
QQ：771841496
邮箱：guanli1991@163.com

![code_everything](/images/code_everything.jpg)
