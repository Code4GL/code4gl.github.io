---
title: 04《高性能JavaScript》第四章 算法和流程控制
date: 2020-12-23 14:28:14
excerpt: 《高性能JavaScript》第四章 算法和流程控制
categories: JavaScript
tags:
    - 高性能JavaScript
    - 原生JS
---

![image](https://upload-images.jianshu.io/upload_images/18236822-ed739b07cf9eed5e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 4.1 循环

### 4.1.1 循环的类型

主要有四种循环类型：`for`、`while`、`do-while`和`for-in`。

```JavaScript
// 1、for循环：初始化、前测条件、后执行体和循环体组成。
for (var i=0; i < 10; i++){
 // 循环体
}
// 2、while循环：前测条件和循环体组成。
var i = 0;
while(i < 10){
 // 循环体
 i++;
}
// 3、do-while循环：循环体和后测条件组成。
var i = 0;
do {
 // 循环体
} while (i++ < 10);
// 4、for-in循环：可以枚举任何对象的属性名。
for (var prop in object){
 // 循环体
}
```

> 注意：`for`循环初始化的`var`语句会创建一个函数级别的变量，而不是循环级。由于JavaScript只有函数级作用域，因此`for`循环中定义一个新变量相当于在循环体外定义一个新变量。

### 4.1.2 循环性能

循环的性能提升主要由这几个方面入手：

#### 4.1.2.1 循环的类型

除了`for-in`循环比其他三种循环明显要慢外，其他几种性能都差不多。所以除非明确需要迭代一个属性数量未知的对象，否则避免使用`for-in`循环

#### 4.1.2.2 减少迭代的工作量

将需要多次查找的属性存入局部变量中重复使用，并使用倒序循环

```JavaScript
// 原始版本
for (var i=0; i < items.length; i++){
    process(items[i]);
}
var j=0;
while (j < items.length){
    process(items[j++]]);
}
var k=0;
do {
    process(items[k++]);
} while (k < items.length);
```

```JavaScript
// 最小化属性查找：在大多数浏览器中能节省25%运行时间
for (var i=0, len=items.length; i < len; i++){
    process(items[i]);
}
var j=0,
count = items.length;
while (j < count){
    process(items[j++]]);
}
var k=0,
num = items.length;
do {
    process(items[k++]);
} while (k < num);
```

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

#### 4.1.2.3 减少迭代次数

使用“达夫设备（Duff’s Device）”模式限制迭代次数。Duff’s Device背后的基本理念：每次循环最多可调用8次`process()`，循环迭代的次数为总数除以8

```JavaScript
// 原始版Duff’s Device
var iterations = Math.floor(items.length / 8),
startAt = items.length % 8,
i = 0;
do {
    switch(startAt){
        case 0: process(items[i++]);
        case 7: process(items[i++]);
        case 6: process(items[i++]);
        case 5: process(items[i++]);
        case 4: process(items[i++]);
        case 3: process(items[i++]);
        case 2: process(items[i++]);
        case 1: process(items[i++]);
    }
    startAt = 0;
} while (--iterations);
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

> 性能优化：迭代次数`<1000`，和常规循环结构比性能提升微不足道；迭代次数`>1000`，Duff’s Device模式性能会有明显提升。在`5000`次迭代中，性能比常规提升70%。

### 4.1.3 基于函数的迭代

ECMA-262标准第四版介绍了本地数组对象的一个新方法forEach()。此方法遍历一个数组的所有成员，并在每个成员上执行一个函数。在每个元素上执行的函数作为 forEach()的参数传进去，并在调用时接收三个参数，它们是：数组项的值，数组项的索引，和数组自身。

```JavaScript
items.forEach(function(value, index, array){
    process(value);
});
```

> 性能优化：在所有情况下，基于循环的迭代比基于函数的迭代快`8`倍。因此在运行速度严格要求时，基于函数的迭代不是合适的选择。

## 4.2 条件语句

### 4.2.1 if-else对比switch

多数情况下，`switch`比`if-else`运行的要快，但只有条件数量很大时才快的比较明显。这两句的主要性能区别是：当条件增加时，`if-else`性能负担增加的程度比`switch`要多。
> 性能优化：`if-else`适用于判断两个离散值或几个不同的值域；当判断多个离散值时，`switch`语句是更佳选择。

### 4.2.2 优化if-else

优化目标：最小化到达正确分支前所需判断的条件数量。
优化原则：

1. 将最可能出现的条件放在首位，最小概率出现的的条件放在末尾；
2. 使用`if-else`嵌套：嵌套过程中使用`二分法`把值域分成一系列区间，逐步缩小范围。从而减少条件判断次数。

- 反例

```JavaScript
// 最多要判断10次
if (value == 0){
    return result0;
} else if (value == 1){
    return result1;
} else if (value == 2){ 
    return result2;
} else if (value == 3){
    return result3;
} else if (value == 4){
    return result4;
} else if (value == 5){
    return result5;
} else if (value == 6){
    return result6;
} else if (value == 7){
    return result7;
} else if (value == 8){
    return result8;
} else if (value == 9){
    return result9;
} else {
    return result10;
}
```

- 正例

```JavaScript
// 最多判断4次
if (value < 6){
    if (value < 3){
        if (value == 0){
            return result0; 
        } else if (value == 1){
            return result1;
        } else {
            return result2;
        }
    } else {
        if (value == 3){
            return result3;
        } else if (value == 4){
            return result4;
        } else {
            return result5;
        }
    }
} else {
    if (value < 8){
        if (value == 6){
            return result6;
        } else {
            return result7;
        }
    } else {
        if (value == 8){
            return result8;
        } else if (value == 9){
            return result9;
        } else {
            return result10;
        } 
    }
}
```

### 4.2.3 查找表

- 描述
JavaScript中可以使用数组和普通对象来构建查找表，特别是在条件语句数量很大的时候，性能比条件语句快很多。
- 原因
当你使用查找表时，必须完全抛弃条件语句。这个过程变成数组项查询或者对象成员查询。主要优点：不用书写任何条件语句，即便候选值数量增加时，也几乎不会产生额外的开销。
- 反例

```JavaScript
if (value == 0){
    return result0;
} else if (value == 1){
    return result1;
} else if (value == 2){ 
    return result2;
} else if (value == 3){
    return result3;
} else if (value == 4){
    return result4;
} else if (value == 5){
    return result5;
} else if (value == 6){
    return result6;
} else if (value == 7){
    return result7;
} else if (value == 8){
    return result8;
} else if (value == 9){
    return result9;
} else {
    return result10;
}
```

- 正例

```JavaScript
// 将返回值存入数组
var results = [result0, result1, result2, result3, result4, result5, result6, result7, result8, result9, result10]
// 利用数组返回当前结果
return results[value];
```

## 4.3 递归

### 4.3.1 调用栈限制

JavaScript引擎支持的递归数量与JavaScript调用栈大小直接相关。如果超出了浏览器的调用栈限制，就会抛出调用栈溢出的异常。

### 4.3.2 递归模式

递归一般有两种模式：直接递归模式和隐伏模式。第二种模式在出现问题时，很难定位。

```JavaScript
// 1、直接调用模式
function recurse(){
    recurse();
}
recurse();
// 2、隐伏模式
function first(){
    second();
}
function second(){
    first();
}
first();
```

### 4.3.3 迭代

- 描述
任何递归能实现的算法，迭代也能实现。
- 原因
使用优化后的循环替代长时间运行的递归函数可以提升性能，因为运行一个循环比反复调用一个函数的开销要少很多。
- 反例

```JavaScript
function merge(left, right){
    var result = [];
    while (left.length > 0 && right.length > 0){
        if (left[0] < right[0]){
            result.push(left.shift());
        } else {
            result.push(right.shift());
        }
    }
    return result.concat(left).concat(right);
}
function mergeSort(items){
    if (items.length == 1) {
        return items;
    }
    var middle = Math.floor(items.length / 2),
    left = items.slice(0, middle),
    right = items.slice(middle);
    return merge(mergeSort(left), mergeSort(right));
}
```

- 正例

```JavaScript
function merge(left, right){
    var result = [];
    while (left.length > 0 && right.length > 0){
        if (left[0] < right[0]){
            result.push(left.shift());
        } else {
            result.push(right.shift());
        }
    }
    return result.concat(left).concat(right);
}
// 使用迭代后比递归要慢一些，但不会受到调用栈限制。是避免栈溢出错误的方法之一。
function mergeSort(items){
    if (items.length == 1) {
        return items;
    }
    var work = [];
    for (var i=0, len=items.length; i < len; i++){
        work.push([items[i]]);
    }
    work.push([]); // 如果数组长度为奇数
    for (var lim=len; lim > 1; lim = (lim+1)/2){
        for (var j=0,k=0; k < lim; j++, k+=2){
            work[j] = merge(work[k], work[k+1]);
        }
        work[j] = []; // 如果数组长度为奇数
    }
    return work[0];
}
```

### 4.3.4 Memoization

Memoization是一种避免重复工作的方法，它缓存前一个计算结果后供后续计算使用。
以阶乘函数为例：

```JavaScript
function factorial(n){
    if (n == 0){
        return 1;
    } else {
        return n * factorial(n-1);
    }
}
// 使用过程中factorial()函数被调用了18次，而所有必要的计算在第一行代码里已经处理掉了。
var fact6 = factorial(6);
var fact5 = factorial(5);
var fact4 = factorial(4);
```

使用Memoization后，将第一次计算的结果缓存起来，后期再调用的时候，直接从缓存中读取结果。

```JavaScript
function memfactorial(n){
    if (!memfactorial.cache){
        memfactorial.cache = { "0": 1, "1": 1 };
    }
    if (!memfactorial.cache.hasOwnProperty(n)){
        memfactorial.cache[n] = n * memfactorial (n-1);
    }
    return memfactorial.cache[n];
}
```

**欢迎大佬纠错指导，欢迎交流学习。**

>GitHub：[https://github.com/Code4GL](https://github.com/Code4GL)
公众号：code_everything
QQ：771841496
邮箱：guanli1991@163.com

![code_everything](/images/code_everything.jpg)
