---
title: "《Flutter之RenderObject介绍》"
subtitle: ""
date: 2021-07-31T21:02:55+08:00
lastmod: 2021-07-31T21:02:55+08:00
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



> 本文主要介绍什么是 RenderObject？

<!--more-->





原文见 [Flutter, what are Widgets, RenderObjects and Elements?](https://medium.com/flutter-community/flutter-what-are-widgets-renderobjects-and-elements-630a57d05208)

每个人都想知道 Flutter 是如何将 Widget 转换成屏幕上的像素的，对不对？

理解底层技术原理是优秀开发者跟伟大开发者的差别。

当你知道什么是可行，什么是不可行的话，就可以更轻松地创建自定义布局和特效，这能节省你很多的时间。

本文的目标是介绍 Flutter 背后的原理。我们从另一个不同的方面看 Flutter 并理解它是如何工作的。

# 开始

你很可能已经知道如何使用 StatelessWidget 和 StatefulWidget。但它们只能组合已有的 Widget。对 Widget 进行布局和绘制实际上发生在别的地方。

强烈建议你打开最喜欢的 IDE 并一步步按下面的步骤来操作，实际代码常常让你恍然大悟。Intellij 中双击 Shift 可以输出名称来打开任何类。

# Opacity

我们通过观察 [Opacity](https://github.com/flutter/flutter/blob/f38743593d00f13b21f49d8d8ee2d2206bf820d3/packages/flutter/lib/src/widgets/basic.dart#L150) Widget 来熟悉 Flutter 的基本概念。之所以选择 Opacity，因为它是一个非常基本的 Widget (特别简单)，可作为一个好例子来学习。

Opacity 只接受一个子 Widget。你可以用 Opacity 来包装任何 Widget 并调整其显示方式。除了 `child` 参数，只有另外一个 `opacity` 参数，它是浮点类型，值介于 0.0 和 1.0。这个参数用于控制不透明度。

# 控件

`Opacity` 是一个 `SingleChildRenderObjectWidget`。它的继承结构如下：

Opacity → SingleChildRenderObjectWidget → RenderObjectWidget → Widget

StatelessWidget 和 StatefulWidget 继承结构则是这样的：

StatelessWidget/StatefulWidget → Widget

**StatelessWidget/StatefulWidget 与 Opacity 的不同在于：前者只能组合其他 Widget，而后者实际上改变了 Widget 的绘制方式**。

但是你在这几个类当中找不到跟实际绘制不透明度相关的任何代码。原因在于 Widget 只持有配置信息。在本例中，Opacity 持有的是 `opacity` 值。

> 每次都可以在 `build()` 函数中创建新的 Widget 的原因是创建的成本并不高，因为它们不过是配置信息的容器

# 渲染

渲染到底发生在哪里？实际上它们发生在 RenderObject 中。

你可以从名字猜到 RenderObject 负责的工作包括渲染。

Opacity Widget 使用如下方法创建并更新 [RenderObject](https://github.com/flutter/flutter/blob/master/packages/flutter/lib/src/widgets/basic.dart#L188)：

```
@override
RenderOpacity createRenderObject(BuildContext context) => new RenderOpacity(opacity: opacity);

@override
void updateRenderObject(BuildContext context, RenderOpacity renderObject) {
  renderObject.opacity = opacity;
}
```

# RenderOpacity

`Opacity` Widget 大小跟其 child 完全一样。基本上它每个方面跟其 child 都一样，除了绘制。它会在绘制 child 前加上不透明度。

本例中，RenderOpacity 需要实际所有方法(比如布局、点击测试、大小计算等等)并要求 child 完成实际工作。

而 `RenderOpacity` 继承自 `RenderProxyBox` (它混入了其他类)。这些类用于实际具体的方法，并将真正的计算工作推迟到 child 中进行。

```
double get opacity => _opacity;
double _opacity;
set opacity(double value) {
  _opacity = value;
  markNeedsPaint();
}
```

(注：这里的代码去掉了优化和断言，只保留最关键部分。完整代码见[这里](https://github.com/flutter/flutter/blob/master/packages/flutter/lib/src/rendering/proxy_box.dart#L736))

字段通常为私有变量暴露一个 getter。而 setter 用于设置字段，此外它还调用了 `markNeedsPaint()` 和 `markNeedsLayout()` 方法。正如名字所言，这个方法告诉系统 “我有变化啦，请重新绘制/重新布局”。

在 `RenderOpacity` 中可以找到这个方法：

```
@override
void paint(PaintingContext context, Offset offset) {
    context.pushOpacity(offset, _alpha, super.paint);
}
```

(注：这里同样只保留最关键代码，去掉了优化和断言，完整代码见[这里](https://github.com/flutter/flutter/blob/master/packages/flutter/lib/src/rendering/proxy_box.dart#L757))

`PaintingContext` 基本上是一个高级的 canvas。这个 canvas 上有一个 `pushOpacity()` 方法。而这行代码就是不透明度的实现。

总结一下：

- `Opacity` 并非 `StatelessWidget` 或 `StatefulWidget`，而是一个 `SingleChildRenderObjectWidget`
- `Widget` 仅持有渲染器会用到的配置信息
- `Opacity` 持有一个 double 值用于表示不透明度
- 继承自 `RenderProxyBox` 的 `RenderOpacity` 完成实际布局/渲染工作
- `Opacity` 几乎跟 child 行为完全一致，所以它会将其方法代理到 child
- `RenderOpacity` 覆盖了 `paint` 方法。在这个方法中调用 `pushOpacity()` 来为 Widget 添加不透明度

# Element

记住，Widget 只是配置信息，`RenderObject` 只管理布局/渲染等工作。

在 Flutter 中你基本上不停地重新创建 Widget。`build()` 被调用时会创建一堆的 Widget。而每次发生变化时 `build()` 方法被调用。比如，有动画效果时 `build()` 方法不停地被调用。这意味着你并不能每次都重建子树。实际上，你想做的是更新它。

你无法获取 Widget 屏幕上的大小和位置，因为 Widget 只是一张蓝图，而不代表屏幕上的实际内容。它仅仅是对底层 RenderObject 会用到的变量的描述。

Element 是实际的树中的具体的 Widget。

具体过程是这样的：首次创建 Widget 时，由它生成 `Element`。这个 Element 被插入到树中，后续如果 Wiget 发生变化，它会跟旧的 Widget 比较，并根据差异对 Element 进行相应地更新。最重要的一点是，Element 并未重新创建，而是更新！

Element 是核心框架的中心，我们应该对它进行更多讨论，不过现在了解这些就足够了。

问题：在上面 Opacity 这个例子中 element 是在哪里创建的？

答案是 `SingleChildRenderObjectWidget` 创建了 Element。代码如下：

```
@override
SingleChildRenderObjectElement createElement() => new SingleChildRenderObjectElement(this);
```

具体见[源码](https://github.com/flutter/flutter/blob/master/packages/flutter/lib/src/widgets/framework.dart#L1631)。另外，`SingleChildRenderObjectElement` 只有一个 child。

问题：是Element 创建了 RenderObject 吗，但我们这个例子中看起来 Opacity Widget 创建了它自己的 RenderObject？

```
class Opacity extends SingleChildRenderObjectWidget {
  @override
  RenderOpacity createRenderObject(BuildContext context) {
    return RenderOpacity(
      opacity: opacity,
      alwaysIncludeSemantics: alwaysIncludeSemantics,
    );
  }
}
```

这个问题说的应该是指 `Opacity.createRenderObject()` 方法创建了 `RenderObject`。

答案是这么做纯粹是为了更好用的 API。因为 Widget 通常需要一个 RenderObject 而不是自定义的 Element。实际上 RenderObject 是由 Element 创建的。

```
SingleChildRenderObjectElement(SingleChildRenderObjectWidget widget) : super(widget);
```

见[源码](https://github.com/flutter/flutter/blob/master/packages/flutter/lib/src/widgets/framework.dart#L4643)。`SingleChildRenderObjectElement` 持有 `RenderObjectWidget` 的引用，后者拥有创建 `RenderObject` 的方法。

`mount()` 方法是 Element 被添加到 Element Tree 的地方。

```
@override
void mount(Element parent, dynamic newSlot) {
  super.mount(parent, newSlot);
  _renderObject = widget.createRenderObject(this);
  attachRenderObject(newSlot);
  _dirty = false;
}
```

一旦 `mount()` 方法被调用，Element 要求 Widget “请给我你想使用的 RenderObject，好让我保存下来”。

# 结束

本文介绍了 Opacity Widget 内部工作机制。我的目标是介绍 Widget 背后的原理，仍然还有许多话题可以讨论，希望我能为你了解内部工作原理做一个好的指引。

# 思考

Widget 跟 View 类比是不准确的。

- View 并不会频繁重建，Widget 会频繁重建
- View 有实际的大小和位置，Widget 没有具体的大小的位置