---
title: "Flutter：如何使用 CustomPaint 绘制心形"
subtitle: ""
date: 2021-05-24T10:47:09+08:00
lastmod: 2021-05-24T10:47:09+08:00
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



> 作为程序员其实也有浪漫的一幕，今天我们一起借助**CustomPaint**和**CustomPainter**绘制心形，本文将带您了解在 Flutter 中使用**CustomPaint**和**CustomPainter**绘制心形的端到端示例。闲话少说（比如谈论 Flutter 的历史或它有多华丽），让我们深入研究代码并制作一些东西。



## 例子

### 预览

我们将创建 4 个心形。第一个没有边界，但其他的有。

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/image/Screen-Shot-2021-10-14-at-16.09.22.jpg)

### 步骤

1.通过扩展CustomPainter类来实现一个画笔：

```dart
class MyPainter extends CustomPainter {
  // The color of the heart
  final Color bodyColor;

  // The color of the border of the heart
  final Color borderColor;
  // The thickness of the border
  final double borderWith;

  MyPainter(this.bodyColor, this.borderColor, this.borderWith);

  @override
  void paint(Canvas canvas, Size size) {
    // The body of the heart
    final Paint body = Paint();
    body
      ..color = bodyColor
      ..style = PaintingStyle.fill
      ..strokeWidth = 0;

    // The border of the heart
    final Paint border = Paint();
    border
      ..color = borderColor
      ..style = PaintingStyle.stroke
      ..strokeCap = StrokeCap.round
      ..strokeWidth = borderWith;

    final double width = size.width;
    final double height = size.height;

    final Path path = Path();
    path.moveTo(0.5 * width, height * 0.4);
    path.cubicTo(0.2 * width, height * 0.1, -0.25 * width, height * 0.6,
        0.5 * width, height);
    path.moveTo(0.5 * width, height * 0.4);
    path.cubicTo(0.8 * width, height * 0.1, 1.25 * width, height * 0.6,
        0.5 * width, height);

    canvas.drawPath(path, body);
    canvas.drawPath(path, border);
  }
```

2.使用 CustomPaint 小部件和我们之前创建的画家绘制心形：

```dart
// Non-border heart
          CustomPaint(
            size: const Size(280, 260),
            painter: MyPainter(Colors.pink, Colors.transparent, 0),
          ),

// Hearts with borders
          CustomPaint(
            size: const Size(200, 120),
            painter: MyPainter(Colors.purple, Colors.black, 10),
          ),
          CustomPaint(
            size: const Size(200, 240),
            painter: MyPainter(Colors.red, Colors.redAccent, 5),
          ),
          CustomPaint(
            size: const Size(50, 100),
            painter: MyPainter(Colors.amber, Colors.indigo, 10),
          ),
```

### 最终代码

这是**main.dart**中的完整代码，它生成了上面屏幕截图中显示的很酷的心形：

```dart
// main.dart
import 'package:flutter/material.dart';

void main() {
  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({Key? key}) : super(key: key);
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      // Hide the debug banner
      debugShowCheckedModeBanner: false,
      title: 'breeze',
      theme: ThemeData(
        primarySwatch: Colors.indigo,
      ),
      home: const HomeScreen(),
    );
  }
}

// Implementing our heart painter
class MyPainter extends CustomPainter {
  // The color of the heart
  final Color bodyColor;

  // The color of the border of the heart
  final Color borderColor;
  // The thickness of the border
  final double borderWith;

  MyPainter(this.bodyColor, this.borderColor, this.borderWith);

  @override
  void paint(Canvas canvas, Size size) {
    // The body of the heart
    final Paint body = Paint();
    body
      ..color = bodyColor
      ..style = PaintingStyle.fill
      ..strokeWidth = 0;

    // The border of the heart
    final Paint border = Paint();
    border
      ..color = borderColor
      ..style = PaintingStyle.stroke
      ..strokeCap = StrokeCap.round
      ..strokeWidth = borderWith;

    final double width = size.width;
    final double height = size.height;

    final Path path = Path();
    path.moveTo(0.5 * width, height * 0.4);
    path.cubicTo(0.2 * width, height * 0.1, -0.25 * width, height * 0.6,
        0.5 * width, height);
    path.moveTo(0.5 * width, height * 0.4);
    path.cubicTo(0.8 * width, height * 0.1, 1.25 * width, height * 0.6,
        0.5 * width, height);

    canvas.drawPath(path, body);
    canvas.drawPath(path, border);
  }

  @override
  bool shouldRepaint(CustomPainter oldDelegate) {
    return true;
  }
}

class HomeScreen extends StatefulWidget {
  const HomeScreen({Key? key}) : super(key: key);

  @override
  State<HomeScreen> createState() => _HomeScreenState();
}

class _HomeScreenState extends State<HomeScreen> {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('KindaCode.com'),
      ),
      body: Center(
          child: Column(
        mainAxisAlignment: MainAxisAlignment.spaceAround,
        mainAxisSize: MainAxisSize.min,
        children: [
          // Non-border heart
          CustomPaint(
            size: const Size(280, 260),
            painter: MyPainter(Colors.pink, Colors.transparent, 0),
          ),

          // Hearts with borders
          CustomPaint(
            size: const Size(200, 120),
            painter: MyPainter(Colors.purple, Colors.black, 10),
          ),
          CustomPaint(
            size: const Size(200, 240),
            painter: MyPainter(Colors.red, Colors.redAccent, 5),
          ),
          CustomPaint(
            size: const Size(50, 100),
            painter: MyPainter(Colors.amber, Colors.indigo, 10),
          ),
        ],
      )),
    );
  }
}
```

## 参考

您可以在官方文档中找到有关 CustomPaint 小部件和 CustomPainter 类的更多详细信息：

- [自定义绘制小部件](https://api.flutter.dev/flutter/widgets/CustomPaint-class.html)
- [CustomPainter 类](https://api.flutter.dev/flutter/rendering/CustomPainter-class.html)

## 后记

您已经学会了如何在不使用任何第三方软件包的情况下从头开始绘制自定义心形。此时，您应该对 Flutter 中的绘图有了更好的了解。

