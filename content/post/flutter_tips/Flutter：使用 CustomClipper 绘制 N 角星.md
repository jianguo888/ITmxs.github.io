---
title: "《Flutter中的提示工具》"
subtitle: ""
date: 2021-08-20T10:47:09+08:00
lastmod: 2021-08-20T10:47:09+08:00
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

本文将向您展示如何使用Flutter 中的**[CustomClipper](https://api.flutter.dev/flutter/rendering/CustomClipper-class.html)**类绘制**n 角星**（5 角星、6 角星、10 角星、20 角星等）。无需安装任何第三方软件包。我们将从头开始制作东西。

## 重点是什么？

1.通过扩展CustomClipper类实现一个可重用的自定义裁剪器：

```D
// This custom clipper help us achieve n-pointed star shape
class StarClipper extends CustomClipper<Path> {
  /// The number of points of the star
  final int points;
  StarClipper(this.points);

  // Degrees to radians conversion
  double _degreeToRadian(double deg) => deg * (math.pi / 180.0);

  @override
  Path getClip(Size size) {
    Path path = Path();
    double max = 2 * math.pi;

    double width = size.width;
    double halfWidth = width / 2;

    double wingRadius = halfWidth;
    double radius = halfWidth / 2;

    double degreesPerStep = _degreeToRadian(360 / points);
    double halfDegreesPerStep = degreesPerStep / 2;

    path.moveTo(width, halfWidth);

    for (double step = 0; step < max; step += degreesPerStep) {
      path.lineTo(halfWidth + wingRadius * math.cos(step),
          halfWidth + wingRadius * math.sin(step));
      path.lineTo(halfWidth + radius * math.cos(step + halfDegreesPerStep),
          halfWidth + radius * math.sin(step + halfDegreesPerStep));
    }

    path.close();
    return path;
  }

  // If the new instance represents different information than the old instance, this method will return true, otherwise it should return false.
  @override
  bool shouldReclip(CustomClipper<Path> oldClipper) {
    StarClipper starClipper = oldClipper as StarClipper;
    return points != starClipper.points;
  }
}

```

2. 现在您可以轻松绘制星星了，如下所示：

```dart
 SizedBox(
                height: 360,
                width: 360,
                child: ClipPath(
                  clipper: StarClipper(6),
                  child: Container(
                    height: 300,
                    color: Colors.amber,
                  ),
                ),
              ),
```

## 完整示例

### 预览

此示例生成 4 种不同的星形：5 角星、6 角星、10 角星和 20 角星。

![img](D:\study_manger\hugo_LoveIt_blog\content\post\flutter_tips\Flutter：使用 CustomClipper 绘制 N 角星.assets\Screen-Shot-2021-10-11-at-14.30.25.jpg)

### 编码

**main.dart 中**的完整源代码和解释：

```dart
// main.dart
import 'package:flutter/material.dart';
import 'dart:math' as math;

// This custom clipper help us achieve n-pointed star shape
class StarClipper extends CustomClipper<Path> {
  /// The number of points of the star
  final int points;
  StarClipper(this.points);

  // Degrees to radians conversion
  double _degreeToRadian(double deg) => deg * (math.pi / 180.0);

  @override
  Path getClip(Size size) {
    Path path = Path();
    double max = 2 * math.pi;

    double width = size.width;
    double halfWidth = width / 2;

    double wingRadius = halfWidth;
    double radius = halfWidth / 2;

    double degreesPerStep = _degreeToRadian(360 / points);
    double halfDegreesPerStep = degreesPerStep / 2;

    path.moveTo(width, halfWidth);

    for (double step = 0; step < max; step += degreesPerStep) {
      path.lineTo(halfWidth + wingRadius * math.cos(step),
          halfWidth + wingRadius * math.sin(step));
      path.lineTo(halfWidth + radius * math.cos(step + halfDegreesPerStep),
          halfWidth + radius * math.sin(step + halfDegreesPerStep));
    }

    path.close();
    return path;
  }

  // If the new instance represents different information than the old instance, this method will return true, otherwise it should return false.
  @override
  bool shouldReclip(CustomClipper<Path> oldClipper) {
    StarClipper starClipper = oldClipper as StarClipper;
    return points != starClipper.points;
  }
}

void main() {
  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({Key? key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      title: 'KindaCode.com',
      theme: ThemeData(
        primarySwatch: Colors.indigo,
      ),
      home: const MyHomePage(),
    );
  }
}

class MyHomePage extends StatefulWidget {
  const MyHomePage({Key? key}) : super(key: key);

  @override
  _MyHomePageState createState() => _MyHomePageState();
}

class _MyHomePageState extends State<MyHomePage> {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Kindacode.com'),
      ),
      body: Padding(
        padding: const EdgeInsets.all(20),
        child: Center(
          child: Column(
            children: [
              // 5-pointed star
              SizedBox(
                height: 180,
                width: 180,
                child: ClipPath(
                  clipper: StarClipper(5),
                  child: Container(
                    height: 150,
                    color: Colors.green,
                  ),
                ),
              ),

              // 6-pointed star
              SizedBox(
                height: 180,
                width: 180,
                child: ClipPath(
                  clipper: StarClipper(6),
                  child: Container(
                    height: 150,
                    color: Colors.amber,
                  ),
                ),
              ),

              // 10-pointed star
              SizedBox(
                height: 180,
                width: 180,
                child: ClipPath(
                  clipper: StarClipper(10),
                  child: Container(
                    height: 150,
                    color: Colors.indigo,
                  ),
                ),
              ),

              // 20-pointed star
              SizedBox(
                height: 180,
                width: 180,
                child: ClipPath(
                  clipper: StarClipper(20),
                  child: Container(
                    height: 150,
                    color: Colors.cyan,
                  ),
                ),
              ),
            ],
          ),
        ),
      ),
    );
  }
}
```

## 结论

我们从头开始绘制自定义星形，没有使用任何第三个插件。如果您想在现代 Flutter 中探索更多新奇有趣的东西