---
title: 05《高性能JavaScript》第五章 字符串和正则表达式
date: 2020-12-23 14:34:48
excerpt: 《高性能JavaScript》第五章 字符串和正则表达式
categories: JavaScript
tags:
    - 高性能JavaScript
    - 原生JS
---

![image](https://upload-images.jianshu.io/upload_images/18236822-ed739b07cf9eed5e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 5.1 字符串连接

![常见字符串合并方法](https://upload-images.jianshu.io/upload_images/18236822-a4173ea40035a0df.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

当连接少量字符串时，这些方法运行速度都很快。随着要合并的字符串长度和数量增加时，有些方法就开始展现优势。

### 5.1.1 加(+)和加等(+=)操作符

这是一个常见的字符串连接：

```JavaScript
str += "one" + "two";
```

这段代码运行时，会经历四个步骤：

1. 在内存中创建一个临时字符串；
2. 连接后的“onetwo”被赋值给该临时字符串；
3. 临时字符串与`str`当前的值连接；
4. 结果赋值给`str`。

以下优化会提速10%~40%，原因是这样做避免使用了临时字符串：

```JavaScript
str += "one";
str += "two";
等价于
str = str + "one" + "two";
等价于
equivalent to str = ((str + "one") + "two")
```

![内存使用示例](https://upload-images.jianshu.io/upload_images/18236822-a0abb61cf5f972af.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 5.1.2 数组项合并

`Array.prototype.join()`方法将数组中的所有元素合并成一个字符串，它接收一个字符串参数为分隔符插入每个元素中间。如果传入的分隔符为空字符`""`，则可以将数组的所有元素连接起来。
> 注意：在大多数浏览器中，数组项合并比其他字符串连接方法更慢，而在IE 7以及更早的版本上合并大量字符串唯一高效的方法，谨慎使用。

### 5.1.3 String.prototype.concat

字符串原生方法`concat`能接收任意数量的参数，并将每一个参数附加到所调用的字符串上。

```JavaScript
// 附加一个字符串
str = str.concat(s1);
// 附加两个字符串
str = str.concat(s1, s2, s3);
// 如果传递一个数组，可以附加数组中的所有字符串
str = String.prototype.concat.apply(str, array);
```

> 注意：遗憾的是，多数情况下`concat`比使用简单的`+`和`+=`稍慢，并且还潜伏着灾难性的性能问题，不建议使用。

## 5.2 正则表达式优化

本节包含了大量概念以及理解性内容，建议阅读原书。此次只做简单的介绍。

### 5.2.1 正则表达式工作原理

1. 第一步：编译
 当你创建了一个正则表达式对象之后（使用一个正则表达式直接量或者RegExp构造器），浏览器检查你的模板有没有错误，然后将它转换成一个本机代码例程，用于执行匹配工作。如果你将正则表达式赋给一个变量，你可以避免重复执行此步骤。
2. 第二步：设置起始位置
 当一个正则表达式投入使用时，首先要确定目标字符串中开始搜索的位置。它是字符串的起始位置，或者由正则表达式的 lastIndex 属性指定，但是当它从第四步返回到这里的时候（因为尝试匹配失败），此位置将位于最后一次尝试起始位置推后一个字符的位置上。
浏览器厂商优化正则表达式引擎的办法是，在这一阶段中通过早期预测跳过一些不必要的工作。例如，如果一个正则表达式以^开头，IE 和 Chrome 通常判断在字符串起始位置上是否能够匹配，然后可避免愚蠢地搜索后续位置。另一个例子是匹配第三个字母是 x 的字符串，一个聪明的办法是先找到 x，然后再将起始位置回溯两个字符（例如，最近的 Chrome 版本实现了这种优化）。
3. 第三步：匹配每个正则表达式的字元
 正则表达式一旦找好起始位置，它将一个一个地扫描目标文本和正则表达式模板。当一个特定字元匹配失败时，正则表达式将试图回溯到扫描之前的位置上，然后进入正则表达式其他可能的路径上。
4. 第四步：匹配成功或失败
 如果在字符串的当前位置上发现一个完全匹配，那么正则表达式宣布成功。如果正则表达式的所有可能路径都尝试过了，但是没有成功地匹配，那么正则表达式引擎回到第二步，从字符串的下一个字符重新尝试。只有字符串中的每个字符（以及最后一个字符后面的位置）都经历了这样的过程之后，还没有成功匹配，那么正则表达式就宣布彻底失败。

### 5.2.2 理解回溯

这个表达式匹配“hello hippo”或“happy hippo”。

```text
/h(ello|appy) hippo/.test("hello there, happy hippo");
```

回溯过程如下：

![分支回溯](https://upload-images.jianshu.io/upload_images/18236822-0967dead5e8cc4af.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

贪婪量词和惰性量词回溯：

![贪婪量词和惰性量词回溯](https://upload-images.jianshu.io/upload_images/18236822-3121a5a4ea9f44e3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 5.2.3 提高效率的方法

1. 关注如何让匹配更快失败；
2. 表达式以简单、必须的字元开始；
3. 使用量词模式，使它们后面的字元互斥；
4. 减少分支数量，缩小分支范围；
5. 使用非捕获组；
6. 只捕获感兴趣的文本以减少后处理；
7. 暴露必需的字元；
8. 使用合适的量词；
9. 把表达式赋值给变量并重用；
10. 将复杂的表达式拆分为简单的片段。

**欢迎大佬纠错指导，欢迎交流学习。**

>GitHub：[https://github.com/Code4GL](https://github.com/Code4GL)
公众号：code_everything
QQ：771841496
邮箱：guanli1991@163.com

![code_everything](/images/code_everything.jpg)
