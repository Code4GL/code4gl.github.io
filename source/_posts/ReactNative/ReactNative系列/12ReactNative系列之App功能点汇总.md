---
title: 12ReactNative系列之App功能点汇总
date: 2020-12-23 11:09:13
excerpt: 通过不断的学习实践，才发现其实RN的学习成本还是蛮高的。不仅需要熟悉前端的常用技术：JS、CSS、JSX、DOM等，还需要对Android和IOS等原生技术有所了解。这样才能做出一个功能完整的App。下面，主要对App相关功能做个汇总。
tags:
    - ReactNative系列
    - 混合App
categories: ReactNative
---

![reactnative](/images/reactnative/reactnative.jpg)

## 一、概述

通过不断的学习实践，才发现其实RN的学习成本还是蛮高的。不仅需要熟悉前端的常用技术：JS、CSS、JSX、DOM等，还需要对Android和IOS等原生技术有所了解。这样才能做出一个功能完整的App。下面，主要对App相关功能做个汇总。
> 主要内容：App图标、启动页、引导页、本地储存、数据请求、消息推送、分享。

## 二、App图标

### 2.1Android

Android的`icon`存放在`android/app/src/main/res/`目录，里面会有五个存放Android图标的文件夹，在RN0.49版本后每个文件夹会有两种图标：圆形和方形，分别为了适配Android不同厂商的图标。只需要将自己的`icon`替换掉原来默认的`icon`即可。
> 注意：`icon`名称、格式什么的都不要改变。

![androidIcon](/images/reactnative/reactnativeSeries/reactnativeSeries12/androidIcon.png)

如图，每个文件夹对应图标的尺寸如下：

- mipmap-hdpi：72x72
- mipmap-mdpi：48x48
- mipmap-xhdpi：96x96
- mipmap-xxhdpi：144x144
- mipmap-xxxhdpi：192x192

### 2.2IOS

IOS的话，就需要在XCode上进行添加，将对应的尺寸拖进对应的位置即可。

![iosIcon](/images/reactnative/reactnativeSeries/reactnativeSeries12/iosIcon.png)

分为五个类型，尺寸分别如下：

- iPhone Notification：20@2x、20@3x
- iPhone：29@2x、29@3x
- iPhone Spotlight：40@2x、40@3x
- iPhone App：60@2x、60@3x
- App Store：1024x1024

## 三、启动页

