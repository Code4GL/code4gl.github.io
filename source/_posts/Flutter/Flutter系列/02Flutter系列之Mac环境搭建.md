---
title: 02Flutter系列之Mac环境搭建
date: 2020-12-23 15:11:54
excerpt: 02Flutter系列之Mac环境搭建
categories: Flutter
tags:
    - Flutter系列
---

![flutter](/images/flutter/flutter.png)

## 一、前期工作

### 1.1配置仓库镜像

国内用户首先需要在环境变量中配置镜像地址：

```config
export PUB_HOSTED_URL=https://pub.flutter-io.cn
export FLUTTER_STORAGE_BASE_URL=https://storage.flutter-io.cn
```

> 注意： 此镜像为临时镜像，并不能保证一直可用，读者可以参考详情请参考[Using Flutter in China](https://github.com/flutter/flutter/wiki) 以获得有关镜像服务器的最新动态。

### 1.2Flutter下载

[Flutter下载](https://flutter.dev/docs/development/tools/sdk/releases?tab=macos#macos)，下载完成之后需要配置环境变量。环境变量`.bash_profile`文件参考：

```config
export PATH=/Users/用户名/Documents/flutter/flutter/bin:$PATH
export ANDROID_HOME="/Users/用户名/Documents/android_sdk" //android sdk目录，替换为你自己的即可
export PATH=${PATH}:${ANDROID_HOME}/tools
export PATH=${PATH}:${ANDROID_HOME}/platform-tools
export PUB_HOSTED_URL=https://pub.flutter-io.cn
export FLUTTER_STORAGE_BASE_URL=https://storage.flutter-io.cn
```

### 1.3环境检测

运行`flutter doctor`命令查看是否需要安装其它依赖项来完成安装。该命令检查您的环境并在终端窗口中显示报告。Dart SDK已经在捆绑在Flutter里了，没有必要单独安装Dart。 仔细检查命令行输出以获取可能需要安装的其他软件或进一步需要执行的任务（以粗体显示）。
> 报告主要分五个部分：
`Flutter`：是否安装了Flutter；
`Android toolchain`：安卓环境工具是否安装完全；
`iOS toolchain`：IOS环境工具是否安装完全；
`IDE`：开发工具（Android Studio、IntelliJ IDEA、VS Code）；
`Connected device`：已链接设备；

这是本人首次运行时检测的问题：

```terminal
Doctor summary (to see all details, run flutter doctor -v):
[✓] Flutter (Channel stable, v1.5.4-hotfix.2, on Mac OS X 10.13.6 17G6030,
    locale zh-Hans-CN)
 
[!] Android toolchain - develop for Android devices (Android SDK version 28.0.3)
    ✗ Android license status unknown.
      Try re-installing or updating your Android SDK Manager.
      See https://developer.android.com/studio/#downloads or visit
      https://flutter.dev/setup/#android-setup for detailed instructions.
[!] iOS toolchain - develop for iOS devices (Xcode 10.0)
    ✗ libimobiledevice and ideviceinstaller are not installed. To install with
      Brew, run:
        brew update
        brew install --HEAD usbmuxd
        brew link usbmuxd
        brew install --HEAD libimobiledevice
        brew install ideviceinstaller
    ✗ ios-deploy not installed. To install:
        brew install ios-deploy
    ✗ CocoaPods not installed.
        CocoaPods is used to retrieve the iOS platform side's plugin code that responds to your plugin usage on the Dart side.
        Without resolving iOS dependencies with CocoaPods, plugins will not work
        on iOS.
        For more info, see https://flutter.dev/platform-plugins
      To install:
        brew install cocoapods
        pod setup
[!] Android Studio (not installed)
[!] IntelliJ IDEA Ultimate Edition (version 2017.3.3)
    ✗ Flutter plugin not installed; this adds Flutter specific functionality.
    ✗ Dart plugin not installed; this adds Dart specific functionality.
[!] VS Code (version 1.35.1)
    ✗ Flutter extension not installed; install from
      https://marketplace.visualstudio.com/items?itemName=Dart-Code.flutter
[!] Connected device
    ! No devices available

! Doctor found issues in 6 categories.

```

#### 1.3.1Android license status unknown

运行`flutter doctor --android-licenses`：

```terminal
A newer version of the Android SDK is required. To update, run:
/Users/xxx/soft/AndroidSDK/tools/bin/sdkmanager --update
```

根据提示运行：`/Users/xxx/soft/AndroidSDK/tools/bin/sdkmanager --update`。拼网速的时候到了，有时候运行命令后没进入此状态，请重新运行几次。

![podFile](/images/flutter/flutterSeries/flutterSeries02/android-license.png)

下载安装完成之后，在此运行`flutter doctor --android-licenses`，会出现各种协议需要同意，一路输入`y`，然后`回车`即可。最后再运行`flutter doctor`监测一下，问题已经解决。

```terminal
[✓] Android toolchain - develop for Android devices (Android SDK version 28.0.3)
```

#### 1.3.2libimobiledevice and ideviceinstaller are not installed

根据提示，依次运行：

```terminal
brew update
brew install --HEAD usbmuxd
brew link usbmuxd
brew install --HEAD libimobiledevice
brew install ideviceinstaller
```

运行`brew update`会卡住不动，此时需要配置一下国内镜像：

```terminal
// 第一步，替换brew.git
cd "$(brew --repo)"
git remote set-url origin https://mirrors.ustc.edu.cn/brew.git
// 第二步：替换homebrew-core.git
cd "$(brew --repo)/Library/Taps/homebrew/homebrew-core"
git remote set-url origin https://mirrors.ustc.edu.cn/homebrew-core.git
```

之后再运行`brew update`，此过程会持续一段时间，请耐心等待。
运行`brew install --HEAD usbmuxd`可能会出现以下问题：

```terminal
Error: Xcode alone is not sufficient on High Sierra.
Install the Command Line Tools:
  xcode-select --install
```

根据提示运行`xcode-select --install`，安装完成后再继续上面步骤。
> 注意：此命令不是重新安装XCode，只是一些安装Ruby所需的命令行工具，无需紧张。

#### 1.3.3ios-deploy not installed

根据提示，依次运行：

```terminal
brew install ios-deploy
```

#### 1.3.4CocoaPods not installed

根据提示，依次运行：

```terminal
brew install cocoapods
pod setup
```

pod setup运行时会相当的慢，最好还是添加国内镜像。清华大学镜像使用方法：

```terminal
cd ~/.cocoapods/repos 
pod repo remove master
git clone https://mirrors.tuna.tsinghua.edu.cn/git/CocoaPods/Specs.git master
```

最后进入自己的工程，在自己工程的podFile第一行加上：

```terminal
source 'https://mirrors.tuna.tsinghua.edu.cn/git/CocoaPods/Specs.git'
```

使用前8kb/s的速度，看看使用后的速度，这还不是峰值。

![podFile](/images/flutter/flutterSeries/flutterSeries02/podFile.png)

经过一系列操作过后，再运行`pod setup`。运行完成之后，以为就要开始创建项目了，但是太天真了。

```terminal
Verify that all connected devices have been paired with this computer in Xcode.
If all devices have been paired, libimobiledevice and ideviceinstaller may require updating.
To update with Brew, run:
   brew update
   brew uninstall --ignore-dependencies libimobiledevice
   brew uninstall --ignore-dependencies usbmuxd
   brew install --HEAD usbmuxd
   brew unlink usbmuxd
   brew link usbmuxd
   brew install --HEAD libimobiledevice
   brew install ideviceinstaller
```

不过不要慌，找了很多资料发现重启一下就好了。实验了一下，发现的确可以，不过好像治标不治本。不管那么多了，在运行`flutter doctor`监测一下，除了IDE和设备之外，其他的环境都好了，设备的问题只要在启动App的时候打开模拟器就好了。

```terminal
Doctor summary (to see all details, run flutter doctor -v):
[✓] Flutter (Channel stable, v1.5.4-hotfix.2, on Mac OS X 10.13.6 17G7024,
    locale zh-Hans-CN)
 
[✓] Android toolchain - develop for Android devices (Android SDK version 28.0.3)
[✓] iOS toolchain - develop for iOS devices (Xcode 10.0)
[!] Android Studio (not installed)
[!] IntelliJ IDEA Ultimate Edition (version 2017.3.3)
    ✗ Flutter plugin not installed; this adds Flutter specific functionality.
    ✗ Dart plugin not installed; this adds Dart specific functionality.
[✓] VS Code (version 1.35.1)
[!] Connected device
    ! No devices available

! Doctor found issues in 3 categories.
```

#### 1.3.5Flutter extension not installed

推荐使用VS Code开发Flutter，所以只用处理一个IDE的问题即可。需要安装Dart插件。

**欢迎大佬纠错指导，欢迎交流学习。**

>GitHub：[https://github.com/Code4GL](https://github.com/Code4GL)
公众号：code_everything
QQ：771841496
邮箱：guanli1991@163.com

![code_everything](/images/code_everything.jpg)
