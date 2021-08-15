---
title: "Flutter基础动画1分钟教程"
date: 2021-08-15T13:05:00+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---

另一个一分钟的教程。

![img](https://miro.medium.com/max/1400/1*QTdoN38m0-pso0C56Q6bTw.jpeg)

一种动画是让您的用户参与您的应用程序的最佳方式，用户会喜欢使用具有良好动画效果的应用程序。

今天我们来看看 Flutter 中的基本动画。

**首先，这是我们的主类和一个有状态的类**

![img](https://miro.medium.com/max/60/1*8ZQ8tgHfzRezDRZ6rtXeGw.png?q=20)

![img](https://miro.medium.com/max/700/1*8ZQ8tgHfzRezDRZ6rtXeGw.png)

如果你注意到这里，我在我的类中使用 singleTickerProviderStateMixin ，它会给我们一个 Ticker Provider，我们将在动画控制器中使用 Ticker provider（如果你想构建多个动画控制器，你必须使用 TickerProviderStateMixin 代替）。

to后^ h时，我们将定义我们的动画控制器。

![img](https://miro.medium.com/max/60/1*vcSEjGw_hTsWyvcU8HKjNA.png?q=20)

![img](https://miro.medium.com/max/700/1*vcSEjGw_hTsWyvcU8HKjNA.png)

在我们的动画控制器中，我们为动画提供了一个持续时间，并将当前状态下的ticker provider 提供给 vsync，然后我们将重复动画。

**我们要做的最后一件事是定义我们想要动画的小部件。**

![img](https://miro.medium.com/max/60/1*XjtZGTwzraCTGuzRjg3obA.png?q=20)

![img](https://miro.medium.com/max/700/1*XjtZGTwzraCTGuzRjg3obA.png)

这里我们有一个动画构建器，我们在其中提供图像，动画构建器接受一个动画，即 animationController，还有一个构建器，我们在其中定义了一个 Transform（Transform 创建一个转换其子项的小部件）。这里我们使用 Transform.scale 来做缩放动画，我们提供了一个缩放值，我们希望在其中为图像设置动画。

**这就是运行应用程序以查看放大动画，该动画会重复播放。**