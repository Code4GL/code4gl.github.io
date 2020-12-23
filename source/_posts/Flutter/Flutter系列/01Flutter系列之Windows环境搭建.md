---
title: 01Flutter系列之Windows环境搭建
date: 2020-12-23 15:10:22
excerpt: Flutter系列之Windows环境搭建
categories: Flutter
tags:
    - Flutter系列
---

![image](https://upload-images.jianshu.io/upload_images/18236822-921b445577766d10.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 一、前期工作

### 1.1 环境检查

![环境配置要求](https://upload-images.jianshu.io/upload_images/18236822-f92aa1e881c14311.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Flutter安装前需要检查以下两个环境配置：[Windows PowerShell 5.0](https://docs.microsoft.com/en-us/powershell/scripting/install/installing-windows-powershell)和[Git for Windows](https://git-scm.com/download/win) 2.x。如果没有安装，需要提前下载安装。

> 检查是否安装Git：按住`Win+R`后，输入`cmd`回车，进入命令行输入`git --version`，即可查看Git版本。

![Git检查](https://upload-images.jianshu.io/upload_images/18236822-34232c9643d6cfe1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> 检查是否安装Windows PowerShell：右键左下角Win图标，选中Windows PowerShell，然后进入输入命令`Get-Host | Select-Object Version`，即可查看Windows PowerShell版本。

![Windows PowerShell检查](https://upload-images.jianshu.io/upload_images/18236822-757f9459ba58f021.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![Windows PowerShell检查](https://upload-images.jianshu.io/upload_images/18236822-582e313ce2edc7ba.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 1.2 Flutter SDK下载

点击进入[中文官网](https://flutter.cn/docs/get-started/install/windows)，下载完成后解压到指定文件夹下即可。**注意：不要将其解压到需要高权限的文件夹，例如`C:\Program Files\`**。

![下载Flutter SDK](https://upload-images.jianshu.io/upload_images/18236822-766d7fbaec7ded65.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

下载完成之后需要配置环境变量，类似Java环境变量配置。通过`计算机`-`属性`-`高级系统设置`-`环境变量`-`用户变量`中的`Path`添加SDK的存放地址，到`bin`目录下。

![环境变量](https://upload-images.jianshu.io/upload_images/18236822-d9966332c911068f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 1.3 Flutter环境检查

运行`flutter doctor`命令查看是否需要安装其它依赖项来完成安装。运行之后如果一直卡在以下画面，说明需要配置国内镜像。

![未配镜像](https://upload-images.jianshu.io/upload_images/18236822-e2f6e2f3f3fa5653.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> Flutter镜像配置：
1、计算机-属性-高级系统设置-环境变量；
2、在用户变量下，新建以下变量。
变量名                         值
FLUTTER_STORAGE_BASE_URL         [https://storage.flutter-io.cn](https://links.jianshu.com/go?to=https%3A%2F%2Fstorage.flutter-io.cn)
PUB_HOSTED_URL              [https://pub.flutter-io.cn](https://links.jianshu.com/go?to=https%3A%2F%2Fpub.flutter-io.cn)

配置完成后，即可安装成功，如下图所示。图片显示信息如下：
1、Flutter SDK安装成功，版本号为1.22.5；
2、Android的协议未同意；
3、未安装Android Studio,需要Flutter插件和Dart插件；
4、没有设备链接（指的是手机或模拟器）。

![安装SDK](https://upload-images.jianshu.io/upload_images/18236822-0648a89b7499a182.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 1.3.1 Android license not accepted

根据提示，在命令行运行`flutter doctor --android-licenses`，随后根据提示输入`y`然后`回车`即可。

![Android协议](https://upload-images.jianshu.io/upload_images/18236822-04a2d8c211f92500.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 1.3.2 Android Studio

即需要在[developer官网](https://developer.android.google.cn/studio/)下载安装Android Studio，下载过程根据网速可能会有点慢。然后还要在Android Studio中安装好需要的插件。安装完成之后打开，选择`Configure`-`plugins`打开插件安装页。

![Android Studio](https://upload-images.jianshu.io/upload_images/18236822-736bd57c12973cde.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在`Marketplace`栏搜索Flutter安装，安装过程中会提示是否安装Dart插件，同意即可。**注意：有些小伙伴可能搜不出插件，这时需要关闭掉`上网工具`在搜索即可。**

![插件安装](https://upload-images.jianshu.io/upload_images/18236822-1a2d97fc4b5b66b6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

安装完成之后运行`flutter doctor`检查一下，如果有小伙伴安装好了之后还是提示有错误，可以无视这个提示。这是Android Studio 4.1中插件地址更换导致的，实际已经安装成功了。

![插件安装](https://upload-images.jianshu.io/upload_images/18236822-35b846b53b078f1d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 1.3.3 Connected device

关于设备分为两种：真机和模拟器。模拟器推荐可以参考之前的文章[ReactNative系列之开发工具-模拟器选择](https://www.jianshu.com/p/c8b3c549d707)，推荐`Genymotion`专业开发Android模拟器。

**欢迎大佬纠错指导，欢迎交流学习。**

>GitHub：[https://github.com/Code4GL](https://github.com/Code4GL)
公众号：code_everything
QQ：771841496
邮箱：guanli1991@163.com

![code_everything](/images/code_everything.jpg)
