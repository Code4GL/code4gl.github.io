---
title: 06ReactNative系列之Mac环境搭建-Android环境
date: 2020-12-23 10:57:18
excerpt: Android环境是在上面IOS环境搭建好的基础上进行的，以上公共软件的安装不在复述。
tags:
    - ReactNative系列
    - 混合App
categories: ReactNative
---

![ReactNative.jpg](https://upload-images.jianshu.io/upload_images/18236822-e9d8ac4cb99f3b3f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 一、概述

Android环境是在上面IOS环境搭建好的基础上进行的，以上公共软件的安装不在复述。

## 二、Java环境

Mac上安装Java环境要和Windows上略有不同，不过比Windows上方便多了。还是附上下载地址：[JDK下载](http://www.oracle.com/technetwork/java/javase/downloads/index.html)。选择好正确版本后（JDK1.8），看清楚是MacOS版哦。下载下来的其实是.dmg文件，双击后安装。
> 温馨提示：Mac系统会默认安装好了J2SE，版本为1.6，这个是苹果系统中自带的Java环境，安装目录是：`/System/Library/Frameworks/JavaVM.framework/Versions/Current/Commands/java`，而我们在Oracle官网下载的JDK安装目录是：`/Library/Java/JavaVirtualMachines/jdk1.8.0_121.jdk/Contents/Home/bin/java`，一般情况下不会去用Mac自带的jdk。安装好之后就开始配置环境变量了。

首先，验证一下JDK是否安装成功`java -version`。
如果安装成功，则输入`sudo vim /etc/profile`来修改配置文件。
在文件最后加上（注意将JDK版本更换成自己下载的版本）：

```config
JAVA_HOME="/Library/Java/JavaVirtualMachines/jdk1.8.0_77.jdk/Contents/Home/"
CLASS_PATH="$JAVA_HOME/lib"
PATH=".:$PATH:$JAVA_HOME/bin"
```

然后在终端输入`source /etc/profile`使其立即生效。
最后查看系统当前的环境变量`echo $JAVA_HOME`。此时如果显示路径为我们所配置的JDK的路径时，说明环境变量配置已经成功。

如果没有“profile”文件，需要自行创建之后再编辑。

1. 输入`cd ~`进入目标目录；
2. 打开文件`open -e .bash_profile`；会提示文件不存在“The file /Users/scarlettxu/.bash_profile does not exist.”；
3. 创建文件`touch .bash_profile`；
4. 最后在编辑`vim .bash_profile`；
5. 将上面的环境内容输入即可。

> 网上也有很多教程配置多版本JDK，并且可以灵活切换。其实不配置也可以，因为现在的开发工具基本上都可以选择JDK版本，需要用到哪个版本去选择即可，环境变量里就放一个比较常用的版本。
多版本JDK切换配置教程：<http://blog.csdn.net/tianxiawuzhei/article/details/48263789>

## 三、Android环境

同Windows环境下一样，同样适用Android SDK Manager来管理Android的SDK。还是在“AndroidDevTools”上下载Mac版，下载地址：[Android SDK Manage](http://www.androiddevtools.cn/)。
然后解压到指定的文件夹下，最后就是配置环境变量了。方法如下：

1. 找到 `~/ .bash_profile`文件；
2. 使用`vi ~/ .bash_profile`将下面的内容加入到.bash_profile文件中：
// 如果你不是通过Android Studio安装的sdk，则其路径可能不同，请自行确定清楚。
`export ANDROID_HOME=~/Library/Android/sdk`
3. 最后运行`source /etc/profile`使其立即生效。方法类似Java环境变量的配置，如果不明白可以看看上面的Java环境变量的配置。

至此，Mac下Android环境已准备就绪。

**欢迎大佬纠错指导，欢迎交流学习。**

>GitHub：[https://github.com/Code4GL](https://github.com/Code4GL)
公众号：code_everything
QQ：771841496
邮箱：guanli1991@163.com

![code_everything](/images/code_everything.jpg)
