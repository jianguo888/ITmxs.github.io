---
title: "《Flutter—APP内存测试》"
subtitle: ""
date: 2021-06-15T18:24:54+08:00
lastmod: 2021-06-15T18:24:54+08:00
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



> 本文主要介绍flutter 应用内存测试数据。

<!--more-->





# 内存测试一 - 简单 UI

分别**使用 Android 和 Flutter 实现最简单的界面**。

- 显示一个文本
- 显示一个图片

使用 TextView 显示 “Hello World!” 的 Android Demo，布局如下：

```
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Hello World!"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

使用 Text Widget 显示 “Hello World!” 的 Flutter Demo，代码如下：

```
Center(child: Text('Hello World!')),
```

显示图片的 Demo 非常类似，这里略过。

测试数据显示：**Flutter 应用比原生应用占用更多内存，多出来的部分主要包括 Other、Code、Native、Graphics**。(注意：Android Studio 中无法正常显示 Other 部分变化，`adb shell dumpsys meminfo` 及 Flutter DevTools 中可正常观察到)

以下是具体测试数据。

## 数据一

机型及配置：华为 Nova 2，Android 8.0，分辨率480dp(3x)。

Android Text Demo
![img](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2020/07/15/15944369530168.jpg)
![img](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2020/07/15/15944406815956.jpg)
![img](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2020/07/15/15944408053563.jpg)

Android Image Demo
![img](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2020/07/15/15944370128933.jpg)
![img](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2020/07/15/15944407225565.jpg)
![img](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2020/07/15/15944408363481.jpg)

Flutter Text Demo
![img](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2020/07/15/15944384753825.jpg)
![img](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2020/07/15/15944408943099.jpg)
![img](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2020/07/15/15944440141932.jpg)

Flutter Image Demo
![img](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2020/07/15/15944385231943.jpg)
![img](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2020/07/15/15944409251967.jpg)
![img](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2020/07/15/15944440514423.jpg)

## 数据二

机型及配置：小米 8，分辨率440dp(2.8x)

Android Text Demo
![img](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2020/07/15/15944403017361.jpg)
![img](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2020/07/15/15944441459158.jpg)
![img](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2020/07/15/15944443093212.jpg)

Android Image Demo
![img](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2020/07/15/15944404063442.jpg)
![img](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2020/07/15/15944441807084.jpg)
![img](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2020/07/15/15944443368308.jpg)

Flutter Text Demo
![img](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2020/07/15/15944399044259.jpg)
![img](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2020/07/15/15944442195175.jpg)
![img](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2020/07/15/15944443788400.jpg)

Flutter Image Demo
![img](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2020/07/15/15944399984819.jpg)
![img](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2020/07/15/15944442627978.jpg)
![img](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2020/07/15/15944444195039.jpg)

# 内存测试二 - 分配大对象

持续在 Dart 代码中分配对象。观察内存变化。

测试数据显示：**从 Android 角度看，Dart Heap 中分配的对象归类为 “Private Other” 内存**

```
List<Uint8List> _memList = List();

