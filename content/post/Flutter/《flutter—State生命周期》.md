---
title: "《Flutter—State生命周期》"
subtitle: ""
date: 2021-07-31T18:19:45+08:00
lastmod: 2021-07-31T18:19:45+08:00
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



> 本文主要介绍类比 Android 和 iOS，了解 Flutter State 的生命周期。

<!--more-->





从 Android 或 iOS 转到 Flutter 开发，最让人疑惑的是 Flutter 如何处理生命周期。

**onCreate()** 在哪里？**viewDidLoad()** 呢？我应该将业务逻辑放在哪里？为什么只有一个 `build` 方法？

本文将解答这些疑惑。

# Android

**Activity** 生命周期是 Android 开发必须记住的：

- onCreate
- onStart
- onResume

------

- onPause
- onStop
- onRestart
- onDestroy

大部分业务逻辑放在 **onCreate** 方法：初始化 view、数据库、监听器等等。**onResume** 和 **onPause** 是判断用户离开或进入当前页面的好时机。

# iOS

UIViewController 的生命周期如下：

- viewDidLoad
- viewWillAppear
- viewDidAppear

------

- viewWillDisappear
- viewDidDisappear
- viewDidUnload

如你所见，两个平台均使用几乎一致的步骤来创建或销毁页面。大部分业务逻辑放在 **viewDidLoad()**，而 **Will/Did Appear/Disappear** 用于保存信息，判断用户何时离开页面等等。

# Flutter

在 Flutter 中呢？Widget 也遵循类似规则吗？

Flutter 中主要有两种 Widget：**StatelessWidget** 和 **StatefulWidget**。本文重点放在 **StatefulWidget**，因为它有点像 **Android** 和 **iOS** 中的某些概念。

## StatefulWidget

StatefulWidget 是最重要的 Widget，因为它持有 **State**，它知道何时发生变化并进行必要的重绘。它的生命周期如下：

- createState
- initState
- didChangeDependencies
- build
- (didUpdateWidget)

— — — — — — — — — — —

- deactivate
- dispose

你可能会注意到创建阶段的 state 比销毁阶段的多，这是因为创建/重建 Widget 及其 State 更复杂。

### createState():

构建新的 StatefulWidget 时将调用 createState()，StatefulWidget 的子类必须覆盖这个方法：

```
class MyScreen extends StatefulWidget {
  @override
  _MyScreenState createState() => _MyScreenState();
}
```

### initState()

通常需要重写这个方法。它是 Widget 创建后调用的第一个方法，可以理解成 **onCreate()** 或 **viewDidLoad()** 的等价物。在这个方法中可以检查 Widget 某些相关属性：是否有被渲染？当前是否处理 mounted 状态？

### mounted

每个 Widget 都有这个属性。当为 Widget.buildContext 赋值后 mounted 的值变成 **true**，Widget 在树中。直到 **dispose** 方法被调用前，mounted 一直为 true。

### addPostFrameCallback

可以在 **initState** 方法中按如下方式调用 `addPostFrameCallback`：

```
import 'package:flutter/scheduler.dart';
@override
void initState() {
  super.initState();
  SchedulerBinding.instance.addPostFrameCallback((_) => {});
}
```

这个方法为一帧结束时添加回调，回调方法只会调用一次，通知 Widget 构建已完成。

### didChangeDependencies

widget 构建时第一次调用 initState() 后立即会调用 didChangeDependencies()。如果 **StatefulWidgets** 依赖某个 **InheritedWidget**，后者发生变化时会导致 didChangeDependencies() 再次被调用。

### build()

可以肯定地说这是最重要的方法。这个方法依赖整个待渲染的 Widget 树，在 **didChangeDependencies()** 立即被调用。所有的 GUI 渲染发生在这个方法，每次 UI 需要重新渲染时都会调用这个方法。(重新渲染是很廉价的操作)

### didUpdateWidget()

这是一个不太常见的生命周期方法。一旦 parent widget 有变化需要重绘 UI 时会调用 didUpdateWidget()。这个方法有一个 **oldWidget** 参数，可以将它跟当前 **widget** 进行比较以执行某些额外的业务逻辑。

### deactivate()

这个生命周期方法也不太常见。这个方法被调用时，意味着 Widget 开始进入”死亡”阶段。

当 framework 从树中移除 State 时会调用本方法。某些情况下，framework 会重新将 State 对象加入到树中。

### dispose()

这个方法也非常重要。当 Widget 及其 State 从树中永久移除时会调用这个方法。此后 Widget 不会再被构建。

你需要在这个方法中做取消对 stream 的监听、销毁动画等操作。它是 **initState** 的反操作。

# WidgetsBindingObserver

如果我们想监听应用何时进入后台该怎么办？

可以这样做。对 **StatefulWidget** 做些小改动：

```
class _MyScreenState extends State<MyScreen> with WidgetsBindingObserver
```

我们让 `_MyScreenState` 实现了抽象类 **WidgetsBindingObserver**，之后就可以观察应用的生命周期状态。在 **initState** 方法中开始观察：

```
WidgetsBinding.instance.addObserver(this);
```

在 **dispose** 方法中停止观察：

```
WidgetsBinding.instance.removeObserver(this);
```

接下来在 **didChangeAppLifecycleState** 接收生命周期的变化即可：

```
@override
void didChangeAppLifecycleState(AppLifecycleState state) {
  super.didChangeAppLifecycleState(state);
  if (state == AppLifecycleState.paused) {
    // went to Background
  }
  if (state == AppLifecycleState.resumed) {
    // came back to Foreground
  }
}
```

这个技巧很有用。如果你想在应用进入后台时弹出 notification，或者保存数据，又或者在用户点 back 回退时弹出 popup，都可以使用这个技巧。