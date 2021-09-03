---
title: "《Flutter不要担心垃圾回收器》"
subtitle: ""
date: 2021-07-31T18:41:57+08:00
lastmod: 2021-07-31T18:41:57+08:00
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



> 本文主要介绍翻译自 [Flutter: Don’t Fear the Garbage Collector - Flutter - Medium](https://medium.com/flutter/flutter-dont-fear-the-garbage-collector-d69b3ff1ca30)。这篇文章简要地说明了 Flutter 垃圾收集器的工作原理，以及它对 Flutter 应用的影响。

<!--more-->

本文要点总结如下：

- Dart 运行时支持 JIT 和 AOT 两种不同模式，分别用于 debug 和 release
- Dart 垃圾收集器支持分代回收
- Dart 垃圾收集器新生代清理阶段是阻塞的，但速度比并行标记/并发清理阶段速度要快
- Flutter 引擎通知 Dart 垃圾收集器新生代清理阶段在应用空闲或没有用户交互时工作，减少到 UI 性能影响
- 开发者通常不应通过持有对象引用来干预 Dart 垃圾回收

Flutter 使用 [Dart](https://www.dartlang.org/) 作为开发语言和运行时。Dart 的运行时一开始就支持 debug 模式和 release 模式，但两者之间有很大差异。

在 debug 模式下，Dart 的大部分模块被加载到设备：包括 Dart 运行时、JIT 编译器/解释器 (Android 下是 JIT 编译器，iOS 下是解释器)、调试服务以及分析服务。而在 release 模式下，JIT/解释器和调试服务被移除，只保留 Dart 运行时，它的体积在 Flutter 应用体积中占大头。

![Debug 模式与 Release 模式](https://luckly007.oss-cn-beijing.aliyuncs.com/img/15734792877537.jpg)

Dart 的运行时中包含一个垃圾收集器，它是实例化对象以及对象变得不可达过程中分配和回收内存的必要组件。

Flutter 应用中会产生大量对象。这些对象在 Stateless Widget 渲染到屏幕时被创建，当应用状态改变或 Widget 不可见时被销毁或重建，大部分 Wiget 对象都是短生命周期。对 UI 复杂的应用来说，可能有数几千个 Widget。

那么 Flutter 开发者需要担心垃圾收集器吗？Flutter 会非常频繁创建和销毁对象，开发者需要限制这种行为吗？新手 Flutter 开发者创建为一段时间内不会变化的 Widget 创建引用，并且在状态数据保留这些引用以避免 Widget 重建的做法很常见。

**但不要这样做！**

担心 Dart 的垃圾收集器是毫无根据的，因为它有分代回收架构，并且其实现为快速创建和销毁对象优化过。大部分场景下，你应当让 Flutter 引擎自行创建和销毁所有的 Widget。

# Dart 垃圾收集器

Dart 的垃圾收集器支持分代回收 ([generational](https://en.wikipedia.org/wiki/Tracing_garbage_collection#Generational_GC_(ephemeral_GC)))，垃圾回收由两个阶段组成：新生代清理阶段以及并行标记收集阶段。

## 调度

为最小化垃圾收集对应用和 UI 性能的影响，垃圾收集器为 Flutter 引擎提供勾子，当引擎检查到应用空闲并且没有用户交互时会通知垃圾收集器开始工作。这可以让垃圾收集器有机会在不影响性能的情况下进行收集。垃圾收集器还可以在空闲期运行滑动压缩(sliding compaction)，减少内存碎片以降低内存占用。

## 新生代清理器

新生代清理器 (Young Space Scavenger)。这个阶段用于清理那短生命周期的临时对象，比如无状态的 Widget。尽管它是阻塞式，但仍然比第二阶段的标记/清理过程快得多，再加上上述的调度策略，实际上消除掉了应用运行期间可感知的暂停。

分配对象实质上是分配一块连续的内存，对象被创建时会分配到下一块可用的已分配内存空间，直到已分配内存耗尽。Dart 使用 bump pointer allocation 算法在新的空间中快速分配 (原文：Dart uses bump pointer allocation to rapidly allocate in new space, making the process very fast)

新的内存空间 (或称之为 nursery) 是新对象分配的地方，由两部分组成(也就是 semi spaces)。任何时刻只使用这两部分中的一部分：一部分活跃，则另一部分不活跃。新对象分配在活跃的那部分中，当这一部分被占满时，将仍然存活的对象从活跃部分拷贝到不活跃部分，忽略已死亡的对象。之后不活跃的部分变成活跃部分，如此往复。

垃圾收集器从根对象开始判断哪些对象存活，哪些对象死亡，比如栈变量 (stack variables) 是一种根对象。垃圾收集器会检查栈变量引用了谁，然后移动这些被引用的对象，之后垃圾收集器又检查被移动的对象指向什么地方，并且继续移动被引用的对象。这个过程一直继续直到所有存活对象被移动。已死亡的对象不被引用，因此被直接丢弃。未来某次垃圾收集过程中存活的对象被拷贝并覆盖掉这些已死亡的对象。

更多信息请参考 [Cheney’s algorithm](https://en.wikipedia.org/wiki/Cheney's_algorithm)。

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/img/15734792877537.jpg)

## 并行标记和并发清理

当对象到达某个寿命，它们被提升到另一块新的内存空间，该空间由第二代收集器管理：标记清理。

这个垃圾收集技术有两个阶段：首先遍历对象图并标记仍在使用的对象。在第二个阶段扫描整个内存，回收所有没被标记的对象。之后所有的标记都被清理。

这种形式的垃圾收集会在标记阶段阻塞；不能进行任何内存操作，UI 线程也被阻塞。这种形式的垃圾收集不像新生代清理器处理短暂对象那样频繁，但 Dart 运行时有时会暂停以执行垃圾收集。考虑到 Flutter 垃圾收集的调度策略 (见上一节)，暂停的影响会被最小化。

但是要注意如果应用并不遵守弱代假设(原文：the weak generational hypothesis， 这个假设认为大部分对象是短生命周期的)，那么这种形式的垃圾收集会运行得更多。考虑到 Flutter Widget 的工作原理，虽然这种情况的可能性并不大，但要牢记。

# Isolates

值得注意的是，Dart isolates 拥有自己私有的堆，每个 isolate 堆跟其他 isolate 完全独立。每个 isolate 在独立线程中运行，一个 isolate 上的垃圾收集事件并不影响另外 isolate 的性能。使用 isolate 是避免阻塞 UI 和密集计算压力的好方法。

# 总结

到此你应该知道：Dart 使用一个强大的分代垃圾收集器来最小化 Flutter 应用中的垃圾收集时的阻塞影响。不要担心垃圾收集器，它已经考虑到应用的最大利益。