---
title: 10ReactNative系列之react-native生命周期
date: 2020-12-23 11:06:02
excerpt: 开发RN，必不可少的就是它的基础框架react-native。对于框架的学习，官网上有很多很详细的介绍，这里就不多讲了。像`props`、`state`等重要属性都有讲解，只是讲的比较简单，不太好理解。如果感觉不好理解，推荐去React官网看看。因为RN就是基于reactjs来编写的，下沉的去学习reactjs是使自己的操作更规范的一个不错方法。
tags:
    - ReactNative系列
    - 混合App
categories: ReactNative
---

![reactnative](/images/reactnative/reactnative.jpg)

## 一、概述

开发RN，必不可少的就是它的基础框架react-native。对于框架的学习，官网上有很多很详细的介绍，这里就不多讲了，[ReactNative英文官网](https://facebook.github.io/react-native/)和[ReactNative中文官网](https://reactnative.cn/)。像`props`、`state`等重要属性都有讲解，只是讲的比较简单，不太好理解。如果感觉不好理解，推荐去[React官网](https://zh-hans.reactjs.org/)看看。因为RN就是基于reactjs来编写的，下沉的去学习reactjs是使自己的操作更规范的一个不错方法。
> 特别推荐一篇[React 哲学](https://zh-hans.reactjs.org/docs/thinking-in-react.html)，它主要讲从UI设计到状态处理，最后到数据流的一个过程。有一定基础的RN开发一定要看看，**特别重要**。

![react](/images/reactnative/reactnativeSeries/reactnativeSeries10/react.png)

## 二、生命周期图谱

![reactLifes](/images/reactnative/reactnativeSeries/reactnativeSeries10/reactLifes.png)

如图，可以把RN组件生命周期大致分为三个阶段：

1. 第一阶段：是组件第一次绘制阶段，如图中的上面虚线框内，在这里完成了组件的加载和初始化；
2. 第二阶段：是组件在运行和交互阶段，如图中左下角虚线框，这个阶段组件可以处理用户交互，或者接收事件更新界面；
3. 第三阶段：是组件卸载消亡的阶段，如图中右下角的虚线框中，这里做一些组件的清理工作。

## 三、生命周期函数

### 3.1getDefaultProps

在组件创建之前，会先调用getDefaultProps()，这是全局调用一次，严格地来说，这不是组件的生命周期的一部分。在组件被创建并加载后，首先调用getInitialState()，来初始化组件的状态。

### 3.2getInitialState

该函数用于对组件一些状态进行初始化，不同于getDefaultProps,在以后的过程中，会再次调用，所以可以将控制控件状态的一些变量放在这里初始化，比如控件上显示的文字，可以通过`this.state`来获取值，通过`this.setState`来修改state值。注意：一旦调用了`this.setState`方法，组件一定会调用render方法，对组件进行再次渲染，不过，React框架会根据DOM的状态自动判断是否需要真正渲染。

### 3.3componentWillMount

准备加载组件，会调用componentWillMount()，这个函数调用时机是在组件创建，并初始化了状态之后，在第一次绘制render()之前。可以在这里做一些业务初始化操作，也可以设置组件状态。这个函数在整个生命周期中只被调用一次。

```reactnative
void componentWillMount()
```

### 3.4render

render函数会插入jsx生成的dom结构，react会生成一份虚拟dom树，在每一次组件更新时，在此react会通过其diff算法比较更新前后的新旧DOM树，比较以后，找到最小的有差异的DOM节点，并重新渲染。

### 3.5componentDidMount

在组件第一次绘制之后，会调用componentDidMount()，通知组件已经加载完成。这个函数调用的时候，其虚拟 DOM 已经构建完成，你可以在这个函数开始获取其中的元素或者子组件了。需要注意的是，RN 框架是先调用子组件的componentDidMount()，然后调用父组件的函数。从这个函数开始，就可以和 JS 其他框架交互了，例如设置计时setTimeout或者setInterval，或者发起网络请求。这个函数也是只被调用一次。这个函数之后，就进入了稳定运行状态，等待事件触发。

```reactnative
void componentDidMount()
```

### 3.6componentWillReceiveProps

如果组件收到新的属性（props），就会调用componentWillReceiveProps()，输入参数nextProps是即将被设置的属性，旧的属性还是可以通过`this.props`来获取。在这个回调函数里面，你可以根据属性的变化，通过调用`this.setState()`来更新你的组件状态，这里调用更新状态是安全的，并不会触发额外的render()调用。

```reactnative
void componentWillReceiveProps(object nextProps)
```

### 3.7shouldComponentUpdate

当组件接收到新的属性和状态改变的话，都会触发调用shouldComponentUpdate(...)，输入参数nextProps和上面的componentWillReceiveProps函数一样，nextState表示组件即将更新的状态值。这个函数的返回值决定是否需要更新组件，如果true表示需要更新，继续走后面的更新流程。否者，则不更新，直接进入等待状态。默认情况下，这个函数永远返回true用来保证数据变化的时候 UI 能够同步更新。在大型项目中，你可以自己重载这个函数，通过检查变化前后属性和状态，来决定 UI 是否需要更新，能有效提高应用性能。

```reactnative
boolean shouldComponentUpdate(object nextProps, object nextState)
```

### 3.8componentWillUpdate

如果组件状态或者属性改变，并且上面的shouldComponentUpdate(...)返回为true，就会开始准更新组件，并调用componentWillUpdate()，输入参数与shouldComponentUpdate一样，在这个回调中，可以做一些在更新界面之前要做的事情。需要特别注意的是，在这个函数里面，你就不能使用`this.setState`来修改状态。这个函数调用之后，就会把`nextProps`和`nextState`分别设置到`this.props`和`this.state`中。紧接着这个函数，就会调用render()来更新界面了。

```reactnative
void componentWillUpdate(object nextProps, object nextState)
```

### 3.9componentDidUpdate

调用了render()更新完成界面之后，会调用componentDidUpdate()来得到通知，因为到这里已经完成了属性和状态的更新了，此函数的输入参数变成了`prevProps`和`prevState`。

```reactnative
void componentDidUpdate(object prevProps, object prevState)
```

### 3.10componentWillUnmount

当组件要被从界面上移除的时候，就会调用componentWillUnmount()，在这个函数中，可以做一些组件相关的清理工作，例如取消计时器、网络请求等。

```reactnative
void componentWillUnmount()
```

### 3.11constructor

构造函数在生命周期图谱中没有体现，但是它是组件构造时候必须调用的函数。constructor参数接受两个参数`props`,`context`，可以获取到父组件传下来的`props`,`context`,如果你想在constructor构造函数内部(注意是内部哦，在组件其他地方是可以直接接收的)使用`props`或`context`,则需要传入，并传入`super`对象。当然如果你只需要在构造函数内使用`props`或者`context`，那么只传入一个参数即可，如果都不可以，就都不传。注意：只要组件存在constructor,就必要要写super,否则`this`指向会错误。

## 四、调用

![reactLifes](/images/reactnative/reactnativeSeries/reactnativeSeries10/reactLifes1.png)

**欢迎大佬纠错指导，欢迎交流学习。**

>GitHub：[https://github.com/Code4GL](https://github.com/Code4GL)
公众号：code_everything
QQ：771841496
邮箱：guanli1991@163.com

![code_everything](/images/code_everything.jpg)
