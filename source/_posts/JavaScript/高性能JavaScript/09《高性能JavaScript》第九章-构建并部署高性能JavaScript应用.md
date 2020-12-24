---
title: 09《高性能JavaScript》第九章 构建并部署高性能JavaScript应用
date: 2020-12-23 14:45:26
excerpt: 出于出书时间的考虑，本章使用到的技术可能已经过时，主要理解其思想。本章的目的是了解如何有效地组织并部署基于JavaScript的Web应用的一些必要知识。
categories: JavaScript
tags:
    - 高性能JavaScript
    - 原生JS
---

![highPerformanceJs](/images/javascript/highPerformanceJs/highPerformanceJs.jpg)

出于出书时间的考虑，本章使用到的技术可能已经过时，主要理解其思想。本章的目的是了解如何有效地组织并部署基于JavaScript的Web应用的一些必要知识。

1. Apache Ant：是一个软件构建自动化工具；
2. 合并多个JavaScript文件，减少页面渲染所需的HTTP请求数；
3. 预处理JavaScript源文件并不会让应用变得更快，但它允许你做其他的事情，如有条件的插入测试代码；
4. JavaScript压缩：把JavaScript文件中所有与运行无关的部分进行剥离的过程。该过程通常可以将文件大小减半，促使文件下载的更快；
5. 合并、预处理和压缩这些步骤既能在构建时进行，也能在运行时进行。但只要能在构建时完成的工作，就不要留在运行时去做；
6. JavaScript的HTTP压缩：HTTP请求头中`Accept-Encoding`可以用来压缩文档，以得到更快的下载，值包括`gzip`、`compress`、`deflate`和`identity`。
7. 缓存JavaScript文件：服务器通过“Expires HTTP响应头”来告诉客户端一个资源应当缓存的时间；
8. 使用CDN：内容分发网络可以极大地减少网络延迟；
9. 部署JavaScript资源通常需要复制大量文件到一台或多台远程主机，可以通过工具来处理，如Apache Ant。

**欢迎大佬纠错指导，欢迎交流学习。**

>GitHub：[https://github.com/Code4GL](https://github.com/Code4GL)
公众号：code_everything
QQ：771841496
邮箱：guanli1991@163.com

![code_everything](/images/code_everything.jpg)