void _addToMemList() {
    // 点击一次, 分配 10MB 内存
    _memList.add(_createData(10 * 1024 * 1024));
}
```

![-w1428](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2020/07/15/15945451536123.jpg)

随着分配的 Dart 对象，Dart 内存和 Android 内存都在增加

- 左图是 Dart 内存
  - Dart Heap Capacity (绿线区域)一直在增长
  - Dart Heap Used (浅蓝色区域)一直在增长
  - Dart External (蓝色区域)几乎不变
- 右图是 Android 内存
  - Total (绿线)一直在增长
  - Other (紫线)一直在增长
  - 其他几乎不变

# 内存测试三 - 显示图片

持续在 Dart 代码中**加载并显示**本地图片(1024x1024)。观察内存变化。

测试数据显示：**Flutter 应用中图片在 Graphics 内存中分配**

![-w1666](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2020/07/15/15947280277523.jpg)

随着加载和显示的图片增多，Dart 内存和 Android 内存都在增加

- 左图是 Dart 内存
  - Dart Heap Capacity (绿线区域)一直在增长
  - Dart Heap Used (浅蓝色区域)几乎不变
  - Dart External (蓝色区域)一直在增长
- 右图是 Android 内存
  - Total (绿线)一直在增长
  - Graphics (橙色区域)波浪形增长
  - 其他几乎不变

# 内存测试四 - 预加载图片

持续在 Dart 代码中**预加载但不显示**本地图片(1024x1024)。观察内存变化。

测试数据显示：**Flutter 应用中图片在 Graphics 内存中分配**

![-w1294](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2020/07/15/15947294740934.jpg)

随着预加载的图片增多，Dart 内存和 Android 内存都在增加

- 左图是 Dart 内存
  - Dart Heap Capacity (绿线区域)一直在增长
  - Dart Heap Used (浅蓝色区域)几乎不变
  - Dart External (蓝色区域)一直在增长
- 右图是 Android 内存
  - Total (绿线)一直在增长
  - Graphics (橙色区域)波浪形增长
  - 其他几乎不变

# 内存测试五 - 启动多个 Flutter 引擎

持续启动 Flutter 引擎，每次增加一个。观察内存变化。

测试数据显示：**每开启一个 Flutter 引擎，Native 内存大约有5-6MB增长，Private Other 有10MB左右增长**

![-w1302](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2020/07/15/15947866240369.jpg)

随着 Flutter 引擎数量增多，Dart 内存和 Android 内存都在增加

- 左图是 Dart 内存
  - Dart Heap Used (浅蓝色区域)几乎不变
  - Dart External (蓝色区域)一直在增长
- 右图是 Android 内存
  - Total (绿线)一直在增长
  - Other (紫色区域)一直在快速增长
  - Native (蓝色区域)一直在较快增长
  - Graphics (橙色区域)无增长

在 Android Studio 中测试结果如下：

![img](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2020/07/15/15947848408890.jpg)

随着 Flutter 引擎数量增多，

- Private Other 一直在增长 (注：注意：Android Studio 中无法正常显示 Other 部分变化，`adb shell dumpsys meminfo` 及 Flutter DevTools 中可正常观察到)
- Native 内存(蓝色区域)一直在增长

# 测试结论

- Flutter 应用比原生应用占用更多内存，多出来的部分主要包括 Other、Code、Native、Graphics [内存分类](https://developer.android.com/studio/profile/memory-profiler#how-counted) [meminfo](https://developer.android.com/studio/command-line/dumpsys#meminfo)
- 从 Android 角度看，Dart Heap 属性 “Private Other” 内存。Dart 中创建对象会导致 Private Other 内存增长
- Flutter 应用中图片在 Graphics 内存中分配 [闲鱼技术 Android Flutter实践内存初探](https://www.yuque.com/xytech/flutter/avmyht)
- 每开启一个 Flutter 引擎，Native 内存大约有5-6MB增长，Private Other 有10MB左右增长

为什么 Flutter 应用会比原生应用占用更多内存？官网给出的一些数据也许能解释部分原因。以下翻译自 [Load sequence, performance, and memory - Flutter](https://flutter.dev/docs/development/add-to-app/performance#memory-and-latency)

> 展示 Flutter UI 会有一定不可忽视的延迟。可以提前启动 Flutter 引擎来减少这个延迟。
>
> 集成 Flutter 到已有应用时要评估预加载 Flutter 引擎(即，加载 Flutter 库、启动 Dart VM、运行 isolate 入口程序)带来的内存开销及延迟。
>
> 在 2015 年的一款低端手机上，Flutter v1.10.3 release-AOT 模式下预热 `FlutterEngine` 开销如下：
>
> - **Android** 42 MB 和 1530 ms。其中有 330 ms 会阻塞主线程
> - **iOS** 22 MB 和 860 ms。其中有 260 ms 会阻塞主线程
>
> 内存方面，一个测试样本数据如下(根据使用场景会有所变化)
>
> - ~4 MB OS’s memory usage for creating pthreads.
> - ~10 MB GPU driver memory.
> - ~1 MB for Dart runtime-managed memory.
> - ~5 MB for Dart-loaded font maps.
>
> 延迟方面，一个测试样本数据如下(根据使用场景会有所变化)
>
> - ~20 ms to collect the Flutter assets from the application package.
> - ~15 ms to dlopen the Flutter engine library.
> - ~200 ms to create the Dart VM and load the AOT snapshot.
> - ~200 ms to load Flutter-dependent fonts and assets.
> - ~400 ms to run the entrypoint, create the first widget tree, and compile the needed GPU shader programs.
>
> 预热 Flutter 引擎的时机应该足够晚，以减小内存占用；同时还要足够早，以避免 Flutter 引擎启动时间跟首帧延迟叠加在一起(否则会感觉启动慢，体验非常糟糕)
>
> 具体预热时机要根据应用的实际结构来决定。通常经验是在将显示 Flutter 页面的前一个页面中预热
>
> 假设引擎已经预热，关联 UI 时的首帧开销如下：
>
> - **Android** 320 ms 以及额外的 12 MB (跟屏幕的物理像素尺寸非常相关，physical pixel size)
> - **iOS** 200 ms 以及额外的 16 MB (跟屏幕的物理像素尺寸非常相关，physical pixel size)
>
> 内存方面，开销主要来自用于渲染的 graphical memory buffer (跟屏幕尺寸相关)
>
> 延迟方面，开销主要是等原生系统回调(提供 surface)以及编译 shader 程序(原文: compiling the remaining shader programs that are not pre-emptively predictable)的时间。这里的延迟仅在第一帧
>
> Flutter UI 释放后，相关内存也会释放。这里的内存释放跟 `FlutterEngine` 中的 Flutter State 无关，除非 `FlutterEngine` 也释放了

# 优化方向

![img](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2020/07/15/15947301347638.jpg)

Flutter 应用内存可优化的点分别是：

- Private Others，Dart 代码分配的对象属于这一类。优化点是减少 Dart 代码中不必要的对象分配，尤其是大对象
- Code - 用于处理代码和资源，如 dex 字节码，so 库和字体。优化点是减少 `libflutter.so` 和 `libapp.so` 大小
- Graphics - 图形缓冲区队列向屏幕显示像素(包括 GL 表面、GL 纹理等等)所使用的内存。比较好入手的一个优化是减少 Dart 代码中图片占用的内存。另外可以考虑优化 Flutter UI 这一块(同样使用 skia，为什么 Android 原生 UI 占用的内存比 Flutter UI 少？)
- Native - C 或 C++ 代码分配的对象的内存。优化点是减少 Flutter 引擎占用的内存，比如单引擎比多引擎使用更少的内存

![-w1261](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2020/07/15/15947874114688.jpg)