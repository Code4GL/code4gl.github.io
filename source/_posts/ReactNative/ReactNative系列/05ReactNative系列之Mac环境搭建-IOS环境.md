---
title: 05ReactNative系列之Mac环境搭建-IOS环境
date: 2020-12-23 10:28:08
excerpt: 众所周知，正常情况下，Windows系统只能进行Android开发，若想进行IOS开发，只有苹果系统才能完成，而且同时可以进行Android开发。因此，Mac上环境搭建分为IOS和Android两种。
tags:
    - ReactNative系列
    - 混合App
categories: ReactNative
---

![reactnative](/images/reactnative/reactnative.jpg)

## 一、概述

众所周知，正常情况下，Windows系统只能进行Android开发，若想进行IOS开发，只有苹果系统才能完成，而且同时可以进行Android开发。因此，Mac上环境搭建分为IOS和Android两种。

## 二、Homebrew安装

Homebrew是Mac环境下包管理器，用于安装Node.js和其他必备的工具软件，一般IOS开发者都会使用，推荐安装。
运行以下命令安装：

```terminal
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

安装过程中，若遇见/usr/local目录不可写的权限问题，则运行以下命令赋予权限：

```terminal
sudo chown -R `whoami` /usr/local
```

## 三、Node安装

和Windows一样，也必须具备Node环境。可以使用Homebrew来安装，运行以下命令来安装：

```terminal
brew install node
```

> 温馨提示：RN目前只支持Node 5.0以上的版本，不过如果使用Homebrew安装的话，都会是最新版本。

安装完成之后可以给npm设置国内镜像，以加快下载速度，同Windows一样：

```terminal
npm config set registry https://registry.npm.taobao.org --global
npm config set disturl https://npm.taobao.org/dist --global
```

同样，推荐使用Yarn来代替npm进行依赖的管理。安装Yarn：

```terminal
npm install -g yarn
```

安装完成之后，也可以配置国内镜像：

```terminal
yarn config set registry https://registry.npm.taobao.org --global
yarn config set disturl https://npm.taobao.org/dist --global
```

> 如果遇见“EACCES:permission denied”这样的权限报错，请参照Homebrew安装时的命令来修复。

## 四、Xcode安装

做过IOS开发的同志应该都知道Xcode，这是专门为IOS开发者提供的一款开发工具，里面包含了各个版本手机模拟器。开发RN项目的话，最主要是使用里面自带的iphone手机模拟器以及打包发布。而且需要Xcode8.0或者更高的版本。具体下载可以再AppStore中下载安装即可。

至此，IOS开发环境已准备就绪。

**欢迎大佬纠错指导，欢迎交流学习。**

>GitHub：[https://github.com/Code4GL](https://github.com/Code4GL)
公众号：code_everything
QQ：771841496
邮箱：guanli1991@163.com

![code_everything](/images/code_everything.jpg)
