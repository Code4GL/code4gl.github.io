---
title: 03ReactNative系列之Windows环境搭建-Node环境
date: 2020-12-23 10:19:26
excerpt: Node是一个让JavaScript运行在服务端的开发平台，它让JavaScript成为与PHP、Python、Perl、Ruby 等服务端语言平起平坐的脚本语言。发布于2009年5月，由Ryan Dahl开发，实质是对Chrome V8引擎进行了封装。同样，RN开发主要也是基于JavaScript编码，所以Node环境也是必不可少的。
tags:
    - ReactNative系列
    - 混合App
categories: ReactNative
---

![reactnative](/images/reactnative/reactnative.jpg)

## 一、概述

Node是一个让JavaScript运行在服务端的开发平台，它让JavaScript成为与PHP、Python、Perl、Ruby 等服务端语言平起平坐的脚本语言。发布于2009年5月，由Ryan Dahl开发，实质是对Chrome V8引擎进行了封装。同样，RN开发主要也是基于JavaScript编码，所以Node环境也是必不可少的。

## 二、下载安装

RN官网教程是利用Chocolatey来安装，这是一个Windows上的软件管理器，可以下载Windows上的很多软件，但是对于Windows用户来说，下载软件通常都是在官网下载，而很少使用命令行来下载，所以不推荐使用。下面是提供两个网址来下载，第一个是Node的官方网站，第二个是Node的中文网站。对于没有使用科学上网工具的同志请选择国内的中文网站下载。[英文官网](https://nodejs.org/en/)和[中文官网](http://nodejs.cn/download/)。
![node-download](/images/reactnative/reactnativeSeries/reactnativeSeries03/node-download.png)
下载长期支持版本，最新版本可能会不稳定。对于安装没有什么特别注意的，主要是选择好安装路径以及是否要添加到环境变量中，此时要选择添加，否则后面需要自己手动配置。
![node-download](/images/reactnative/reactnativeSeries/reactnativeSeries03/node-download1.png)

## 三、安装成功验证

使用win+R，输入“cmd”打开dos命令行，键入“node -v”如图显示对应的版本号，即安装成功。若未成功请查看是否配置好了环境变量。
![node-download](/images/reactnative/reactnativeSeries/reactnativeSeries03/node-download2.png)

## 四、Npm

npm全称为“Node Package Manager”。顾名思义，这是Node自带的依赖包管理器。主要功能就是管理Node依赖，包括安装、卸载、更新、查看等。做过后端的同志可能会明白，这类似后端中的jar包。Npm是跟随Node同时安装在电脑上的，在安装好Node之后，可以在dos中键入“npm -v”查看当前npm版本。
![npm](/images/reactnative/reactnativeSeries/reactnativeSeries03/npm.png)

## 五、Yarn（推荐）

Apache Hadoop YARN（Yet Another Resource Negotiator，另一种资源协调者）是一种新的Hadoop资源管理器，它是一个通用资源管理系统，可为上层应用提供统一的资源管理和调度，它的引入为集群在利用率、资源统一管理和数据共享等方面带来了巨大好处。[官网](https://yarn.bootcss.com/)
对于Node的亲儿子Npm来说，Yarn最明显的优点：

1. 离线模式
yarn有个缓存目录，在安装依赖的同时，会将依赖放入缓存中，下次安装时直接从缓存中读取，避免的不必要的网络请求，大大提高了效率。
2. 并行执行
npm会等一个包完全安装完才会跳到下一个包，但yarn会并行执行包，因此速度会快很多。yarn在速度方面优势明显。
3. 版本锁定
npm在包的依赖层次比较深时，版本控制不够好。会出现相同package.json，不同人的电脑上安装出不同版本的依赖包。

> 下载安装：对于Windows环境下，可以有两种方法下载，即下载msi安装文件和npm使用命令来安装。但是推荐使用npm来安装，因为后期依赖的管理都是通过命令行来管理，这里就可以提前熟悉，前提是具有Node环境。
在具有Node环境后，打开dos键入“npm install yarn -g”回车，等待安装完成。完成之后就可以通过命令“yarn --version”或者“yarn -v”来查看Yarn版本，若正常显示版本号则说明安装成功。

## 六、国内镜像仓库

推荐使用淘宝镜像仓库，这里npm和yarn都可以配置。把“npm”改为“yarn”即可为yarn设置国内镜像。

```terminal
npm config set registry https://registry.npm.taobao.org --global
npm config set disturl https://npm.taobao.org/dist --global
```

> 温馨提示：若在网上下载下来的项目本地运行时，会出现依赖下载失败的情况，这时需要将将镜像去掉，再重新下载。原因可能是镜像仓库中有些依赖未及时更新。此时可将仓库设为`npm config set registry https://registry.npmjs.org/ --global`即可。使用`npm config list`命令即可查看相关配置。

**欢迎大佬纠错指导，欢迎交流学习。**

>GitHub：[https://github.com/Code4GL](https://github.com/Code4GL)
公众号：code_everything
QQ：771841496
邮箱：guanli1991@163.com

![code_everything](/images/code_everything.jpg)
