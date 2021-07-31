---
title: "《Flutter SizedOverflowBox 简介及案例》"
subtitle: ""
date: 2021-07-31T21:15:48+08:00
lastmod: 2021-07-31T21:15:48+08:00
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



> 本文主要介绍OverflowBox 和 SizedOverflowBox 允许子控件超出父控件的边界。这个特性可以用来实现一些比较棘手的视觉效果。

<!--more-->





设计给出如下视觉图：

![-w393](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2020/02/18/15820301730961.jpg)

初看觉得没有什么特别的，不过是7个控件平均水平分布。但仔细观察之后发现并非如此：其中一个控件(表示当天，称之A控件)的背景比较特别，超出边框范围带一点光晕效果。这个光晕背景其实比其他控件(表示非当天，称之B控件)的背景占据的空间要大。

## 方案一 - “理想的背景图”

控件A和控件B的背景大小不一致，且要求水平方向平均分布。一种可行的方式是使用实际上大小相同、但视觉上有差异的背景图，比如控件A使用 100x100带光晕效果的背景图，控件B使用 100x100的透明背景图。可惜我们视觉未给出这种**理想的背景图**

## 方案二 - “聪明的边距”

既然控件A和控件B的背景大小不一致，又要求水平方向平均分布。如果没有”理想的背景图”，我们还可以想办法：

- 让控件A和控件B大小不一致(以达到视觉上”看起来”大小一致的效果)
- 添加不同大小的边距(padding 或 margin)让它们”看起来”水平平均分布

不过添加”聪明的边距”的代码会惨不忍睹，几乎没有可维护性。

## 方案三 - OverflowBox 和 SizedOverflowBox

看如何使用 SizedOverflowBox 来解决这个问题。

### 优化前

优化前，光晕背景图无法占满控件A，视觉效果不佳。

![img](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2020/02/18/15820344976572.jpg)

控件A代码如下：

```
@override
Widget build(BuildContext context) {
  return Container(
    width: 36,
    height: 36,
    decoration: BoxDecoration(
        border: Border.all(
          width: 1,
          color: c_FFFFE1A6,
        ),
        borderRadius: BorderRadius.all(Radius.circular(3)),
        image: DecorationImage(
            image: AssetImage(_bgAwardToday), fit: BoxFit.fitHeight)),
    child: child,
    alignment: Alignment.center,
  );
}
```

### 优化后

优化后，光晕背景图可以占满控件A，完美还原视觉图。
![img](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2020/02/18/15820345882075.jpg)

此外，控件A的大小为 (width=36.0, height=36.0)，与控件B的大小完全一致。由于控件A和控件B的大小一致，所以让它们水平方向平均分布的代码非常简单优雅，`spaceBetween` 即可。

```
Row(mainAxisAlignment: MainAxisAlignment.spaceBetween,
  children: []
)
```

控件A代码如下：

```
@override
Widget build(BuildContext context) {
  return SizedOverflowBox(
    size: const Size(36.0, 36.0),
    child: Stack(alignment: Alignment.center, children: [
      Image.asset(
        _bgAwardToday,
        width: 56,
      ),
      SizedBox(
        child: child,
        width: 36,
        height: 36,
      ),
    ]),
    alignment: Alignment.center,
  );
}
```

------

以下是控件B的代码。用于对照参考。

```
  @override
Widget build(BuildContext context) {
  return Container(
    width: 36,
    height: 36,
    decoration: BoxDecoration(
        border: Border.all(
          width: 1,
          color: Color(0X62FFFFFF),
        ),
        borderRadius: BorderRadius.all(Radius.circular(3)),
        image: DecorationImage(image: AssetImage(_bgAwardOthers))),
    child: child,
    alignment: Alignment.center,
  );
}
```

# SliceSizedOverflowBox 介绍

TODO

# 参考

如下，蓝色矩形子控件超出了浅蓝色矩形父控件的区域。

![-w225](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2020/02/18/15820350888457.jpg)

```
class SliceSizedOverflowBox extends StatelessWidget implements SliceExample {
  @override
  String get name => 'SliceSizedOverflowBox';

  @override
  Widget build(BuildContext context) {
    return Container(
      color: Colors.blue[50],
      child: SizedOverflowBox(
        size: const Size(100.0, 100.0),
        alignment: AlignmentDirectional.bottomStart,
        child: Container(height: 50.0, width: 150.0, color: Colors.blue,),
      ),
    );
  }
}
```

- [SizedOverflowBox class - widgets library - Dart API](https://api.flutter.dev/flutter/widgets/SizedOverflowBox-class.html)
- [flutter-widget-livebook/slice_sized_overflow_box.dart at master · blankapp/flutter-widget-livebook](https://github.com/blankapp/flutter-widget-livebook/blob/master/uiexplorer/lib/views/slice_sized_overflow_box/slice_sized_overflow_box.dart)