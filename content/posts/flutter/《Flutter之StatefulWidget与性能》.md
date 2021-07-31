---
title: "《Flutter之StatefulWidget与性能》"
subtitle: ""
date: 2021-07-31T21:08:33+08:00
lastmod: 2021-07-31T21:08:33+08:00
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



> 本文主要介绍Flutter StatefulWidget 与应用性能其实有些微妙的联系。

<!--more-->





Flutter 官方文档提到 widget 只是一个配置数据结构，创建是非常轻量的，加上 Flutter 团队对 widget 的创建/销毁做了优化，不用担心整个 widget 树重新创建所带来的性能问题。但实际上并非这么简单。看看这篇文章怎么说吧。

本文翻译自 [Performance considerations](https://api.flutter.dev/flutter/widgets/StatefulWidget-class.html#performance-considerations)。

有两大类 [StatefulWidget](https://api.flutter.dev/flutter/widgets/StatefulWidget-class.html)。

一类 StatefulWidget 在 [State.initState](https://api.flutter.dev/flutter/widgets/State/initState.html) 方法中分配资源，并且在 [State.dispose](https://api.flutter.dev/flutter/widgets/State/dispose.html) 方法中释放资源，但并不依赖 [InheritedWidget](https://api.flutter.dev/flutter/widgets/InheritedWidget-class.html) 或调用 [State.setState](https://api.flutter.dev/flutter/widgets/State/setState.html) 方法。这类 Widget 通常用于应用或页面的根节点，并且使用 [ChangeNotifier](https://api.flutter.dev/flutter/foundation/ChangeNotifier-class.html)、[Stream](https://api.flutter.dev/flutter/dart-async/Stream-class.html) 或其他对象来跟子节点通信。遵守这种模式的 StatefulWidget 在 CPU 和 GPU 上的开销相对较低，因为它们只被创建一次且不再更新。所以这种 StatefulWidget 可以有相对复杂和较深层级的 build 方法。

另一类 StatefulWidget 使用 [State.setState](https://api.flutter.dev/flutter/widgets/State/setState.html) 方法或依赖 [InheritedWidget](https://api.flutter.dev/flutter/widgets/InheritedWidget-class.html)。这类 widget 通常在应用的生命周期中重建很多次，因此最小化其重建过程非常重要。(它们可能也会在 [State.initState](https://api.flutter.dev/flutter/widgets/State/initState.html) 或 [State.didChangeDependencies](https://api.flutter.dev/flutter/widgets/State/didChangeDependencies.html) 方法中分配资源，但重点在于**重建**)

有若干技巧用来减小重建 StatefulWidget 对性能的影响：

- 将状态下沉到叶子节点。比如，页面上有个时钟，与其将时间状态放在上层页面中(每次时间更新时重建整个页面)，不如创建一个专用的 clock widget (每次时间更新时仅仅只需更新这个 widget)
- 最小化 build 方法创建的 widget 的数量以及相对应的 node 数量。理想状态下，一个 StatefulWidget 只用创建唯一一个 widget (类型为 [RenderObjectWidget](https://api.flutter.dev/flutter/widgets/RenderObjectWidget-class.html))。(显然这并不总是可行，但 widget 越接近这个理想状态则越高效)
- 如果一棵子树并不变化，缓存对应的 widget 并尽可能复用。复用一个 widget 比新生成一个 widget 要高效。一种常用的改进方式是，重构有状态的部分到一个接收 child 参数的 widget 中
- 尽可能使用 `const` widget (这等同于缓存和复用 widget)
- 避免改变已创建的子树的深度或修改子树中 widget 的类型。比如，与其直接返回子节点或将其包裹在 [IgnorePointer](https://api.flutter.dev/flutter/widgets/IgnorePointer-class.html)，不如总是将子节点包裹在 [IgnorePointer](https://api.flutter.dev/flutter/widgets/IgnorePointer-class.html) 并且操作 [IgnorePointer.ignoring](https://api.flutter.dev/flutter/widgets/IgnorePointer/ignoring.html) 属性值。原因是改变子树深度会导致重新 build/layout/paint 整个子树，而修改属性值只会对渲染树进行尽可能少的修改 (在 [IgnorePointer](https://api.flutter.dev/flutter/widgets/IgnorePointer-class.html) 这个例子中，根本不必重新 layout 和 paint)
- 如果出于某些原因必须要改变深度，可以考虑将子树的通用部分包裹在一个带 [GlobalKey](https://api.flutter.dev/flutter/widgets/GlobalKey-class.html) 的 widget 中 (该 GlobalKey 在 widget 生命周期中不变。如果其他 widget 不能很方便地设置 key，可考虑使用 [KeyedSubtree](https://api.flutter.dev/flutter/widgets/KeyedSubtree-class.html))

# 总结

- 减少 widget 重建 (可借助 Android Studio > Flutter Performance > Widget rebuild stats 来[统计重建次数](https://flutter.cn/docs/development/tools/android-studio#show-performance-data))
- 缓存不变的子树
- 尽可能使用 `const` widget