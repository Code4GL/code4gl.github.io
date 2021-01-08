---
title: Flutter问题汇总
date: 2021-01-08 16:33:11
excerpt: Flutter问题汇总
categories: Flutter
tags:
    - Flutter问题
---

![flutter](/images/flutter/flutter.png)

## 一、概述

### 1.1 版本

```terminal
[√] Flutter (Channel stable, 1.22.5, on Microsoft Windows [Version 10.0.18363.1256], locale zh-CN)
    • Flutter version 1.22.5 at D:\flutter_windows_1.22.5-stable\flutter
    • Framework revision 7891006299 (4 weeks ago), 2020-12-10 11:54:40 -0800
    • Engine revision ae90085a84
    • Dart version 2.10.4
    • Pub download mirror https://pub.flutter-io.cn
    • Flutter download mirror https://storage.flutter-io.cn


[√] Android toolchain - develop for Android devices (Android SDK version 30.0.3)
    • Android SDK at C:\Users\Code4GL\AppData\Local\Android\sdk
    • Platform android-30, build-tools 30.0.3
    • Java binary at: D:\Android Studio\jre\bin\java
    • Java version OpenJDK Runtime Environment (build 1.8.0_242-release-1644-b01)
    • All Android licenses accepted.

[!] Android Studio (version 4.1.0)
    • Android Studio at D:\Android Studio
    X Flutter plugin not installed; this adds Flutter specific functionality.
    X Dart plugin not installed; this adds Dart specific functionality.
    • Java version OpenJDK Runtime Environment (build 1.8.0_242-release-1644-b01)

[!] Connected device
    ! No devices available

! Doctor found issues in 2 categories.
```

## 二、版本问题

### 2.1 Android版本问题

#### 2.1.1 图片引入在Android 8及其以下版本正常，在Android 9+版本上Crash

`Image`引入在Android 8及其以下版本正常，在Android 9+版本上Crash，还没找到原因。

### 2.2 IOS版本问题

### 2.3 Flutter版本问题
