---
title: "Flutter2源码阅读"
date: 2021-08-13T09:00:22+08:00
draft: false
tags: ["Flutter"]
categories: ["Flutter"]
---



# Flutter 2  源码阅读

进行源码分析之前，先了解一下官方文档中提供的核心架构图，它也代表着整个 **Flutter 架构**。

![archdiagram](https://luckly007.oss-cn-beijing.aliyuncs.com/img/archdiagram.png)

 **Flutter** 的架构主要分成三层：**Framework，Engine** 和 **Embedder**。

1)、***Framework***：**Framework**

 使用 **dart** 实现，包括 **Material Design** 风格的 Widget，**Cupertino**(针对 iOS)风格的 Widgets，文本/图片/按钮等基础 Widgets，渲染，动画，手势等。此部分的核心代码是：**flutter** 仓库下的 **flutter package**，以及 **sky_engine** 仓库下的 io，async ，ui (**dart:ui** 库提供了 Flutter 框架和引擎之间的接口)等 package。其中  **dart:ui** 库是对 Engine 中 Skia 库的 C++ 接口的绑定。向上层提供了 window、text、canvas 等通用的绘制能力，通过 **dart:ui** 库就能使用 Dart 代码操作 Skia 绘制引擎。所以我们实际上可以通过实例化 **dart:ui** 包中的类（例如 Canvas、Paint 等）来绘制界面。然而，除了绘制，还要考虑到协调布局和响应触摸等情况，这一切实现起来都异常麻烦，这也正是 **Framework** 帮我们做的事。渲染层 **Rendering** 是在 **::dart:ui** 库之上的第一个抽象层，它为你做了所有繁重的数学工作。为了做到这一点，它使用 RenderObject 对象，该对象是真正绘制到屏幕上的渲染对象。由这些 RenderObject 组成的树处理真正的布局和绘制。

## 2)、***Engine***：**Engine** 

使用 C++ 实现，主要包括：**Skia**，**Dart** 和 **Text**。**Skia** 是开源的二维图形库，提供了适用于多种软硬件平台的通用 API。在安卓上，系统自带了 **Skia**，在 iOS 上，则需要 APP 打包 **Skia** 库，这会导致 Flutter 开发的 iOS 应用安装包体积更大。 **Dart** 运行时则可以以 JIT、JIT Snapshot 或者 AOT 的模式运行 Dart 代码。

## 3)、***Embedder***：**Embedder** 

是一个嵌入层，即把 **Flutter** 嵌入到各个平台上去，这里做的主要工作包括渲染 Surface 设置，线程设置，以及插件等。从这里可以看出，**Flutter** 的平台相关层很低，平台（如 iOS）只是提供一个画布，剩余的所有渲染相关的逻辑都在 Flutter 内部，这就使得它具有了很好的跨端一致性。




