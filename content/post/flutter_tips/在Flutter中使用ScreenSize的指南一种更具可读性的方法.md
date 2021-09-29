---
title: "在Flutter中使用ScreenSize的指南一种更具可读性的方法"
date: 2021-09-29T08:52:43+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---

> 本文主要介绍在 Flutter 中使用 ScreenSize 的指南 - 一种更具可读性的方法

使用上下文减少功能在 Flutter 中获取当前屏幕的大小。

 Flutter 中获取屏幕尺寸是微不足道的，而不是冗长的（在大多数情况下）。这就是你如何做到的。

```dart
var screenSize = MediaQuery.of(context).size;
var width = screenSize.width;
var height = screenSize.height;
```

在某些布局中，您需要对这些尺寸进行一些计算。您还必须在不同的小部件中重新使用它们。拿下面的图片。

![网格布局截图](https://www.filledstacks.com/assets/static/008-screenshot-1.7d545b4.a594c2e47756fa93ce2d5fdf06afa12b.jpg)

在这里你需要做一些事情。有一个屏幕高度一半的容器（蓝色），另一个容器是屏幕高度的三分之一（黄色），右侧有一个工具栏（紫色），同时考虑添加到 Scaffold 的 AppBar 的高度. 让我们看看使用 reducer 会是什么样子。

让我们首先创建一个名为 screensize_reducers.dart 的文件，我们将首先添加一个 screenSize 函数，其中包含基本的 screenHeight 和 screenWidth 缩减器，它将高度除以我们传入的数字。默认为 1。

```dart
Size screenSize(BuildContext context) {
  return MediaQuery.of(context).size;
}

double screenHeight(BuildContext context, {double dividedBy = 1}) {
  return screenSize(context).height / dividedBy;
}

double screenWidth(BuildContext context, {double dividedBy = 1}) {
  return screenSize(context).width / dividedBy;
}
```

确保你有一个正在运行的项目，然后让你的构建方法看起来像这样。

```dart
class Home extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Column(
        children: <Widget>[
        Container(
          color: Colors.blue[200],
          height: screenHeight(context,
              dividedBy: 2),
        ),
        Container(
            color: Colors.yellow[200],
            height: screenHeight(context,
                dividedBy: 3))
      ]),
    );
  }
}
```

你应该看到这样的事情。

![蓝黄方块](https://www.filledstacks.com/assets/static/008-screenshot-2.7d545b4.f1f4f997e6b674a61694b29b7eafa76e.jpg)

使用 提供的屏幕高度的一个问题`MediaQuery`是，当您使用工具栏时，它不会考虑它，因此在可用空间的情况下，高度总是错误的。让我们向脚手架添加一个工具栏并更新我们的 screenHeight 方法以接收新值。我们将创建一个新函数来利用这个值。

```dart
double screenHeight(BuildContext context, 
  {double dividedBy = 1, 
  double reducedBy = 0.0}) {
  return (screenSize(context).height - reducedBy) / dividedBy;
}

double screenHeightExcludingToolbar(BuildContext context, {double dividedBy = 1}) {
  return screenHeight(context, dividedBy: dividedBy, reducedBy: kToolbarHeight);
}

...

// Updated build function
Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Screen Height')),
      body: Column(
        children: <Widget>[
        Container(
          color: Colors.blue[200],
          height: screenHeightExcludingToolbar(context,
              dividedBy: 2),
        ),
        Container(
            color: Colors.yellow[200],
            height: screenHeightExcludingToolbar(context,
                dividedBy: 3))
      ]),
    );
  }
```

添加 AppBar 并且仍然使用旧的 screenHeight 函数时的屏幕截图

![缩小底部空间的屏幕截图](https://www.filledstacks.com/assets/static/008-screenshot-3.7d545b4.c6ba210f2ddd6908ba97d81d3ae3ca6d.jpg)

使用新的排除工具栏功能的屏幕截图。

![调整底部空间的屏幕截图](https://www.filledstacks.com/assets/static/008-screenshot-4.7d545b4.441724838d7266f8fcd859ff9b912e2f.jpg)

只是为了完成布局，让你的构建方法看起来像这样。然后它应该看起来像我们展示的起始屏幕截图。对我来说，代码更容易阅读，这对我的代码维护和健康非常重要。

```dart
 Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Screen Height')),
      body: Row(
        children: <Widget>[
          Expanded(
            child: Column(children: <Widget>[
              Container(
                color: Colors.blue[200],
                height: screenHeightExcludingToolbar(context, dividedBy: 2),
              ),
              Container(
                  color: Colors.yellow[200],
                  height: screenHeightExcludingToolbar(context, dividedBy: 3))
            ]),
          ),
          Container(
            width: 80.0,
            color: Colors.purple[300],
          )
        ],
      ),
    );
  }
```

这不是什么大启示，但使用“上下文缩减器”可以清理一些不需要的代码。我使用它从我的上下文中获取大部分内容，以使我的代码易于阅读。以下是此 screen_size_reducers.dart 文件的完整副本

```dart
Size screenSize(BuildContext context) {
  return MediaQuery.of(context).size;
}

double screenHeight(BuildContext context,
    {double dividedBy = 1, double reducedBy = 0.0}) {
  return (screenSize(context).height - reducedBy) / dividedBy;
}

double screenWidth(BuildContext context,
    {double dividedBy = 1, double reducedBy = 0.0}) {
  return (screenSize(context).width - reducedBy) / dividedBy;
}

double screenHeightExcludingToolbar(BuildContext context,
    {double dividedBy = 1}) {
  return screenHeight(context, dividedBy: dividedBy, reducedBy: kToolbarHeight);
}
```

感谢您的阅读。