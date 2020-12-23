---
title: 11ReactNative系列之react-navigation路由嵌套
date: 2020-12-23 11:08:02
excerpt: react-navigation是FaceBook在2017年1月份才推出的一款导航组件，致力于解决导航卡顿，数据传递，Tabbar和Navigator布局，支持redux。并且在0.44发布的时将之前一直存在问题的Navigator废弃了，在之后的项目中几乎都使用其来作为导航，有原生导航的体验。对于react-navigation的基础学习，Navigation官网已经写得很详细了，网上也有很多教程。此次，主要对导航路由嵌套策略做一点讨论。
tags:
    - ReactNative
    - 混合App
categories: ReactNative系列
---

![ReactNative.jpg](https://upload-images.jianshu.io/upload_images/18236822-e9d8ac4cb99f3b3f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 一、概述

react-navigation是FaceBook在2017年1月份才推出的一款导航组件，致力于解决导航卡顿，数据传递，Tabbar和Navigator布局，支持redux。并且在0.44发布的时将之前一直存在问题的Navigator废弃了，在之后的项目中几乎都使用其来作为导航，有原生导航的体验。对于react-navigation的基础学习，[Navigation官网](https://reactnavigation.org/)已经写得很详细了，网上也有很多教程。此次，主要对导航路由嵌套策略做一点讨论。

## 二、嵌套策略

随着react-navigation的不断更新，路由策略和API的使用也会不断更新。通过在不断的实践中摸索，基于react-navigation3.x的路由嵌套策略更新如下。

![image.png](https://upload-images.jianshu.io/upload_images/18236822-8c50423887c786b1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如图所示，路由总共分为五层：

1. 第一层：路由容器层，负责管理您的应用状态并将顶级导航器链接到应用环境。在Android上，应用容器使用链接API来处理后退按钮。容器还可以配置为保持导航状态。在Web上，您可以使用createBrowserApp和handleServerRequest维护顶级导航器的状态。
2. 第二层：抽屉导航栏，侧边抽屉导航设置，可以在其中做标签导航的嵌套。如果没有抽屉导航，这一层可以去掉。
3. 第三层：标签导航栏，分为ModalStack，AuthStack和MainStack即模态框、身份校验和主路由。ModalStack中主要存放Loading等公共页面，页面消失之后需要重置路由，操作完成之后不能返回之前的路由栈；AuthStack主要存放登录、忘记密码等身份校验页面；MainStack主要存放TabStack和其他页面。
4. 第四层：选项卡导航，主要存放App底部选项卡导航，一般为4-5个最佳。这一层嵌套了一层StackNavigator，为了配合每个模块首页Navigator个性化处理。如果四个模块共用一个Navigator，那处理的逻辑太复杂，不好维护。
5. 第五层：具体页面，主要是具体的业务逻辑页面的存放。

**欢迎大佬纠错指导，欢迎交流学习。**

>GitHub：[https://github.com/Code4GL](https://github.com/Code4GL)
公众号：code_everything
QQ：771841496
邮箱：guanli1991@163.com

![code_everything](/images/code_everything.jpg)
