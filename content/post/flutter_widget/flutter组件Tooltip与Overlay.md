---
title: "Flutter组件Tooltip与Overlay"
date: 2021-08-31T09:51:35+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter","dart"]
categories: ["Flutter","dart"]
---

 `Tooltip` 组件主要的作用是在`鼠标悬浮`或`长按手势`下触发消息提示。它继承自 `StatefulWidget` ，其中必须传入 `String` 类型的 `message` ，还有很多其他的参数用于配置。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/910218aad31b4e989d398a9e2d2439dc~tplv-k3u1fbpfcp-watermark.awebp)

```
final String message;
复制代码
```

------

如下是 `Tooltip` 默认的效果，可以套在任意组件上，当`鼠标悬浮`或`长按手势`时，会在下方显示提示信息。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d55fe024c29f44f1afe192be639358ec~tplv-k3u1fbpfcp-watermark.awebp)

```dart
Tooltip(
  message: "宝塔镇河妖",
  child: Icon(Icons.info_outline)
);
复制代码
```

------

当 `preferBelow` 属性为 `false` ，提示信息就会显示在上方。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0024d39b3a0c4be2ac3167ee2be4df76~tplv-k3u1fbpfcp-watermark.awebp)

```dart
Tooltip(
  preferBelow: false,
  message: "宝塔镇河妖",
  child: Icon(Icons.info_outline)
);
复制代码
```

------

通过 `verticalOffset` 可以设置竖直偏移，此偏移量可为负数。此值为 `0` 时，提示框底部与组件中心对齐

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/879f2e7835224ecea24ce74b42258fa9~tplv-k3u1fbpfcp-watermark.awebp)

```dart
Tooltip(
  preferBelow: false,
  verticalOffset: 12,
  message: "宝塔镇河妖",
  child: Icon(Icons.info_outline)
);
复制代码
```

------

通过 `padding` 和 `margin` 可以设置内外边距：

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5b0ad6dd2d304e52b0d0d9b2875e5204~tplv-k3u1fbpfcp-watermark.awebp)

```dart
Tooltip(
  preferBelow: false,
  padding: EdgeInsets.symmetric(horizontal: 40,vertical: 5),
  margin: EdgeInsets.all(10),
  message: "宝塔镇河妖",
  child: Icon(Icons.info_outline)
);
复制代码
```

------

通过 `decoration` 和 `textStyle` 可以设置 `盒子装饰` 和 `文字样式`。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8f11fd2d6e8c436f9e96c3322b9c3b8d~tplv-k3u1fbpfcp-watermark.awebp)

```dart
Tooltip(
  preferBelow: false,
  verticalOffset: 15,
  message: "宝塔镇河妖",
  textStyle: TextStyle(
    color: Colors.red,
    shadows: [
      Shadow(
        color: Colors.white,
        offset: Offset(1, 1),
      ),
    ],
  ),
  decoration: BoxDecoration(boxShadow: [
    BoxShadow(
      color: Colors.orangeAccent,
      offset: Offset(1, 1),
      blurRadius: 8,
    )
  ]),
  child: Icon(Icons.info_outline)
);
复制代码
```

------

有时候我们并不希望鼠标一进入就显示提示，`waitDuration` 表示鼠标进入时，需要等待多长时间再显示提示框。`showDuration` 表示长按时，需要等待多长时间再显示提示框。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/77ba50e94f3a429aa2827bf33e07d784~tplv-k3u1fbpfcp-watermark.awebp)

```dart
Tooltip(
  // 略同...
  waitDuration:const Duration(seconds: 2),
  showDuration:const Duration(seconds: 2),
  child: Icon(Icons.info_outline)
);
复制代码
```

`Tooltip` 组件的属性就是这些，下面我们来看一下它的源码实现。

------

#### 2. Tooltip 源码简看

`Tooltip` 作为一个 `StatefulWidget`，自然是会维护一个状态类进行组件构建，状态周期等逻辑处理。如下是 `_TooltipState` 的类定义。看到它混入了 `SingleTickerProviderStateMixin`，表示该状态类中会使用动画。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ff54ae51c24f48038ca58e63581f892f~tplv-k3u1fbpfcp-watermark.awebp)

在 `initState` 回调中，会初始化 `_controller` 动画控制器，可以看出 `Tooltip` 的提示框会伴随一个透明度的渐变动画。然后对鼠标 `mouseTracker` 和触点 `pointerRouter` 进行监听。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0fb384c2663d4c138ea91e4187252a79~tplv-k3u1fbpfcp-watermark.awebp)

