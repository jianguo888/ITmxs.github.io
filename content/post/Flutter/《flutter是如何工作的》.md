---
title: "《Flutter是如何工作的》"
subtitle: ""
date: 2021-07-31T18:26:33+08:00
lastmod: 2021-07-31T18:26:33+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter"]
categories: ["Flutter"]

hiddenFromHomePage: false
hiddenFromSearch: false
twemoji: false

fontawesome: true
linkToMarkdown: true
rssFullText: false


featuredImage: "https://luckly007.oss-cn-beijing.aliyuncs.com/img/90c6cc12-742e-4c9f-b318-b912f163b8d0.png"
featuredImagePreview: "https://luckly007.oss-cn-beijing.aliyuncs.com/img/90c6cc12-742e-4c9f-b318-b912f163b8d0.png"

toc:
  enable: true
  auto: true
math:
  enable: false
lightgallery: false
license: ""
share:
  enable: true
comment: true
mapbox:
  accessToken: ""
---



> 本文主要介绍Flutter 是如何工作的？

<!--more-->



要点如下：

- Flutter 框架更像一个游戏引擎，而不是传统的应用框架
- Flutter 应用由 Widget 构成，Widget 被渲染到 Skia canvas，最后发送到平台
- Flutter 引擎在对应的平台 shell 中运行
- Flutter UI 发生变化时会被构建和渲染到 Skia Canvas
- Flutter 以 60fps 来更新 UI，并且使用 GPU 完成大部分工作

原文翻译如下。

跟其他框架相比，Flutter 以一种全新的方式构建，它更像一个游戏引擎而不是传统应用框架。我想讲讲在大层面上 Flutter 是如何工作的，以及它如何实现高性能。从大的层面来讲，**应用由 Widget 构成，Widget 被渲染到 Skia canvas，最后发送到平台。平台显示 canvas，并且按要求返回事件**。

![img](https://www.sunmoonblog.com/images/15944494180838.jpg)

应用在平台上以 AOT 直接运行 (Your app runs on the platform in natively, Ahead of Time complied.)

# 平台

Flutter 在平台上提供 `Shell`，用作 Dart VM 和宿主。Shell 是平台相关的，提供对平台原生 API 的访问支持并且建立平台相关的 canvas。

![img](https://www.sunmoonblog.com/images/15944503631949.jpg)

Shell 还用于和 IMEs 和系统应用生命周期事件通信。

# 引擎

引擎是平台之上的层，提供 Dart 运行时、Skia、Platform Channel 及其他内容。Flutter Wiki 中的[架构图](https://github.com/flutter/engine/wiki#architecture-diagram)中有更多细节。

![img](https://www.sunmoonblog.com/images/15944505039484.jpg)

# 框架

Flutter 引擎跟应用开发者最相关。它包含你开发应用时需要交互的所有内容。

![img](https://www.sunmoonblog.com/images/15944513053707.jpg)

Flutter 只需简单使用对应的风格就能保持跟 iOS 或 Android 原生一致风格。iOS 使用 Cupertino 风格，Android 使用 Material 风格。

Flutter 不使用原生组件，而是 Widget。Widget 是 Flutter 应用的组件。Flutter 将 UI 绘制到 Skia Canvas。

一旦 UI 完成渲染，就进入引擎环境。

# 渲染流水线

Flutter 更像游戏引擎，而不是应用框架。Flutter UI 发生变化时会被构建和渲染到 Skia Canvas。Flutter 以 60fps 来更新 UI，并且使用 GPU 完成大部分工作。(所以 Flutter UI 这么流畅)

# 更多

这里只是大体上讲了一下 Flutter 是如何工作的，实际上 Flutter 底层有许多细节。可以参考这些资料

代码库：

- [Flutter Framework](https://github.com/flutter/flutter)
- [Flutter Engine](https://github.com/flutter/engine)

技术文档：

- [Flutter System Architecture Diagrams](https://docs.google.com/presentation/d/1cw7A4HbvM_Abv320rVgPVGiUP2msVs7tfGbkgdrTy0I/edit#slide=id.p)
- [Technical Overview](https://flutter.io/technical-overview/)