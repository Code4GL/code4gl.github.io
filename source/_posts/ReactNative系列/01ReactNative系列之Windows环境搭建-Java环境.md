---
title: 01ReactNative系列之Windows环境搭建-Java环境
date: 2020-12-23 10:14:14
excerpt: 首先，Windows环境下只能开发Android，所以Windows环境搭建只针对Android开发来说。当然，使用特殊手段也能进行IOS开发，比如“黑苹果”之类的就另说了。
tags:
    - ReactNative
    - 混合App
categories: ReactNative系列
---

![ReactNative.jpg](https://upload-images.jianshu.io/upload_images/18236822-e9d8ac4cb99f3b3f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 一、概述

首先，Windows环境下只能开发Android，所以Windows环境搭建只针对Android开发来说。当然，使用特殊手段也能进行IOS开发，比如“黑苹果”之类的就另说了。
由于原生Android就是使用Java开发的，所以环境搭建的第一步就需要安装好Java环境。官方推荐安装JDK1.8，所以我们就以JDK1.8为例，而且有些RN组件不太支持JDK1.7及以下。安装JDK，对于接触过Java开发的同志们应该是非常熟悉了，后面的内容可以略过。

## 二、JDK1.8下载

下载链接如下：
>[https://www.oracle.com/technetwork/cn/java/javase/downloads/index.html](https://www.oracle.com/technetwork/cn/java/javase/downloads/index.html)

目前最新版JDK已经到11了，需要往下翻一下找到JDK1.8，下载下来。
![image.png](https://upload-images.jianshu.io/upload_images/18236822-510c1d7eda518543.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
勾选协议后，选择Windows环境安装包，点击下载。
![image.png](https://upload-images.jianshu.io/upload_images/18236822-e1ced73a5eba24d0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 三、安装步骤

安装包下载完成之后，双击运行安装程序。
![image.png](https://upload-images.jianshu.io/upload_images/18236822-7efa63376538cc2c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
点击下一步之后，选择好安装目录，继续安装。其他选项默认即可。
![image.png](https://upload-images.jianshu.io/upload_images/18236822-c54ffe5b422aaa81.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
完成之后，这时出现安装Java JRE的安装界面，应选择当时安装JDK时的Java目录下，与当时JDK在同级目录下。
![image.png](https://upload-images.jianshu.io/upload_images/18236822-946e51df8361de2a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
>温馨提示：Java中JDK是开发者开发使用的工具，其中自带有一套JRE，JRE则是java项目运行的环境。也就是说，如果只需要运行Java项目，则只需要安装JRE即可；若需要进行Java开发，则需要安装JDK。

安装成功后，点击关闭。

## 四、环境变量配置

在完成安装之后，此时的JDK还不能马上使用，还需要配置环境变量。什么是环境变量呢？
>温馨提示：通俗地讲，我们通常安装软件实际上安装的一个项目，项目就会有目录结构，在指定的目录下会有项目启动的客户端，我们每次使用实际上先启动了项目，再使用其功能。如果每次启动项目都需要进入这个目录下启动，是不是很繁琐？此时，计算机便提供了一个便捷方式，在计算机的环境变量下配置好该目录，我们就可以在计算机的任何位置启动该项目了。前端的同志可以想想npm的全局安装，亦是同样的道理。

接下来，右键“我的电脑”，点击“高级系统设置”。
![image.png](https://upload-images.jianshu.io/upload_images/18236822-ae16e014afa3b8ed.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
之后点击“环境变量”。
![image.png](https://upload-images.jianshu.io/upload_images/18236822-5d0ab4af70de8d8b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
新建系统环境变量。变量名：JAVA_HOME；变量值：（就是你的JDK安装路径）如：D:\Program Files (x86)\Java\jdk1.8.0_73；注意这里不需要JRE的地址，然后点击确定。
![image.png](https://upload-images.jianshu.io/upload_images/18236822-27e7cefb956aa236.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
再点击新建系统变量。变量名：CLASSPATH；变量值：“.;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar;”双引号中间的一个都不能漏掉（不包括双引号），最好是粘贴复制上去。
![image.png](https://upload-images.jianshu.io/upload_images/18236822-2f60e7954554fe51.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
找到Path，这是是系统之前就有的，找到之后双击，将“.;%JAVA_HOME%\bin;” 复制进去放在最后面。还是一样，双引号中间的一个都不能漏掉（不包括双引号）。

## 五、验证

打开命令行，（windows+R）打开输入cmd进入命令行，输入“javac”，看是否有反应，如下图则说明成功，否则失败。或者输入“java -version”也可以查看JDK版本。
![image.png](https://upload-images.jianshu.io/upload_images/18236822-ea63892628269b7c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**欢迎大佬纠错指导，欢迎交流学习。**

>GitHub：[https://github.com/Code4GL](https://github.com/Code4GL)
公众号：code_everything
QQ：771841496
邮箱：guanli1991@163.com

![code_everything](/images/code_everything.jpg)