RN的启动页使用需要借助一个第三方组件[react-native-splash-screen](https://github.com/crazycodeboy/react-native-splash-screen)，进入GitHub主页可以看到它的集成过程和使用方法。这里就不讲它的集成过程，官网都有详细的教程。主要讲一下集成过程中可能会出现的问题。

![splashScreen](/images/reactnative/reactnativeSeries/reactnativeSeries12/splashScreen.png)

### 3.1Android

在集成Android的过程中会发现，在当前版本MainActivity中没有`onCreate`这个方法，需要把此方法复制到MainActivity中，同时将Bundle包也引入才能配置成功。

![splashScreen](/images/reactnative/reactnativeSeries/reactnativeSeries12/splashScreen1.png)

和图标一样，Android的启动页图片也存放在`android/app/src/main/res/`目录，只是文件夹名称不同了。

![splashScreen](/images/reactnative/reactnativeSeries/reactnativeSeries12/splashScreen2.png)

Android启动图对应尺寸如下：

- drawable-hdpi：480x800
- drawable-mdpi：320x480
- drawable-xhdpi：720x1280
- drawable-xxhdpi：960x1600
- drawable-xxxhdpi：1280x1920

### 3.2IOS

在IOS的配置过程中，不熟悉XCode的同志，不容易找到的第4步对应的位置：

![splashScreen](/images/reactnative/reactnativeSeries/reactnativeSeries12/splashScreen3.png)

如图所示：

![splashScreen](/images/reactnative/reactnativeSeries/reactnativeSeries12/splashScreen4.png)

需要双击弹出paths添加弹框，进行添加。

![splashScreen](/images/reactnative/reactnativeSeries/reactnativeSeries12/splashScreen5.png)

如果一切都配置成功，启动页也显示出来，发现卡在启动页不动了。此时是因为App启动后没有关闭启动页，需要添加代码关闭即可：

![splashScreen](/images/reactnative/reactnativeSeries/reactnativeSeries12/splashScreen6.png)

针对IOS8以上的版本整理了如下启动页需要的尺寸：

- iPhoneXS/MAX：1242x2688
- iPhoneXR：828x1792
- iPhoneX/XS：1125x2436
- RetinaHD5.5：1242x2208
- RetinaHD4.7：750x1334

## 四、引导页

引导页一般为可以滑动的几个页面组成，通常可以理解为全屏的轮播图，在轮播图的最后一页给个入口进入App。需要注意的是，引导页只会在用户首次打开App时才会显示，此时就需要利用RN的本地储存`AsyncStorage`了。
> 首次进入App时需要储存一个值用于判断用户是否已经打开过App。在下次进入时候去判断是否有此值，若有则是老用户，直接进入App；若没有，则是新用户，需要进入引导页。

![guidePages](/images/reactnative/reactnativeSeries/reactnativeSeries12/guidePages.png)

我们可以借助一个轮播图的组件来实现以上效果：[react-native-swiper](https://github.com/leecade/react-native-swiper)
。由于是使用全屏的图片，Android和IOS在样式上要做适当的适配。

![guidePages](/images/reactnative/reactnativeSeries/reactnativeSeries12/guidePages1.png)

## 五、本地储存

`AsyncStorage`是一个简单，未加密，异步，持久，键值存储系统，是应用程序的全局。应该使用它来代替LocalStorage。在iOS上，`AsyncStorage`由本机代码支持，该代码将小值存储在序列化字典中，将较大值存储在单独的文件中。在Android上，`AsyncStorage`将根据可用内容使用RocksDB或SQLite。

![asyncStorage](/images/reactnative/reactnativeSeries/reactnativeSeries12/asyncStorage.png)

其次，这个组件已经转移至[react-native-community](https://github.com/react-native-community)，后期react-native库中将不维护。
> 题外话，RN开始慢慢将类似这样的外围组件全部转移到`react-native-community`库中，比如Slider、ViewPagerAndroid等等。

官方建议不要直接使用AsyncStorage，需要对其做一定的封装。在使用过程中需要注意的是key值得管理，通常的做法是将key用配置文件创建好，再引用使用，不能使用直接定义key值得字符串。

## 六、数据请求

在数据请求方面，从原生的`XHR`到`Ajax`，后来又出现了`Fetch`和`Axios`，技术的更新驱使着开发者们不断的前进。

- Ajax大家都很熟悉了，最早的数据请求工具，对于多次请求的回调，很容易出现回调地狱，目前已不推荐使用；
- Fetch被称为是Ajax的替代品，基于promise设计的数据请求工具，相对比较底层，需要额外的封装会比较多。有兴趣的可以去学习学习[Fetch](https://developer.mozilla.org/zh-CN/docs/Web/API/Fetch_API/Using_Fetch)；
- Axios则是较为高级的请求工具，已经做好了拦截器等工具的封装，非常适合不愿再做封装的同志使用，简单轻便，[axios中文网](http://www.axios-js.com/zh-cn/)地址。
下面对axios做了简单的封装处理，可以作为参考：

```axios
import axios from 'axios';
import { Toast } from '@ant-design/react-native';
import { env, apiUrl } from '../../app.json';

const instance = axios.create({
  baseURL: apiUrl[env],
  timeout: 10000,
  headers: {
    'content-type': 'application/json;charset=UTF-8',
  },
});

// 请求拦截处理
instance.interceptors.request.use((config) => {
  // 在发送请求之前做些什么
  console.log('requestConfig', config);
  return config;
}, (error) => {
// 对请求错误做些什么
  console.log('requestError', error);
  Promise.reject(error);
});

// 返回拦截处理
instance.interceptors.response.use((response) => {
  // 对响应数据做点什么
  console.log('response', response);
  return response;
}, (error) => {
  // 对响应错误做点什么
  if (error.response) {
    switch (error.response.status) {
      case 404:
        Toast.fail('页面丢啦！');
        break;
      case 500:
        Toast.fail('服务器故障！');
        break;
      case 503:
        Toast.fail('服务器繁忙！');
        break;
      default:
        Toast.fail('系统繁忙！', 2);
    }
  } else {
    Toast.fail('系统繁忙！', 2);
    console.log('responseMessage', error.message);
  }
  Toast.fail('系统繁忙！', 2);
  console.log('responseError', error.response);
  Promise.reject(error);
});

export const asiosGet = (url, params) => new Promise((resolve, reject) => {
  instance.get(url, params)
    .then((response) => {
      resolve(response.data);
    })
    .catch((error) => {
      reject(error);
    });
});

export const asiosPost = (url, params, config) => new Promise((resolve, reject) => {
  instance.post(url, params, config)
    .then((response) => {
      resolve(response.data);
    })
    .catch((error) => {
      reject(error);
    });
});
```

## 七、消息推送

### 7.1MobPush

MobPush放在第一位是因为它的免费，不仅是普通的推送免费，厂商推送也是免费的，这在其他平台是找不到的。其次，它的短信服务也有免费的，所以极力推荐。只可惜不支持RN，若要集成，需要做原生处理。[http://www.mob.com/mobService/mobpush](http://www.mob.com/mobService/mobpush)

![MobPush](/images/reactnative/reactnativeSeries/reactnativeSeries12/mobpush.png)

### 7.2极光推送

[https://www.jiguang.cn/push](https://www.jiguang.cn/push)

![jiguangpush](/images/reactnative/reactnativeSeries/reactnativeSeries12/jiguangpush.png)

### 7.3友盟推送

[https://www.umeng.com/push](https://www.umeng.com/push)

![umengpush](/images/reactnative/reactnativeSeries/reactnativeSeries12/umengpush.png)

### 7.4个推推送

[https://www.getui.com/cn/getui.html](https://www.getui.com/cn/getui.html)

![getui](/images/reactnative/reactnativeSeries/reactnativeSeries12/getui.png)

### 7.5腾讯信鸽推送

[https://xg.qq.com/](https://xg.qq.com/)

![xg](/images/reactnative/reactnativeSeries/reactnativeSeries12/xg.png)

### 7.6百度云推送

[http://push.baidu.com/](http://push.baidu.com/)

![baidu](/images/reactnative/reactnativeSeries/reactnativeSeries12/baidu.png)

### 7.7阿里云推送

[https://help.aliyun.com/product/30047.html?spm=a2c4g.11186623.6.540.614a52e79dFt5d](https://help.aliyun.com/product/30047.html?spm=a2c4g.11186623.6.540.614a52e79dFt5d)

![aliyun](/images/reactnative/reactnativeSeries/reactnativeSeries12/aliyun.png)

## 八、分享

对于分享，首先要确定分享到哪些平台，根据需求再来找需要的组件可能是比较好的选择。

### 8.1推送平台分享

对于App服务平台，一般都会推出App的一系列产品方案，所以有推送的一般都会有分享等其他App需要的基本功能。比如MobShare、极光分析、友盟分享等等，可以去平台的官网找找。

### 8.2react-native-share

react-native-share是`react-native-community`社区维护的一个分享组件，但是大多数面向的是国外的平台，国内的比较少，地址[https://github.com/react-native-community/react-native-share](https://github.com/react-native-community/react-native-share)。

![share](/images/reactnative/reactnativeSeries/reactnativeSeries12/share.png)

为此，国内有开发者已经推出了自己的分享组件：[https://github.com/songxiaoliang/react-native-share](https://github.com/songxiaoliang/react-native-share)，有点可惜的是推广的不够，star太少了，不过并不影响使用。

![share](/images/reactnative/reactnativeSeries/reactnativeSeries12/share1.png)

### 8.3react-native-wechat

react-native-wechat是专门为微信推出的一个组件，集成了微信登录、支付、分享等一系列功能，如果只是使用到了微信，那么这绝对是个最好的选择。[https://github.com/yorkie/react-native-wechat](https://github.com/yorkie/react-native-wechat)

![share](/images/reactnative/reactnativeSeries/reactnativeSeries12/share2.png)

> 唯一有点问题的是，目前的分享小程序功能还没实现。不过已经有puti94大佬fork了项目，实现了分享小程序，愿早点合到master上去。[https://github.com/puti94/react-native-wechat](https://github.com/puti94/react-native-wechat)

![share](/images/reactnative/reactnativeSeries/reactnativeSeries12/share3.png)

**欢迎大佬纠错指导，欢迎交流学习。**

>GitHub：[https://github.com/Code4GL](https://github.com/Code4GL)
公众号：code_everything
QQ：771841496
邮箱：guanli1991@163.com

![code_everything](/images/code_everything.jpg)
