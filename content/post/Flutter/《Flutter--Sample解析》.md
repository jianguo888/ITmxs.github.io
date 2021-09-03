---
title: "《Flutter  Sample解析》"
subtitle: ""
date: 2021-07-31T19:08:37+08:00
lastmod: 2021-07-31T19:08:37+08:00
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



> 本文主要介绍简单看了下 Flutter 源码中的例子，flutter_gallery。

<!--more-->



[flutter_gallery](https://github.com/flutter/flutter/tree/master/examples/flutter_gallery) 是学习 Flutter 的好例子。

------

# SizedBox

例子中多处用到 [SizedBox](https://api.flutter.dev/flutter/widgets/SizedBox-class.html)。

> A box with a specified size.

> If given a child, this widget forces its child to have a specific width and/or height (assuming values are permitted by this widget’s parent). If either the width or height is null, this widget will size itself to match the child’s size in that dimension.

> If not given a child, SizedBox will try to size itself as close to the specified height and width as possible given the parent’s constraints. If height or width is null or unspecified, it will be treated as zero.

这个控件中如果不指定子控件，则类似于 android 中的 [android.view.Space](https://developer.android.com/reference/android/widget/Space) 用于占位。

这个控件中如果指定子控件，则强制子控件大小跟当前控件一致。

```
SizedBox(
  width: 200.0,
  height: 300.0,
  child: const Card(child: Text('Hello World!')),
)
```

[Flutter之SizedBox学习使用 - weixin_33716154的博客 - CSDN博客](https://blog.csdn.net/weixin_33716154/article/details/88017225)

# mainAxisAlignment 和 crossAxisAlignment

Row 和 Column 控件中经常会看到 [mainAxisAlignment](https://flutter.dev/docs/development/ui/layout) 和 `crossAxisAlignment` 两个属性。`mainAxisAlignment` 是主轴，`crossAxisAlignment` 是交叉轴。具体来说，

- 对于 Row，`mainAxisAlignment` 是横轴，`crossAxisAlignment` 是纵轴
- 对于 Column，`mainAxisAlignment` 是纵轴，`crossAxisAlignment` 是横轴

> You control how a row or column aligns its children using the mainAxisAlignment and crossAxisAlignment properties. For a row, the main axis runs horizontally and the cross axis runs vertically. For a column, the main axis runs vertically and the cross axis runs horizontally.

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/img/row-diagram.png)

- [Flutter中MainAxisAlignment和CrossAxisAlignment详解 - 喻志强的博客 - CSDN博客](https://blog.csdn.net/yuzhiqiang_1993/article/details/86496145)
- [Flutter进阶—垂直和水平布局 - 咖啡花园 - CSDN博客](https://blog.csdn.net/hekaiyou/article/details/70849178)
- [Flutter crossAxisAlignment vs mainAxisAlignment - Stack Overflow](https://stackoverflow.com/questions/53850149/flutter-crossaxisalignment-vs-mainaxisalignment)

# 图标居中的技巧

```
Container(
  width: 56.0,
  height: 56.0,
  alignment: Alignment.center,
  child: Icon(
    demo.icon,
    size: 24.0,
    color: isDark ? Colors.white : _kFlutterBlue,
  ),
),
```