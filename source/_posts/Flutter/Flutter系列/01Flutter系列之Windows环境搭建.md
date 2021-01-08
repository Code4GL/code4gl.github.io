---
title: 01Flutter系列之Windows环境搭建
date: 2020-12-23 15:10:22
excerpt: Flutter系列之Windows环境搭建
categories: Flutter
tags:
    - Flutter系列
---

![flutter](/images/flutter/flutter.png)

## 一、前期工作

### 1.1 环境检查

![环境配置要求](/images/flutter/flutterSeries/flutterSeries01/env-config.png)

Flutter安装前需要检查以下两个环境配置：[Windows PowerShell 5.0](https://docs.microsoft.com/en-us/powershell/scripting/install/installing-windows-powershell)和[Git for Windows](https://git-scm.com/download/win) 2.x。如果没有安装，需要提前下载安装。

> 检查是否安装Git：按住`Win+R`后，输入`cmd`回车，进入命令行输入`git --version`，即可查看Git版本。

![Git检查](/images/flutter/flutterSeries/flutterSeries01/git-version.png)

> 检查是否安装Windows PowerShell：右键左下角Win图标，选中Windows PowerShell，然后进入输入命令`Get-Host | Select-Object Version`，即可查看Windows PowerShell版本。

![Windows PowerShell检查](/images/flutter/flutterSeries/flutterSeries01/powerShell.png)

![Windows PowerShell检查](/images/flutter/flutterSeries/flutterSeries01/PowerShell-version.png)

### 1.2 Flutter SDK下载

点击进入[中文官网](https://flutter.cn/docs/get-started/install/windows)，下载完成后解压到指定文件夹下即可。**注意：不要将其解压到需要高权限的文件夹**，例如`C:\Program Files\`。

![下载Flutter SDK](/images/flutter/flutterSeries/flutterSeries01/flutter-sdk.png)

下载完成之后需要配置环境变量，类似Java环境变量配置。通过`计算机`-`属性`-`高级系统设置`-`环境变量`-`用户变量`中的`Path`添加SDK的存放地址，到`bin`目录下。

![环境变量](/images/flutter/flutterSeries/flutterSeries01/flutter-path.png)

### 1.3 Flutter环境检查

运行`flutter doctor`命令查看是否需要安装其它依赖项来完成安装。运行之后如果一直卡在以下画面，说明需要配置国内镜像。

![未配镜像](/images/flutter/flutterSeries/flutterSeries01/flutter-doctor.png)

> Flutter镜像配置：
1、计算机-属性-高级系统设置-环境变量；
2、在用户变量下，新建以下变量。
变量名                         值
FLUTTER_STORAGE_BASE_URL         [https://storage.flutter-io.cn](https://links.jianshu.com/go?to=https%3A%2F%2Fstorage.flutter-io.cn)
PUB_HOSTED_URL              [https://pub.flutter-io.cn](https://links.jianshu.com/go?to=https%3A%2F%2Fpub.flutter-io.cn)

配置完成后，即可安装成功，如下图所示。图片显示信息如下：

1. Flutter SDK安装成功，版本号为1.22.5；
2. Android的协议未同意；
3. 未安装Android Studio,需要Flutter插件和Dart插件；
4. 没有设备链接（指的是手机或模拟器）。

![安装SDK](/images/flutter/flutterSeries/flutterSeries01/flutter-doctor1.png)

#### 1.3.1 Android license not accepted

根据提示，在命令行运行`flutter doctor --android-licenses`，随后根据提示输入`y`然后`回车`即可。

![Android协议](/images/flutter/flutterSeries/flutterSeries01/flutter-doctor2.png)

#### 1.3.2 Android Studio

即需要在[developer官网](https://developer.android.google.cn/studio/)下载安装Android Studio，下载过程根据网速可能会有点慢。然后还要在Android Studio中安装好需要的插件。安装完成之后打开，选择`Configure`-`plugins`打开插件安装页。

![Android Studio](/images/flutter/flutterSeries/flutterSeries01/android-studio.png)

在`Marketplace`栏搜索Flutter安装，安装过程中会提示是否安装Dart插件，同意即可。**注意：有些小伙伴可能搜不出插件，这时需要关闭掉`上网工具`在搜索即可。**

![插件安装](/images/flutter/flutterSeries/flutterSeries01/android-studio-dart.png)

安装完成之后运行`flutter doctor`检查一下，如果有小伙伴安装好了之后还是提示有错误，可以无视这个提示。这是Android Studio 4.1中插件地址更换导致的，实际已经安装成功了。可以忽略此错误。

![插件安装](/images/flutter/flutterSeries/flutterSeries01/android-studio-plugin1.png)

#### 1.3.3 Connected device

关于设备分为两种：真机和模拟器。模拟器推荐可以参考之前的文章[《ReactNative系列之开发工具-模拟器选择》](/ReactNative/ReactNative系列/08ReactNative系列之开发工具-模拟器选择/)，推荐`Genymotion`专业开发Android模拟器，官网地址：[https://www.genymotion.com/](https://www.genymotion.com/)。

1. 下载时需要注册账号，用邮箱注册完成后登录；
2. 如果计算机未安装VirtualBox，登录后请下载`With VirtualBox`版本，否则可以下载`WithOut VirtualBox`版本；
3. 安装完成后，软件需要登录才能使用，登录刚才官网注册的账号即可；
4. 登录后，选择`Personal Use`版本，此版本仅供个人免费使用；
5. 最后，同意隐私政策，新建模拟器即可。

![Genymotion](/images/flutter/flutterSeries/flutterSeries01/Genymotion1.png)

![Genymotion](/images/flutter/flutterSeries/flutterSeries01/Genymotion2.png)

![Genymotion](/images/flutter/flutterSeries/flutterSeries01/Genymotion3.png)

**欢迎大佬纠错指导，欢迎交流学习。**

>GitHub：[https://github.com/Code4GL](https://github.com/Code4GL)
公众号：code_everything
QQ：771841496
邮箱：guanli1991@163.com

![code_everything](/images/code_everything.jpg)