在 `dispose` 回调中移除监听和销毁动画控制器。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1c6527d9f52149bba677173ef45a3656~tplv-k3u1fbpfcp-watermark.awebp)

在 `build` 方法中可以看出提示框的默认表现会受 `Theme`、`TooltipTheme` 的数据影响，对暗黑主体也进行了适配。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/18cbd2bd332f4e19b7d8f991e5df1c33~tplv-k3u1fbpfcp-watermark.awebp)

会通过 `GestureDetector` 来监听长按事件，如果检测到鼠标的连接，外层会套上 `MouseRegion` 进行监听鼠标的移入移出事件。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3e17399c92cc40c3bbf37d753f2d01c2~tplv-k3u1fbpfcp-watermark.awebp)

最终显示的是用户传入的 `child` 组件，那提示框是如何弹出和消失的呢？现在焦点就可以放在 `_showTooltip` 和 `_hideTooltip` 如何控制提示框的显隐。

------

#### 3.Overlay 在 Tooltip 源码的应用

在移动端中，长按会弹出提示框，从源码中可以看出，核心的方法是 `ensureTooltipVisible`。

```dart
void _handleLongPress() {
  _longPressActivated = true;
  final bool tooltipCreated = ensureTooltipVisible();
  if (tooltipCreated)
    Feedback.forLongPress(context);
}
复制代码
```

------

`_TooltipState` 中维护了两个计时器 `_hideTimer` 和 `_showTimer` 来处理延迟。开始会取消并置空 `_showTimer` 计时器，这样保证不会在计时器完成时再出现一个框。如果 `_entry` 非空，表示提示框已经存在，会取消并置空 `_hideTimer` 计时器，并执行动画。此处返回 `false` ，表示已经存在，开启失败。
 否则会执行 `_createNewEntry` 创建新的 `Entry` 并执行动画。

```dart
Timer? _hideTimer;
Timer? _showTimer;
OverlayEntry? _entry;

bool ensureTooltipVisible() {
  _showTimer?.cancel();
  _showTimer = null;
  if (_entry != null) {
    // Stop trying to hide, if we were.
    _hideTimer?.cancel();
    _hideTimer = null;
    _controller.forward();
    return false; // Already visible.
  }
  _createNewEntry();
  _controller.forward();
  return true;
}
复制代码
```

------

在 `_createNewEntry` 中，先通过 `Overlay.of` 获取 `OverlayState` 对象，再通过  `context.findRenderObject` 获取 `RenderBox` 得到组件的位置。最后创建 `OverlayEntry` 为 `_entry` 赋值，并将`_entry`通过 `overlayState`  插入，其中主体的内容就是 `overlay` 组件。

```dart
void _createNewEntry() {
  final OverlayState overlayState = Overlay.of( //1. 得到 overlayState
    context,
    debugRequiredFor: widget,
  )!;
  final RenderBox box = context.findRenderObject()! as RenderBox;
  final Offset target = box.localToGlobal(
    box.size.center(Offset.zero),
    ancestor: overlayState.context.findRenderObject(),
  );

  final Widget overlay = Directionality( 
    textDirection: Directionality.of(context),
    child: _TooltipOverlay(//...组件暂略
  );
   //2. 创建 _entry
  _entry = OverlayEntry(builder: (BuildContext context) => overlay);
   //3. 插入 _entry
  overlayState.insert(_entry!);
  SemanticsService.tooltip(widget.message);
}
复制代码
```

------

我们为 `Tooltip` 传入的大多数参数都是用于构建 `_TooltipOverlay`  的，下面是它的源码。可以看出，它通过 `FadeTransition` 进行透明渐变动画，通过 `CustomSingleChildLayout` 进行提示框的定位。并且 `IgnorePointer` 表示提示框是忽略点击事件的。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c9b8c31da112451f98e9da8ff8d4357f~tplv-k3u1fbpfcp-watermark.awebp)

这样 `Overlay` 的弹出就看完了，至于 `Overlay` 的移除，只需要 `_entry?.remove();` 即可。

```dart
void _removeEntry() {
  _hideTimer?.cancel();
  _hideTimer = null;
  _showTimer?.cancel();
  _showTimer = null;
  _entry?.remove();
  _entry = null;
}
复制代码
```

`Overlay` 组件本身的使用并不复杂，这是 `Tooltip` 中有延迟和动画的处理，让显隐的逻辑复杂了一些。这些在计时器的控制人常开发中也是值得我们学习的。虽然是很小的一个组件，但其中包含了很多知识，这种小巧的组件很适合我们去细细品读。


