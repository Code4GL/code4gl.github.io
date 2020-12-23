---
title: 13ReactNative系列之安全策略
date: 2020-12-23 11:11:15
excerpt: 对于安全问题更多的是出现在Android系统上，IOS由于系统的封闭保证了App的安全。即使这样，也没有绝对的安全，安全都是相对的，我们只能尽可能的做到安全。
tags:
    - ReactNative
    - 混合App
categories: ReactNative系列
---

![](https://upload-images.jianshu.io/upload_images/18236822-e9d8ac4cb99f3b3f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
# 一、概述
对于安全问题更多的是出现在Android系统上，IOS由于系统的封闭保证了App的安全。即使这样，也没有绝对的安全，安全都是相对的，我们只能尽可能的做到安全。
Android安全问题可以参考[移动App安全测试](https://www.cnblogs.com/yinlili/p/9883189.html)这篇文章，涵盖的面比较广，下面主要介绍通常的数据安全处理，目的是增加破解者的难度。
# 二、代码混淆
主要防止Apk被反编译后对源码的解读。在/Android/app/build.gradle文件中将enableProguardInReleaseBuilds值设置为true。

![](https://upload-images.jianshu.io/upload_images/18236822-ad1e35aa04a1ff0c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> 注意：代码混淆后可能会出现打包后App第三方功能无法使用，这可能是没有忽略第三方依赖的代码混淆导致的。需要在`proguard-rules.pro`文件中添加需要忽略混淆的第三方组件。如忽略微信相关组件：
```
# 微信相关
-keep class com.tencent.mm.sdk.** {
   *;
}
```
# 三、数据加密
数据加密使用当前比较流行的`RSA`非对称加密，结合`MD5`数字签名对敏感信息及报文进行加密和防篡改处理。
```
1. 在服务器端生成一对公钥和私钥，将公钥存放在客户端；
2. 在敏感信息输入后，用公钥进行RSA加密，后台处理时需要用私钥进行解密。对于密码等不需要解密出明文的信息，需要和后台开发商量好是否进行MD5处理后储存；
3. 请求时将报文进行MD5签名，用公钥对签名串进行RSA加密，然后将明文和加密串一起传给后台；
4. 后台接收后用私钥进行解密，之后验签，验签通过后再处理请求。
```
- 前端处理
```
1. 对于需要解密的敏感信息（如身份证号、手机号等），需要进行RSA加密处理；
2. 对于不需要解密的敏感信息（如密码等），直接使用MD5处理后传给后台；
3. 发送请求时，将报文进行MD5签名后，使用RSA加密处理，最后将签名密文和报文明文传给后台；
4. MD5处理时需要做加盐处理，内容规则由前后端商量决定。
```
- 后台处理
```
1. 将明文进行MD5处理得到MD5串1；
2. 将签名密文解密得到MD5串2；
3. 将串1和串2进行匹配，匹配成功后进入业务了逻辑处理；
4. 对RSA加密的敏感信息需要进行解密处理；对MD5处理的敏感信息直接储存。
```
# 四、Apk包加固
以上方案安全的基础是Apk包不被破解的情况下，但是当下反编译已经很容易就破解了安装包，包括代码混淆等手段只是增加了破解难度而已，并没有彻底安全的方案，我们需要做的就是尽可能增加破解难度。如果需要更高的安全要求，此时就需要第三方机构了，为Apk包加壳等加强了安装包的安全，但是是需要付费的。

**欢迎大佬纠错指导，欢迎交流学习。**

>GitHub：[https://github.com/Code4GL](https://github.com/Code4GL)
公众号：code_everything
QQ：771841496
邮箱：guanli1991@163.com

![code_everything](/images/code_everything.jpg)