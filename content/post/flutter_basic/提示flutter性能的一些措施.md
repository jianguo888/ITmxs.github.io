---
title: "提示flutter性能的一些措施"
date: 2021-09-08T23:14:51+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---

> 本文主要介绍

<!--more-->

# 1. Use Widgets Over Functions



![image-20210908232123118](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210908232123118.png)

## 简短说明

您可以节省 CPU 周期并与 const 构造函数一起使用以仅在需要时进行重建以及更多好处（重用等...）

## 长说明

# 如何提高 Flutter 应用程序的性能

[How to improve the performance of your Flutter app | Codemagic BlogWritten by Diego Velásquez There are many doubts and questions related to how we can improve the performance of our…blog.codemagic.io](https://blog.codemagic.io/how-to-improve-the-performance-of-your-flutter-app./#dont-split-your-widgets-into-methods)

[What is the difference between functions and classes to create reusable widgets?Lets I will explain it from very scratch.🙂 (only about imperative) Programming history ,we all know started with…stackoverflow.com](https://stackoverflow.com/questions/53234825/what-is-the-difference-between-functions-and-classes-to-create-reusable-widgets)

[Splitting widgets to methods is an antipatternAlmost 6 months ago, I advised you to split long build methods into multiple smaller methods. And it turns out I was…iiro.dev](https://iiro.dev/splitting-widgets-to-methods-performance-antipattern/)

# 2. **尽可能使用 const**

```
x = Container();
y = Container();
x == y // falsex = const Container();
y = const Container();
x == y // true
```

## 解释

如果已经定义，您可以使用相同的小部件保存 RAM

# **3. 尽可能使用 const 构造函数**

```
class CustomWidget extends StatelessWidget {
  const CustomWidget();
  @override
  Widget build(BuildContext context) {
    ...
  }
}
```

## 简短说明

构建自己的小部件或使用 Flutter 小部件时。这有助于 Flutter 仅重建应该更新的小部件。

# 4. 使用 nil 代替 const Container()

```
// good
text != null ? Text(text) : const Container()// Better
text != null ? Text(text) : const SizedBox()// BEST
text != null ? Text(text) : nil
or
if (text != null) Text(text)
```

## 简短说明

它只是一个基本的 Element Widget，几乎不需要任何费用。

## Long Explanation

[nil | Flutter PackageA simple widget to add in the widget tree when you want to show nothing, with minimal impact on performance. Sometimes…pub.dev](https://pub.dev/packages/nil)

# 5. 尽量使用状态管理工具

## 简短说明

状态管理工具可帮助您进行粒度更新，而不是重建所有小部件。有状态的小部件有时真的很伤人

## Long Explanation

[How to improve the performance of your Flutter app | Codemagic BlogWritten by Diego Velásquez There are many doubts and questions related to how we can improve the performance of our…blog.codemagic.io](https://blog.codemagic.io/how-to-improve-the-performance-of-your-flutter-app./#avoid-rebuilding-all-the-widgets-repetitively)

# 6.`itemExtent`在ListView中使用长列表

## 简短说明

这有助于计算的ListView滚动位置，而不是更多的计算每一个部件，使滚动动画的高度的高性能

## Long Explanation

[How to improve the performance of your Flutter app | Codemagic BlogWritten by Diego Velásquez There are many doubts and questions related to how we can improve the performance of our…blog.codemagic.io](https://blog.codemagic.io/how-to-improve-the-performance-of-your-flutter-app./#use-itemextent-in-listview-for-long-lists)

# 7.  避免将 AnimationController 与 setState 一起使用

```
void initState() {
  _controller = AnimationController(
    vsync: this,
    duration: Duration(seconds: 1),
  )
  ..addListener(() => setState(() {}));
}Column(
  children: [
    Placeholder(), // rebuilds
    Placeholder(), // rebuilds
    Placeholder(), // rebuilds
    Transform.translate( // rebuilds
      offset: Offset(100 * _controller.value, 0),
      child: Placeholder(),
    ),
  ],
),
```

TO

```
void initState() {
  _controller = AnimationController(
    vsync: this,
    duration: Duration(seconds: 1),
  ); 
  // No addListener(...)
}AnimatedBuilder(
  animation: _controller,
  builder: (_, child) { 
    return Transform.translate(
      offset: Offset(100 * _controller.value, 0),
      child: child,
    );
  },
  child: Placeholder(),
),
```

## 简短说明

它导致重建整个 UI 不仅动画小部件而且使动画滞后

## Long Explanation

[Flutter Laggy Animations: How Not To setStateCreating animations in Flutter is really fun and easy, but there is one bad practice that might make those animations…medium.com](https://medium.com/flutter-community/flutter-laggy-animations-how-not-to-setstate-f2dd9873b8fc)

# 8. 避免在 AnimatedBuilder 中使用 Opacity

```
// BADAnimatedBuilder(
  animation: _controller,
  builder: (_, child) {
    return Opacity(
      opacity: _controller.value,
      child: child,
    );
  },
  child: Placeholder(), 
),// GOODFadeTransition(
  opacity: _controller,
  child: Placeholder(),
),// OR ALTERNATIVELYAnimatedOpacity(
   opacity: opacity,
   duration: duration,
   child: Placeholder(),
),
```

## 简短说明

改变不透明度是一个非常昂贵的动画，Flutter 为我们提供了更好的解决方案

## Long Explanation

[Flutter Laggy Animations: How Not To setStateCreating animations in Flutter is really fun and easy, but there is one bad practice that might make those animations…medium.com](https://medium.com/flutter-community/flutter-laggy-animations-how-not-to-setstate-f2dd9873b8fc)

# 9. 使用 Keys 加速 Flutter 性能

```
// FROM
return value
  ? const SizedBox()
  : const Placeholder(),// TO
return value
  ? const SizedBox(key: ValueKey('SizedBox'))
  : const Placeholder(key: ValueKey('Placeholder')),----------------------------------------------
// FROM
final inner = SizedBox();
return value ? SizedBox(child: inner) : inner,// TO
final global = GlobalKey();
final inner = SizedBox(key: global);
return value ? SizedBox(child: inner) : inner,
```

## 简短说明

Flutter 在使用按键时可以更好地识别 Widget。这为我们提供了高达 4 倍的更好性能

**警告**

- `ValueKey` 可以让你的代码看起来有点臃肿
- `GlobalKey` 有点危险，但有时值得。

## Long Explanation

[Elements, Keys and Flutter’s performanceTL;DR: Widget Keys can improve the performance of our application in places where you do not get the promised 60 FPS.medium.com](https://medium.com/flutter-community/elements-keys-and-flutters-performance-3ef15c90f607)

# 10.优化图片ListView使用时的内存

```
ListView.builder(
  ...
  addAutomaticKeepAlives: false (true by default)
  addRepaintBoundaries: false (true by default)
);
```

## 简短说明

ListView 无法杀死它的孩子在屏幕上不可见。如果孩子有高分辨率的图像，它会导致消耗大量内存。

通过将这些选项设置为 false，可能会导致使用更多的 GPU 和 CPU 工作，但它可以解决我们的内存问题，并且您将获得非常高效的视图而不会出现明显问题。

## Long Explanation

[Flutter Memory Optimization SeriesAlmost anything in Flutter is optimized and enhanced by default as you may already know, thanks to the Flutter teams in…devmuaz.medium.com](https://devmuaz.medium.com/flutter-memory-optimization-series-8c4a73f3ea81)

感谢您的阅读。