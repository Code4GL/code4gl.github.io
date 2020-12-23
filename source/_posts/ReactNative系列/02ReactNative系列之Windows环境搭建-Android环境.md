---
title: 02ReactNative系列之Windows环境搭建-Android环境
date: 2020-12-23 10:16:28
excerpt: 目前市面上App系统主要由Android和IOS系统霸占，想要开发App安卓环境是必须要满足的。后期主要用于安卓模拟器的运行及debug调试。
tags:
    - ReactNative
    - 混合App
categories: ReactNative系列
---

![ReactNative.jpg](https://upload-images.jianshu.io/upload_images/18236822-e9d8ac4cb99f3b3f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
# 一、概述
目前市面上App系统主要由Android和IOS系统霸占，想要开发App安卓环境是必须要满足的。后期主要用于安卓模拟器的运行及debug调试。
# 二、Android SDK Manager
Android SDK Manager是安卓SDK管理工具，它可以管理你计算机上的安卓SDK，包括下载、删除等等。网上大多数攻略推荐使用Android Studio来安装，如果不做安卓的后端开发，Android Studio也就只是起到安装SDK和启动项目的作用，如果只使用这两点功能，安装Android Studio其实不是一个最好的选择。这里强烈推荐使用Android SDK Manager来管理计算机上的AndroidSDK，主要可以自定义选择安装和删除需要的SDK，对于有点处女座的我来说，首选工具。
>下载地址：http://www.androiddevtools.cn/

此网站专门为安卓开发这提供的资源下载网站，进入之后点击“Android SDK工具”。
![SDK Tools](https://upload-images.jianshu.io/upload_images/18236822-4727a7dd428cbac2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
点击之后，进入如下页面，选择需要下载的版本。一个是需要安装的，一个是压缩包直接解压。这里选择的是Windows系统的版本。
![image.png](https://upload-images.jianshu.io/upload_images/18236822-937a7792cf6920d4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
下载完成之后就点击安装，安装完成后还需要配置环境变量。
# 三、环境变量配置
还是和JDK环境变量配置一样，进入环境变量配置窗口，新建系统变量。变量名：`ANDROID_HOME`；变量值：`D:\Android\SDK`（自己SDK的安装路径）。最后在Path中加入
```
// 与之前的环境变量一定要用英文;隔开
%ANDROID_HOME%\platform-tools;%ANDROID_HOME%\tools 
```
如果不明白的可以去看看上一篇文章中的Java环境变量配置：[ReactNative系列之Windows环境搭建-Java环境](https://www.jianshu.com/p/504bc59e0b0e)。然后调出命令行，进行验证。输入“adb”命令，如果显示相关信息则说明配置成功，否则失败。
![image.png](https://upload-images.jianshu.io/upload_images/18236822-871ca79719236acb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
# 四、国内仓库镜像
工具配置好了之后，需要下载需要的SDK文件，此时需要去Google下载。国内用户则需要通过一定手段才能不被网速耗死。此时可以配置一下国内的镜像仓库，当然对自己网速自信的同志可以忽略。
- 常用镜像：

名称 | 地址
---|---
大连东软信息学院 | http://mirrors.neusoft.edu.cn:80
北京化工大学 | IPv4:http://ubuntu.buct.edu.cn:80
北京化工大学 | IPv4:http://ubuntu.buct.cn:80
北京化工大学 | IPv6:http://ubuntu.buct6.edu.cn:80
上海GDG | http://sdk.gdgshanghai.com:8000
中国科学院开源协会 | http://mirrors.opencas.cn:80
中国科学院开源协会 | http://mirrors.opencas.org:80
中国科学院开源协会 | http://mirrors.opencas.ac.cn:80
> 这里务必请注意，由于网络或者镜像仓库的问题，有些镜像中依赖包的数量并不全，如果发现没有自己想要的包时，重启、更换镜像或不使用镜像都是解决的方法。还有些依赖不知道什么原因，始终不会没有，这时就需要从网站上下载后移植到对应的文件夹中，这个后面遇见了会讲。

添加镜像：Tools（左上角）>Options：
![镜像仓库](https://upload-images.jianshu.io/upload_images/18236822-cbd198a2915b79a2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
# 五、常见依赖包
安装成功之后，会产生两个启动文件，一个是AVD Manager，一个是SDK Manager。前者是自带的安卓手机模拟器，为项目调试准备的，不过是相当的卡，不建议使用，后面会有专门推荐使用工具；后者就是安卓依赖的管理器，点击启动进入以下窗口。
![image.png](https://upload-images.jianshu.io/upload_images/18236822-55dabc6ab533cc12.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
##5.1常见包说明
后缀 | 说明
---|---
Android SDK Tools | 即SDK Manager，方便以后版本更新。
Platform-tools | 构建工具运行平台，也就是Build-tools运行的平台，所以后面要安装Build-tools就必须安装对应版本的Platform-tools，而且Platform-tools只有大版本。
Tools(Preview Channel) | 此为前面Tools的预览版本，一般不会使用，因为预览版本一般不稳定。
Android 8.1.0(API 27) | 安卓系统版本，括号里是对应的API版本。
System Image | 模拟器的操作系统ROM，如果需要在模拟器上调试程序则需要下载，一般一个版本只需要一个atom+x86就够了。
Google APIs | Google提供的API，如需要调用Google的API就需要下载，否则不需要。
Sources for Android SDK | SDK的源码，如需要看源码的同志，可以下载下来看看。
Android Support Repository | 主要是方便在gradle中使用Android Support Library，因为Google并没有把这些库发布到maven center或者jcenter去，而是使用了Google自己的maven仓库。
Android Support Library | 提供的支持库。
> 在很多中情况下Android Support Library是无法找到的，需要在网站上重新下载，之后解压到SDK的extras目录下即可。还是在AndroidDevTools下载即可，下载地址：http://www.androiddevtools.cn/。下载完成之后，根据网站上的指导将文件转移到指定文件夹即可。

![Android Support Library](https://upload-images.jianshu.io/upload_images/18236822-bc8bdbb0914f67ed.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
> 如果需要用自带的模拟器调试，还需要下载最后一项Intel x86 Emulator Accelerator，这个是对AVD Manager在Windows上运行进行的优化。但是这个包后面会显示Not compatible with Windows，此时也是需要额外重新下载安装的，这个可以在Intel官网上下载，下载地址：https://software.intel.com/en-us/articles/intel-hardware-accelerated-execution-manager-intel-haxm


## 5.2ReactNative0.58+必备依赖包
最新实践后，RN0.85+版本必须的依赖包如下：
1. Android SDK Platform 23
2. Android SDK Build-Tools 23.0.1
3. Android Support Repository
4. Android SDK Platform 26
5. Android SDK Build-Tools 26.0.3
6. Android SDK Platform 27
7. Android SDK Build-Tools 27.0.3

**欢迎大佬纠错指导，欢迎交流学习。**

>GitHub：[https://github.com/Code4GL](https://github.com/Code4GL)
公众号：code_everything
QQ：771841496
邮箱：guanli1991@163.com

![code_everything](/images/code_everything.jpg)
