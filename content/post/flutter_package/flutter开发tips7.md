---
title: "flutter开发tips7"
date: 2021-08-18T14:05:48+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter"]
categories: ["Flutter"]
---

# Provider



# Riverpod



# Dio



# Translator



# Location



# Url Launcher



```dart
const url = '<https://tomicriedel.medium.com>';
if(await canLaunch(url)){
	await launch(url);
}else {
	throw 'Could not launch url';
}
```

# Animated Text Kit

这个包应该已经被很多人知道了，因为它在[pub.dev](http://pub.dev/)上有 1900 个[赞](http://pub.dev/)，已经成为 Flutter 的最爱，并且已经成为本周包的一集。

顾名思义，您可以使用它为**任何文本设置动画**。已经有很多预定义的动画。我个人最喜欢的是 Rotate、Fade、Typer、Typewriter、Colorizer 和 Scale，但也有 TextLiquidFill、Wavy 和 Flicker。

如果这些都不适合您的需求，您可以轻松**创建自己的**动画。

# Reordable List View

这是一个不是来自[pub.dev](http://pub.dev/)包的小部件。顾名思义，它为您提供了一个 ListView，然后您可以**重新排列这些项目**。我经常使用它，这个小部件也成为了本周的 Flutter 小部件，所以在[YouTube 上](https://www.youtube.com/watch?v=3fB1mxOsqJE)有一个非常好的和快速的教程。

# Fl chart

很多人应该都知道[fl_chart](https://pub.dev/packages/fl_chart)包，但它太好了，不得不添加到列表中。正如包名称所说，您可以使用它创建图表。官方的README文件有5种类型，可以自定义的非常多。这 5 种类型是 LineChart、BarChart、PieChart、ScatterChart 和 RadarChart。

# Flutter SVG

在我看来，SVG 图像对于应用程序非常重要，因为没有人希望看到像素化和模糊的插图。不幸的是，如果没有包，在 Flutter 中使用 SVG 图像非常困难，并且您将不得不编写大量不必要的代码。幸运的是，有一个解决方案，[flutter_svg](https://pub.dev/packages/flutter_svg)包。使用这个包，您可以轻松**使用 SVG 图像，**而无需编写大量代码。