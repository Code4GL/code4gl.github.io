---
title: 14ReactNative系列之版本更新策略
date: 2020-12-23 11:12:23
excerpt: 版本更新是每个App不可缺少的功能，它既是日常版本迭代的重要工具，也是在出现问题紧急更新的法宝。目前主要有：市场更新、自主更新和热更新。
tags:
    - ReactNative系列
    - 混合App
categories: ReactNative
---

![reactnative](/images/reactnative/reactnative.jpg)

## 一、概述

版本更新是每个App不可缺少的功能，它既是日常版本迭代的重要工具，也是在出现问题紧急更新的法宝。目前主要有：市场更新、自主更新和热更新。

## 二、IOS

由于IOS中不能在App Store之外的地方更新App，因此所有的更新策略都是围绕着App Store来展开。目前常见做法是拿当前App版本和App Store的版本对比，若App Store版本大于当前版本，则提醒用户跳转App Store更新。多数情况下，iPhone在Wifi网络下都会自动静默完成版本更新。
> 对于`企业版App`则需要搭建自己的更新平台，类似于Android，对比客户端和服务器上的版本，提醒用户进行下载更新。之后，用户需要信任该企业证书才能使用企业版App。

## 三、Android

Android的方式则大有不同，Apk由于不受App市场及手机限制，可以随意安装Apk文件，所以大部分情况下都会避开市场下载，直接从自己服务器下拉最新Apk进行更新，所以也就出现了首页发现新版本、版本检测等一些功能。

### 3.1市场更新

这种方式和IOS一样，通过对比当前安装App版本与市场App版本，提示用户跳转应用市场，通过市场来更新App，这是一个成本最低的更新策略。

### 3.2自主更新

这种方式需要自己创建一整套服务用于App版本更新。主要步骤如下：

1. App版本检测：分为自动检测和手动检测。自动检测是在首页加载之后进行检测，提示用户需要更新版本；手动检测是在App设置中提供入口，以供用户自己检测App是否为最新版本。需要将App的版本号和服务器上Apk的版本号作比较。
2. Apk下载：从Apk文件服务器上下载最新版本Apk，需要展示下载进度，下载完成之后提示用户安装。需要注意的是，用户点击下载时，需要检测默认路径下有没有最新Apk，没有再进行下载，否则直接提示安装。这样可以防止恶意下载。
3. 下载完成后会提示是否安装，点击安装即可。
另外，还需要考虑低版本App是否能够继续使用，即是否进行强制更新。在更新策略中应该体现，后台可以选择此版本是否支持强制更新。更新教程：[react-native-app-upgrade](https://github.com/songxiaoliang/react-native-app-upgrade)。

## 四、热更新

说起RN，就不得不说说热更新，但是目前并没有一个可靠的免费平台提供支持，这使好多开发者望而却步。

![hotRload](/images/reactnative/reactnativeSeries/reactnativeSeries14/hotRload.png)

热更新一般都是更新JS和图片，也就是在不重新安装apk的情况下更新JS和图片。目前市面上常见的热更新方案有两款：微软的[react-native-code-push](https://github.com/Microsoft/react-native-code-push)和RN中文网的[react-native-pushy](https://github.com/reactnativecn/react-native-pushy)。如果有要求也能自己搭建热更新服务。对热更新细化后还可以分为：全量热更新和补丁热更新。
> 苹果允许使用热更新[Apple's developer agreement 条款3.3.2](https://developer.apple.com/programs/information/Apple_Developer_Program_Information_8_12_15.pdf), 但是规定不能弹框提示用户更新，影响用户体验。苹果禁的是 rollout.io, JSPatch 这类具备修改原生代码能力的框架。 Google Play也允许热更新，但必须弹框告知用户更新。在中国的android市场发布时，都必须关闭更新弹框，否则会在审核应用时以“请上传最新版本的二进制应用包”驳回应用。

### 4.1react-native-code-push

[热更新-CodePush最新集成总结](https://blog.csdn.net/qq_33323251/article/details/79437932)
[微软的React Native热更新](https://blog.csdn.net/qq_21937107/article/details/81870759)
[热更新全方位讲解](https://www.jianshu.com/p/8e08c7661275)
[热更新原理分析与实现](https://blog.csdn.net/shandian000/article/details/54582603)
[react-native-code-push进阶篇](https://www.jianshu.com/p/6e96c6038d80?from=timeline)

### 4.2react-native-pushy

[pushy](http://update.reactnative.cn/home)
[RN热更新react-native-pushy](https://www.jianshu.com/p/741cd088033b)

### 4.3自主搭建热更新服务

其实就是在自己服务器上搭建一套codepush系统，这样比较简单也省事。除了服务器地址与微软的不一样，其他的完全一样。
[ReactNative热更新之自搭建环境](https://www.jianshu.com/p/be8962d12726)

### 4.4补丁热更新

实际上就是通过算法计算出增量补丁，使用补丁更新。算法有：google-diff-match-patch、bsdiff、bspatch等。

1. 服务端使用bsdiff算法将老RN包和新RN包生成一个补丁patch文件，供客户端下载。
2. 客户端下载patch文件，使用将补丁patch文件和老RN包生成一个新RN包。

**欢迎大佬纠错指导，欢迎交流学习。**

>GitHub：[https://github.com/Code4GL](https://github.com/Code4GL)
公众号：code_everything
QQ：771841496
邮箱：guanli1991@163.com

![code_everything](/images/code_everything.jpg)
