---
title: 08ReactNative系列之开发工具-模拟器选择
date: 2020-12-23 11:01:07
excerpt: 其实对于模拟器选择，只是针对Android而言。因为IOS的模拟器就在XCode里面，也只能用它。Android模拟器主要分三种：AVD、Genymotion和游戏模拟器。第一种是之前讲过的Android SDK Manager自带的；第二种是国外的一款专门开发使用的模拟器；第三种就是市场上玩游戏的Android模拟器。第一种太卡，第三种版本太少，适合业余开发使用的。所以`推荐`第二种Genymotion。
tags:
    - ReactNative
    - 混合App
categories: ReactNative系列
---

![ReactNative.jpg](https://upload-images.jianshu.io/upload_images/18236822-e9d8ac4cb99f3b3f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
# 一、概述
其实对于模拟器选择，只是针对Android而言。因为IOS的模拟器就在XCode里面，也只能用它。
Android模拟器主要分三种：AVD、Genymotion和游戏模拟器。第一种是之前讲过的Android SDK Manager自带的；第二种是国外的一款专门开发使用的模拟器；第三种就是市场上玩游戏的Android模拟器。第一种太卡，第三种版本太少，适合业余开发使用的。所以`推荐`第二种Genymotion。
# 二、AVD
AVD的全称为：Android Virtual Device，就是Android运行的虚拟设备，他是Android的模拟器识别。建立的Android要运行，必须创建AVD，每个AVD上可以配置很多的运行项目。
它是由Android SDK Manager安装时候自带安装的，不需要额外安装，不过使用起来体验相当不好，不推荐使用。
# 三、Genymotion（推荐）
Genymotion是一款专业Android的模拟器，支持Windows、Linux和Mac OS等操作系统。相对AVD来讲，速度要快得多。
#3.1下载安装
点击连接进入官网，下载之前需要注册，这个注册是必须的，因为后面使用时也是需要这个帐号登录的。中文官网地址：[Genymotion](http://www.genymotion.net/)。
进入之后，点击“注册”。
![image.png](https://upload-images.jianshu.io/upload_images/18236822-525e9e2ff278333a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
然后跳转到登录页面，点击“Create an account”创建帐号。
![image.png](https://upload-images.jianshu.io/upload_images/18236822-64b93f2dcb9efe2d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
然后就开始填写信息。
![image.png](https://upload-images.jianshu.io/upload_images/18236822-b25043d5b02bfa2f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
账号注册好了，登录成功后，会出现以下页面。
![image.png](https://upload-images.jianshu.io/upload_images/18236822-187fec64c220449b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
> 温馨提示：不要点击中间的下载按钮，这个下载是下载通用版本。Genymotion有一个个人版本是免费的，我们主要使用个人版本。和其他公司一样，个人免费版本总是需要寻找一番。

点击右上角的下载之后，进入以下页面。
![image.png](https://upload-images.jianshu.io/upload_images/18236822-35af79fd8424cf4a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
页面继续往下拉，会看到`Get Genymotion personal version`，这个才是个人版本。
![image.png](https://upload-images.jianshu.io/upload_images/18236822-873af972b9f7631d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
点击之后，进入如下页面。
![image.png](https://upload-images.jianshu.io/upload_images/18236822-6eb7f80302a44962.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
此时才真正进入下载页面。中间展示的是和当前系统匹配的版本，下面是其他系统的版本。根据自己系统选择对应的版本下载即可。
![image.png](https://upload-images.jianshu.io/upload_images/18236822-768236cb565e00f1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
需要注意的是，有两个版本可以选择：带Virtualbox和不带Virtualbox。第一次安装选择带Virtualbox版本。
![image.png](https://upload-images.jianshu.io/upload_images/18236822-9f7e8731998b1295.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
> VirtualBox 是一款开源虚拟机软件。VirtualBox 是由德国 Innotek 公司开发，由Sun Microsystems公司出品的软件，使用Qt编写，在 Sun 被 Oracle 收购后正式更名成 Oracle VM VirtualBox。模拟器的启动需要虚拟机的支持。

下载完成之后安装即可，安装没有什么要注意的。
# 3.2创建模拟器
安装完成之后，会进入下面这个界面，选择是否新建一个虚拟机。选择之前需要注意一下，新建过程非常非常非常慢，如果暂时不需要可以后面选择“add”在新建。
![image.png](https://upload-images.jianshu.io/upload_images/18236822-ed310addc377d918.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
首次创建会提示要登录，帐号就是刚才下载时注册的帐号，登录即可。
![image.png](https://upload-images.jianshu.io/upload_images/18236822-47c736e157c1d024.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
登录之后，就可以选择Android版本以及手机型号。
![image.png](https://upload-images.jianshu.io/upload_images/18236822-75040177e8677589.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
选择一种之后就进入安装界面。考验网速的时候到了，如果对自己网速不自信，可以在网上找已经安装好的ova文件，下载下来导入也是一种不错的选择。下载地址：https://www.cnblogs.com/rainboy2010/p/6387770.html。
![image.png](https://upload-images.jianshu.io/upload_images/18236822-3c132eab9f1bd1e0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
创建完成之后，选择一种点击“start”即可，VirtualBox会自动启动。
![image.png](https://upload-images.jianshu.io/upload_images/18236822-c71f4c7389980314.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
启动完成后，就出现了安卓模拟器的样子。
![image.png](https://upload-images.jianshu.io/upload_images/18236822-2a3118754e3ff1dc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
> 温馨提示：首次启动，若计算机虚拟机功能没有开启，需要进入bios界面手动开启，将`Virtualization Technology`设为`enabled`即可。
# 四、游戏模拟器
游戏模拟器，顾名思义是专门玩游戏用的，所以他的性能非常好。但是Android的版本太少，有的甚至只有一种，所以只能用于业余开发。常见模拟器及其端口号：
1. 夜神安卓模拟器：`62001`
2. 逍遥安卓模拟器：`21503`
3. 蓝叠安卓模拟器：`5555`
4. 雷电安卓模拟器：`5555`
5. 天天安卓模拟器：`5037`
6. 网易MuMu模拟器：`7555`
7. 安卓模拟器大师：`54001`
8. Genymotion：`5555`

**欢迎大佬纠错指导，欢迎交流学习。**

>GitHub：[https://github.com/Code4GL](https://github.com/Code4GL)
公众号：code_everything
QQ：771841496
邮箱：guanli1991@163.com

![code_everything](/images/code_everything.jpg)