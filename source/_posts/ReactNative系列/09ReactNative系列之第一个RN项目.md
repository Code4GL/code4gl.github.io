---
title: 09ReactNative系列之第一个RN项目
date: 2020-12-23 11:04:38
excerpt: 在搭建好环境之后，接下来就是创建咱们第一个RN项目。
tags:
    - ReactNative
    - 混合App
categories: ReactNative系列
---

![ReactNative.jpg](https://upload-images.jianshu.io/upload_images/18236822-e9d8ac4cb99f3b3f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 一、概述

在搭建好环境之后，接下来就是创建咱们第一个RN项目。

## 二、react-native-cli安装

react-native-cli是RN的一个脚手架工具，用它可以初始化项目，得到一个项目模板。所以，首先得安装工具。运行以下命令即可安装。

```terminal
npm install -g react-native-cli
```

## 三、项目创建

创建之前，先进入指定的工作目录，运行初始化命令即可。

```terminal
react-native init HelloWorld
```

若想创建指定RN版本的项目，则需要在后面加上参数。

```terminal
react-native init HelloWorld --version x.xx
```

出现这样的界面，就算创建成果了。
![image.png](https://upload-images.jianshu.io/upload_images/18236822-5bdfabcd23ab0a34.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 四、运行IOS

IOS启动有两种方式：XCode中启动和运行命令启动。

1. XCode中启动：用XCode打开项目中/ios/.xcodeproj文件，选择一个模拟器，然后启动项目；
2. 命令启动：根目录下运行`react-native run-ios`即可，它会选择一个默认的模拟器启动；若想指定模拟器，则需要加上参数`react-native run-ios --simulator 'iPhone X'`。
运行成功后，就会出现以下页面。
![image.png](https://upload-images.jianshu.io/upload_images/18236822-8658bfb52082d99a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

`command+D`打开设置面板，点击`Enable Hot Reloading`开启热部署，即可进行开发。尝试着在`index.js`里修改一点代码。

## 五、运行Android

Android运行命令：`react-native run-android`。此时，我们可能会遇见第一个报错：
![image.png](https://upload-images.jianshu.io/upload_images/18236822-6cc7ed49995de681.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

查看错误原因，则是没有链接的设备，我们得先启动模拟器。启动完成之后，我们可以运行`adb devices`来查看已经链接的设备列表。
如果还是没有链接成功，则需要手动链接，运行`adb connect 127.0.0.1:端口号`，端口号是根据模拟器类型有所不同的，Genymotion的默认端口号为`5555`。可以去[常用模拟器端口号](https://www.jianshu.com/p/c8b3c549d707)查看。

出现以下界面，表示运行成功。

![image.png](https://upload-images.jianshu.io/upload_images/18236822-1f9bd490354d4219.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

点击菜单打开设置面板，开启热加载`Enable Hot Reloading`。就可以进行开发了。尝试着在`index.js`里修改一点代码：加上`My First`。
![image.png](https://upload-images.jianshu.io/upload_images/18236822-e857f2873b5f283f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

此时，我们的项目就启动好了。

初次启动时，可能会遇见各式各样的问题：

1. 模拟器链接成功，项目启动红屏。
![image.png](https://upload-images.jianshu.io/upload_images/18236822-77debd773c94ab7e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> 解决方案：点击菜单，打开`Dev Settings`，进入开发者设置；进入host设置，将host设置为`172.17.100.2:8081`；之后返回，重新进入菜单，选择`Reload`重新加载。

![image.png](https://upload-images.jianshu.io/upload_images/18236822-1e14e133aeeeb8d6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image.png](https://upload-images.jianshu.io/upload_images/18236822-941e8efd52027699.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
2. 设置host之后还是不起作用
> 运行`adb shell`，进入shell环境；然后运行`getprop`获取相关参数。找到`dhcp.eth1.server`对应的IP地址；找到ip地址之后，重复第一步，端口还是`8081`。

![image.png](https://upload-images.jianshu.io/upload_images/18236822-8052efa78a82e65f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

一般情况下，`dhcp.eth1.server`对应的IP地址都是一样的，即`172.17.100.2`，所以大部分情况下不需要执行`adb shell`。

## 六、目录结构介绍

此时我们可以使用之前下载好的VS Code打开项目了。进入VS Code之后点击“文件”，选择“打开文件夹”，打开刚才创建的项目HelloWorld。
![image.png](https://upload-images.jianshu.io/upload_images/18236822-fef1b43d9e8b0fe3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

目录介绍：

```file
_Test_：测试使用的文件夹，执行npm test会调用此文件夹。
.vscode：使用VS Code打开就会产生，用于存放VS Code相关配置。
android：安卓原生开发目录，可以使用Android Studio来进行原生开发。
ios：IOS原生开发目录，可以使用XCode进行原生开发。
node_modules：项目运行时下载的依赖存放的文件夹，此文件夹是在提交版本时必须忽略的文件夹（SVN不会自动忽略）。
.bablelrc：Bable的配置文件，Bable是一个转码器，可以将ES6的代码转换成ES5代码，从而适应大部分运行环境。
.buckconfig：buck的配置文件，Buck是Facebook推出的一款高效率的App项目构建工具。
.flowconfig：Flow 是 Facebook 旗下一个为 JavaScript 进行静态类型检测的检测工具。它可以在 JavaScript 的项目中用来捕获常见的 bugs，比如隐式类型转换，空引用等等。
.gitattributes：git属性文件设定一些项目特殊的属性。比如要比较word文档的不同；对strings程序进行注册；合并冲突的时候不想合并某些文件等等。
.gitignore：用来配置git提交需要忽略的文件。
.watchmanconfig：用于监控bug文件和文件变化，并且可以出发指定的操作。
App.js：实际App编写代码的文件。
app.json：配置了name和displayName，用于配置App的基础信息。
index.js：项目入口文件，运行时候只需要运行这个文件即可。
package.json：package.json定义了项目所需要的各种模块，以及项目的配置信息（比如名称、版本、许可证等元数据）。npm install命令根据这个配置文件，自动下载所需的模块，也就是配置项目所需的运行和开发环境。
package-lock.json：如果用npm来管理依赖，还会生成这个文件用来锁依赖的版本，类似于yarn.lock文件。
yarn.lock：Yarn 是 一个由 Facebook 创建的新 JavaScript 包管理器；每次添加依赖或者更新包版本，yarn都会把相关版本信息写入yarn.lock文件。这样可以解决同一个项目在不同机器上环境不一致的问题。
```

**欢迎大佬纠错指导，欢迎交流学习。**

>GitHub：[https://github.com/Code4GL](https://github.com/Code4GL)
公众号：code_everything
QQ：771841496
邮箱：guanli1991@163.com

![code_everything](/images/code_everything.jpg)
