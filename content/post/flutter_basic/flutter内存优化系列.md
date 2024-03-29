---
title: "Flutter内存优化系列"
date: 2021-09-09T09:26:31+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter","dart"]
categories: ["Flutter","dart"]
---

您可能已经知道，**Flutter 中的**几乎所有内容都默认进行了优化和增强，这要归功于**Flutter**团队，他们为我们提供了非常有用的**开发工具**，可以帮助我们更有效地优化我们的应用程序......但我们不会谈论这些工具中的任何一个以及如何使用它们，相反，我们将谈论我们应该如何编写适当的代码段来更仔细和有效地处理内存。

# 这个系列是关于什么的？

看起来 一篇文章足以涵盖整个主题的所有内容，但这可能是一团糟，这就是为什么我决定将这个主题分成几个小的单独主题并制作一系列主题。

目前，本文将讨论以下内容：

- **在 ListView 中加载高分辨率图像**



让我们开始吧！

# **在 ListView 中加载高分辨率图像**

这可能是 Flutter 中可以完成的最简单的任务，而且确实如此。

在**ListView 中**加载大量图像可能会消耗大量设备内存 ( **RAM** )，甚至可能导致整个应用程序崩溃……因此，如果我们看一下以下代码：

![image-20210909092842680](https://luckly007.oss-cn-beijing.aliyuncs.com/image/image-20210909092842680.png)

代码 1.1听起来很容易阅读……您可以**随心所欲地**想象**_images** getter，但现在，它所能做的就是每次我们从服务器端(例如从 API)请求新页面时返回一堆图像。

## 这段代码有什么问题？

好吧，有几件事取决于究竟是什么会导致错误......例如：如果图像具有非常大的分辨率并且**ListView**不断获取和查看图像，那么这绝对是一个可怕的问题！

## 为什么？

简单地说，因为**ListView**默认情况下在整个过程中保持其子项**活着**(沿滚动)，并且不会再次**重新绘制**其子项并保持之前的状态，因此在向后滚动(向上)时不会丢失.

默认情况下，**ListView**将其子项保留在两件事中：

- 首先，它保持孩子的状态活着，所以一旦用户向后滚动它就不会丢失。
- 其次，如果用户也向后滚动，则不会再次重绘子项。

事实上，孩子被两个不同的小部件包裹起来，完成上述工作(保持状态，而不是再次重新绘制孩子)。

- [**RepainBoundries**](https://api.flutter.dev/flutter/widgets/RepaintBoundary-class.html)**(默认情况下每个孩子都用这个小部件包装，它只为孩子绘制一次以获得更高的性能)**
- [**AutomaticKeepAlive**](https://api.flutter.dev/flutter/widgets/AutomaticKeepAlive-class.html)**(默认情况下每个孩子都用这个小部件包装，这让每个孩子的状态保持活跃)**

## 问题！

在这里，**ListView**可以很好地保持它的孩子们拥有自己的状态，但是通过这样做.. 当用户向下滚动一点或孩子们在屏幕上不可见时，孩子们不会被处理或垃圾收集。 . 这就像将孩子缓存到内存中，以便**ListView**可以轻松地直接从内存中获取它们。

加载大量图像(高分辨率图像)的整个过程肯定会消耗大量内存，最终可能会导致应用程序崩溃。

## 那么，我们该如何解决这个问题呢？

为此，**ListView**和几乎所有其他可滚动小部件都有两个不同的有用布尔属性：

- [**addAutomaticKeepAlives**](https://api.flutter.dev/flutter/widgets/SliverChildListDelegate/addAutomaticKeepAlives.html)**(默认为真)**
- [**addRepaintBoundaries**](https://api.flutter.dev/flutter/widgets/SliverChildListDelegate/addRepaintBoundaries.html)**(默认为真)**

这两个属性默认是启用的，所以在我们的例子中..我们可以简单地禁用它们，以便不可见的子项将被自动处理和垃圾收集。



![image-20210909092904028](https://luckly007.oss-cn-beijing.aliyuncs.com/image/image-20210909092904028.png)



这可能会导致使用更多的 GPU 和 CPU 工作，因为重新绘制子项并管理状态，但它可以解决我们的内存问题，并且您可以获得非常高效的视图而不会出现明显问题。

# 就这样😆

我可以看到这篇文章有点小，写的时间更少，但老实说..在我的一些大项目中..我遇到了同样的问题，所以我解决了它并找到了如何借助 Flutter API 文档(由 Flutter 团队轻松提供)的帮助下正确解决它**。Flutter**为您提供了选择构建出色应用程序的正确选项的灵活性。
