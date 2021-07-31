---
title: "《FlutterPerformance分析工具简介》"
subtitle: ""
date: 2021-07-31T21:09:34+08:00
lastmod: 2021-07-31T21:09:34+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["flutter"]
categories: ["flutter"]

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



> Flutter 提供了不少性能分析工具，但感觉相关文档不咋的，散落在官方的各个地方，对开发者不太友好。另外，有的工具目前只在 Android Studio 中有，比如 Flutter Performance；甚至有的工具只在 debug 模式下可用。本文对这些工具简单整理一下，便于做性能优化时查找和参考。

<!--more-->

# Performance Overlay

开启 Performance Overlay 有多种方式，最简单的几种包括：

- Android Studio > Flutter Inspector > Performance Overlay
- Android Studio > Flutter Performance > Performance Overlay
- DevTools > Timeline > Performance Overlay

![-w477](https://www.sunmoonblog.com/images/15786503018684.jpg)

性能图层用两张图表显示应用的耗时信息。每一张图表都代表当前线程的最近 300 帧表现。

![img](https://www.sunmoonblog.com/images/15786490815418.jpg)

- 顶部的图形表示 GPU 线程所花费的时间
- 底部的图表显示了 UI 线程所花费的时间
- 竖轴表示耗时，沿竖轴的黑线是时间线 (间隔单位为 16ms)
- 横轴则表示帧，垂直的绿色条代表的是当前帧
- 卡顿时绿色条会变成红色条
  - 如果是在 UI 图表出现了红色竖条，则表明 Dart 代码消耗了大量资源
  - 而如果红色竖条是在 GPU 图表出现的，意味着场景太复杂导致无法快速渲染

更多信息请参考 [Flutter 线程](https://flutter.dev/docs/perf/rendering/ui-performance#flutters-threads)

使用 Performance Overlay 可以对 UI 性能问题进行**定性分析**，大致判断到底是 Dart 代码执行过慢(布局慢)还是场景复杂无法快速渲染(渲染慢)。

Performance Overlay 可用于 profile 模式。

# [PerformanceOverlay控件](https://api.flutter.dev/flutter/widgets/PerformanceOverlay-class.html)

[PerformanceOverlay](https://api.flutter.dev/flutter/widgets/PerformanceOverlay-class.html) 可以视为 Performance Overlay 的高级版本(Performance Overlay 正是基于 PerformanceOverlay 控件来实现的)。

通过如下方式在代码中开启 `PerformanceOverlay` 控件。[参考](https://flutter.dev/docs/testing/code-debugging#performance-overlay)。

```
class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      showPerformanceOverlay: true,
      title: 'My Awesome App',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: MyHomePage(title: 'My Awesome App'),
    );
  }
}
```

如果没有使用 `MaterialApp` 等控件，可以自己调用 `PerformanceOverlay.allEnabled(checkerboardOffscreenLayers: true,);` 来实现类似效果。

# [Observatory](https://dart-lang.github.io/observatory/) 之 Timeline

Observatory 是 Dart SDK 提供的用于分析和调试 Dart 程序的工具。可以使用 Observatory 来观察 Dart VM 内部状态，获取应用的实时数据。主要功能包括：

- 检查应用的耗时情况
- 检查已分配内存
- 检查哪些代码已执行
- 调试内存泄漏问题
- 调试内存碎片问题

虽然 Observatory 正在被 DevTools 慢慢取代，但某些情况下它提供的性能数据更为详细。

Observatory 的使用步骤：

1. VS Code 或命令行下打开 Observatory
2. 点击 timeline
3. 点击 Flutter Developer
4. 操作应用
5. 点击 Refresh 加载数据
6. w/s 放大， a/d (点?号可以看到全部的快捷键)

![img](https://www.sunmoonblog.com/images/15813940478063.jpg)

刷新后可以在事件面板中检查和分析UI线程和GPU的耗时，以定位性能瓶颈。

- 将 `debugProfileBuildsEnabled` 设置为 `true` 可以在 Observatory 中看到 构建 Widget 的耗时
- Save 后导出的 json 文件可以重新导入 Chrome 进行分析。Chrome 中输入 `chrome://tracing` 然后将 json 文件拖进去

# DevTools 之 Timeline

[时间线视图](https://flutter.dev/docs/development/tools/devtools/timeline#frame-rendering-chart)用于显示 Flutter 帧信息。它由三个部分组成。

- 帧渲染图表
- 帧事件图表
- CPU 分析器

使用 Timeline 可以对 UI 性能问题进行**定量分析**，通过观察布局阶段和渲染阶段的耗时，进一步定位问题原因。

Performance Overlay 可用于 profile 模式。

## 帧渲染图表

帧渲染图表跟 Performance Overlay 很类似。

![img](https://www.sunmoonblog.com/images/15786578717563.jpg)

图表中的每个条形框都代表一帧，每帧中不同线程的执行情况以不同颜色表示。

- UI 线程执行 Dart VM 中的 Dart 代码。构建过于复杂的图层树可能导致这个线程变慢
- GPU 线程执行 Flutter 引擎中图形相关的代码。某些图层树易于构建却难于渲染，也可能导致这个线程变慢

## 帧事件图表

在帧渲染图表中选中一帧后，帧事件图表中将显示具体事件及其耗时。

![-w1274](https://www.sunmoonblog.com/images/15786583559211.jpg)

- [debugProfileBuildsEnabled](https://api.flutter.dev/flutter/widgets/debugProfileBuildsEnabled.html) - 向 Timeline 事件中添加 build 信息
- [debugProfilePaintsEnabled](https://api.flutter.dev/flutter/rendering/debugProfilePaintsEnabled.html) - 向 timeline 事件中添加 paint 信息
- [debugPrintRebuildDirtyWidgets](https://api.flutter.dev/flutter/widgets/debugPrintRebuildDirtyWidgets.html) - 记录每帧重建的 widget

## CPU 分析器

- 采样率
  - 高采样率 - 单元时间内采集的 CPU 信息会更加详细且采集样例更多。但应用性能可能下降，采样存储空间可能溢出
  - 单元时间内采集的 CPU 信息会比较粗略且采集样例较少。对应用性能影响小，可以采集更长时间
- 图表
  - 火焰图 - 展示的是自上而下的调用堆栈信息，即上面的堆栈帧调用下面的堆栈帧。每一个堆**栈帧的宽度代表 CPU 执行的时长**。调用堆栈栈帧消耗 CPU 的时间越长，就越洽有可能是我们进行性能改进的好地方
  - 调用树 - 展示的是自上而下展示 CPU 中的调用堆栈信息
  - Bottom up 视图 - 用于显示方法调用堆栈，是一个自下而上的表示方式。表格中的每个最上方的方法实际上是给定 CPU 样本的调用堆栈中的最后一个方法

# Chrome Tracing

我们可以从 DevTools Timeline 中导出 timeline 文件。

![-w1000](https://www.sunmoonblog.com/images/15786557953993.jpg)

导出的 timeline 文件可以重新导入到 chrome tracing 进行分析。

![-w1000](https://www.sunmoonblog.com/images/15786559387247.jpg)

chrome tracing 几个常用的操作键包括：

- W - 放大
- S - 缩小
- A - 左移
- D - 右移

chrome tracing 工具展示如下两个阶段的耗时：

- VSYNC
- GPURasterizer:Draw

![-w1147](https://www.sunmoonblog.com/images/15786563521810.jpg)

(TODO 如何解读这些数据? 比 CPU 分析器更容易观察数据?)

[Timeline](https://api.flutter.dev/flutter/dart-developer/Timeline-class.html) 用于向时间线添加同步事件。使用方法很简单：

```
Timeline.startSync("Doing Something");
doSomething();
Timeline.finishSync();
```

或者：

```
Timeline.timeSync("Doing Something", () {
  doSomething();
});
```

我们在代码中添加 “Doing Something”，

![-w519](https://www.sunmoonblog.com/images/15790537579215.jpg)

然后将 DevTools Timeline 导出的 timeline 文件重新导入到 Chrome Tracing 工具。从中我们可以找到 “Doing Something” 对应的事件，如下图：

![-w900](https://www.sunmoonblog.com/images/15790539789634.jpg)

# [Widget rebuild profiler](https://flutter.cn/docs/development/tools/android-studio#show-performance-data)

Android Studio 中 View > Tool Windows > Flutter Performance 打开性能工具窗口，在 Widget rebuild stats 中勾选 Track widget rebuilds 来查看 widget 的重建信息。重建信息包括 Widget 名字、源码位置、上一帧中重建次数、当前页面中重建次数。此外，Widget 名字前面还会显示一个小图标。

![-w697](https://www.sunmoonblog.com/images/15788975395660.jpg)

- 黄色旋转圆圈 - 重建次数过多
- 灰色圆圈 - 未重建
- 灰色旋转圆圈 其他情况

这个功能的目的是让你了解 widget 是何时重建的，如果发生不符合预期的重建，就需要优化代码了。优化方法请参考 [StatefulWidget 与性能](https://api.flutter.dev/flutter/widgets/StatefulWidget-class.html#performance-considerations) (译文见[这里](https://www.sunmoonblog.com/2020/01/10/stateful-widget-performance-considerations/))

Widget rebuild profiler 只能用于 debug 模式。

# FPS

Performance Overlay 使用两张图来表示 UI线程 和 GPU线程的最近 300 帧表现。

![img](https://www.sunmoonblog.com/images/15786490815418.jpg)

不过 FPS 对我们来说更直接。Android Studio Flutter Performance 中可以看到 FPS 值：

![-w703](https://www.sunmoonblog.com/images/15788855830332.jpg)

我们也可以使用 `WidgetsBinding.addTimingsCallback` 方法来自行统计 FPS。这里提供两个参考实现：

- [统计FPS](https://gist.github.com/yrom/ac4f30b26ee02ce3bd3a1d260bb9ffb4)
- [FpsWidget](https://gist.github.com/410063005/3293c1faa78a01a3d0e7b4e2ce12d1af)

# [测试参数](https://flutter.dev/docs/testing/code-debugging)

刚入门 Flutter 开发时，你肯定会如何右上角那个刺眼的 debug 标志苦恼过。

![Debug logo -w320](https://www.sunmoonblog.com/images/15789021349152.jpg)

方法很简单。将 `debugShowCheckedModeBanner` 参数设置为 `false` 即可。

```
MaterialApp(
 debugShowCheckedModeBanner: false
)
```

Flutter 提供性能测试/调试参数，这些参数通常是布尔类型，只要在代码中打开相应的参数就可以开始进行性能测试。

- [PerformanceOverlayLayer.checkerboardOffscreenLayers](https://api.flutter-io.cn/flutter/rendering/PerformanceOverlayLayer/checkerboardOffscreenLayers.html) 用于检查是否使用 `saveLayer`，详情请参考 [Checking for offscreen layers](https://flutter.dev/docs/perf/rendering/ui-performance#checking-for-offscreen-layers)
- [PerformanceOverlayLayer.checkerboardRasterCacheImages](https://api.flutter-io.cn/flutter/rendering/PerformanceOverlayLayer/checkerboardRasterCacheImages.html) 用于检查哪些图片被缓存，请情请参考 [Checking for non-cached images](https://flutter.dev/docs/perf/rendering/ui-performance#checking-for-non-cached-images)

```
MaterialApp(
  checkerboardOffscreenLayers: true,
  checkerboardRasterCacheImages: true,
)
```

# Debug flags

## layers

Flutter framework 的每一层都提供了函数来输出当前状态或事件到控制台(这些函数调用 `debugPrint`)。

调用 [debugDumpApp()](https://api.flutter-io.cn/flutter/widgets/debugDumpApp.html) 输出 the state of the Widgets library。不应在 `build()` 方法中调用 `debugDumpApp()`，可以在调用 `runApp()` 后的任何时候调用 `debugDumpApp()`。调用 [debugFillProperties()](https://api.flutter-io.cn/flutter/widgets/Widget/debugFillProperties.html) 方法向 `debugDumpApp()` 的输出添加自定义信息。

调用 [debugDumpRenderTree()](https://api.flutter-io.cn/flutter/rendering/debugDumpRenderTree.html) 方法来输出 rendering tree，用于分析布局问题。建议在 [frame callback](https://api.flutter-io.cn/flutter/scheduler/SchedulerBinding/addPersistentFrameCallback.html) 或 event handler 中调用 `debugDumpRenderTree()` 方法。分析布局问题时，关注的关键字段是 `size` 和 `constraints`。调用 [debugFillProperties()](https://api.flutter-io.cn/flutter/rendering/Layer/debugFillProperties.html) 方法向 `debugDumpRenderTree()` 的输出添加自定义信息。

调用 [debugDumpLayerTree()](https://api.flutter-io.cn/flutter/rendering/debugDumpLayerTree.html) 方法来输出 layer tree，用于分析 compositing 问题。

> The RepaintBoundary widget, which creates a RenderRepaintBoundary in the render tree, creates a new layer in the layer tree. This is used to reduce how much needs to be repainted.

可以使用 `RepaintBoundary` 来在 render tree 中创建 `RenderRepaintBoundary` ，即，在 layer tree 中创建新的 layer。这种方法可以减少重绘工作。详细可参考 [说说Flutter中的RepaintBoundary - 掘金](https://juejin.im/post/5deda528f265da339f7ce93c)。

调用 [debugDumpSemanticsTree()](https://api.flutter.dev/flutter/rendering/debugDumpSemanticsTree.html) 方法来输出 semantics tree (这棵树表示 system accessibility APIs)。暂时用不上，略过。

调用 [debugPrintBeginFrameBanner](https://api.flutter.dev/flutter/scheduler/debugPrintBeginFrameBanner.html) 和 [debugPrintEndFrameBanner](https://api.flutter.dev/flutter/scheduler/debugPrintEndFrameBanner.html) 方法来输出一帧的开始和结束。

```
void main() {
  runApp(DemoApp());
  debugPrintBeginFrameBanner = true;
  debugPrintEndFrameBanner = true;
}
```

## layout

[debugPaintSizeEnabled](https://api.flutter.dev/flutter/rendering/debugPaintSizeEnabled.html) 用于调试布局问题。其用法如下：

```
void main() {
    debugPaintSizeEnabled = true;
    runApp(DemoApp());
}
```

- box - 所有的 box 外都显示一个亮蓝绿色边界
- padding - 以浅蓝色显示 `Padding` 等控件 (带一个深蓝色 box)
- alignment - 包括 `Center` 和 `Align` 等控件，以黄色箭头显示
- spacer - 以灰色显示，比如没有子节点的 `Container` 就属于这一类

[debugPaintBaselinesEnabled](https://api.flutter.dev/flutter/rendering/debugPaintBaselinesEnabled.html) 的作用类似，它显示的是对象的基线。

[debugPaintPointersEnabled](https://api.flutter.dev/flutter/rendering/debugPaintPointersEnabled.html) 用于开启一个特殊的模式：该模式下被点击的对象以蓝绿色显示。这个功能用于检查 hit test 是否正确。

还有几个用于调试 compositor layer 的 flag，

- [debugPaintLayerBordersEnabled](https://api.flutter.dev/flutter/rendering/debugPaintLayerBordersEnabled.html) - 以橙色显示每个 layer 的边界，用于检查是否需要使用 `RepaintBoundary` 控件
- [debugRepaintRainbowEnabled](https://api.flutter.dev/flutter/rendering/debugRepaintRainbowEnabled.html) - 每个 layer 重绘时会叠加上不同的颜色。使用技巧：如果一个静态的 widget 颜色出现变化，可以考虑给它加上 `RepaintBoundary`
- debugPrintScheduleBuildForStacks - 为什么被构建
- debugPrintRebuildDirtyWidgets - 什么组件被重新构建了
- debugProfileBuildsEnabled - 在观测台里显示构建树
- [debugPaintLayerBordersEnabled](https://api.flutter.dev/flutter/rendering/debugPaintLayerBordersEnabled.html) - 查看 layer 界线
- [debugProfileBuildsEnabled](https://api.flutter.dev/flutter/widgets/debugProfileBuildsEnabled.html) - Adds Timeline events for every Widget built.
- [debugProfilePaintsEnabled](https://api.flutter.dev/flutter/rendering/debugProfilePaintsEnabled.html)
- debugPrintRebuildDirtyWidgets
  - 结合 [debugPrintScheduleBuildForStacks](https://api.flutter.dev/flutter/widgets/debugPrintScheduleBuildForStacks.html)，可以观察 widget 的 dirty/clean 生命周期
  - 结合 [debugProfileBuildsEnabled](https://api.flutter.dev/flutter/widgets/debugProfileBuildsEnabled.html)，可以在 DevTools Timeline 中观察到详细事件信息

注意：所有这些以 debug 开头的 flag 均只能工作于 debug 模式。

## animation

调试动画的最有效方式是减慢其速度。可以使用 DevTool 中 [Inspector view](https://flutter.dev/docs/development/tools/devtools/inspector) 的 Slow Animations 按钮来减慢。可以使用 [timeDilation](https://api.flutter.dev/flutter/scheduler/timeDilation.html) 来更精确地控制动画速度。

## performance

[debugPrintMarkNeedsLayoutStacks](https://api.flutter.dev/flutter/rendering/debugPrintMarkNeedsLayoutStacks.html) - 如果 layout 次数比预期中的要多，可以通过这个 flag 来观察 render box 被置为 dirty 的原因。

# Flutter Driver

TODO

- https://flutter.cn/docs/perf/rendering/ui-performance#benchmarking

# 一些小技巧

- 检查 Performance Overlay
- 检查 FPS (自行计算 FPS)
- 检查屏幕之外的视图 (使用 `checkerboardOffscreenLayers`)
- 检查没有缓存的图像 (使用 `checkerboardRasterCacheImages`)
- 检查 widget 重建性能 (使用 Widget rebuild stats)

优化点：

- 想办法减少 widget 重建

# 参考

- [code-debugging](https://flutter.dev/docs/testing/code-debugging)
- [debugging](https://flutter.dev/docs/testing/debugging)
- [ui-performance](https://flutter.dev/docs/perf/rendering/ui-performance)
- [Performance testing of Flutter apps - Flutter - Medium](https://medium.com/flutter/performance-testing-of-flutter-apps-df7669bb7df7)