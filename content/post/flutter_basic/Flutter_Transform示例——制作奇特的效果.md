---
title: "Flutter_Transform示例——制作奇特的效果"
date: 2021-09-14T17:07:49+08:00
draft: false
tags: ["Flutter"]
categories: ["Flutter"]
---

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/image/Screen-Shot-2021-01-08-at-22.08.15.jpg)

通过实例学习是最有效的学习方法之一，尤其是在 Web 和移动开发中。在本文中，我们将介绍几个使用**Transform 的****完整示例**，这是一个可以在 Flutter 应用程序中转换其子项的小部件。



## 示例 1 – 基本（无动画）

此示例包含一个紫色框和一个浮动按钮。当用户按下按钮时，框会跳到屏幕上的随机位置。为简单起见，我们不会在此示例中实现动画，但您可以在第二个示例中看到它。广告





### **应用预览**

1.00

<video class="" autoplay="" controls="" loop="" muted="" data-lazy-type="video" data-src="https://www.kindacode.com/wp-content/uploads/2021/01/Flutter-Transform-1.mp4" src="https://www.kindacode.com/wp-content/uploads/2021/01/Flutter-Transform-1.mp4" __idm_id__="429228033" style="margin: 0px; padding: 0px; box-sizing: border-box; width: 720px;"></video>

### **完整代码**

```dart
import 'package:flutter/material.dart';
import 'dart:math';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      title: 'Kindacode.com',
      home: HomePage(),
    );
  }
}

class HomePage extends StatefulWidget {
  @override
  _HomePageState createState() => _HomePageState();
}

class _HomePageState extends State<HomePage> with TickerProviderStateMixin {
  final _random = Random();

  double _x = 0;
  double _y = 0;
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Kindacode.com'),
      ),
      body: Transform.translate(
          offset: Offset(_x, _y),
          child: Container(width: 200, height: 200, color: Colors.purple)),
      floatingActionButton: FloatingActionButton(
        onPressed: () {
          setState(() {
            _x = _random.nextDouble() * 400;
            _y = _random.nextDouble() * 500;
          });
        },
        child: Icon(Icons.refresh),
      ),
    );
  }
}
```

## 示例 2 – 动画整个屏幕

此示例转换了整个应用程序。我们将使用[**Matrix4.compose** ](https://api.flutter.dev/flutter/vector_math_64/Matrix4-class.html)构造函数作为**变换**小部件的**变换**参数：

```go
Matrix4.compose(
  Vector3 translation, 
  Quaternion rotation, 
  Vector3 scale
)
```

我们还将使用**[TweenAnimationBuilder]()**来创建动画效果。

### **应用预览**

1.00

<video class="" autoplay="" controls="" loop="" muted="" data-lazy-type="video" data-src="https://www.kindacode.com/wp-content/uploads/2021/01/Flutter-Transform.mp4" src="https://www.kindacode.com/wp-content/uploads/2021/01/Flutter-Transform.mp4" __idm_id__="429228034" style="margin: 0px; padding: 0px; box-sizing: border-box; width: 720px;"></video>

**完整代码：**

```go
import 'package:flutter/material.dart';

// Import this to use the Vector3 and Quaternion classes
// This module comes with Flutter so you don't need to install any thing
import 'package:vector_math/vector_math_64.dart' as vector;

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      title: 'Kindacode.com',
      home: HomePage(),
    );
  }
}

class HomePage extends StatefulWidget {
  @override
  _HomePageState createState() => _HomePageState();
}

class _HomePageState extends State<HomePage> with TickerProviderStateMixin {
  final Matrix4 _begin = Matrix4.compose(vector.Vector3(0, 0, 1),
      vector.Quaternion.euler(0, 0, 0), vector.Vector3(1, 1, 1));

  final Matrix4 _midway = Matrix4.compose(vector.Vector3(0, 0, 1),
      vector.Quaternion.euler(1, -0.2, 0), vector.Vector3(.8, .8, .8));

  final Matrix4 _final = Matrix4.compose(vector.Vector3(0, 0, 1),
      vector.Quaternion.euler(-1, 0, 0), vector.Vector3(1, 1, 1));

  Matrix4 _target = Matrix4.compose(vector.Vector3(0, 0, 1),
      vector.Quaternion.euler(1, -0.2, 0), vector.Vector3(0.8, 0.8, 0.8));

  @override
  Widget build(BuildContext context) {
    return TweenAnimationBuilder(
        tween: Tween(begin: _begin, end: _target),
        duration: Duration(seconds: 2),
        builder: (BuildContext context, Matrix4 value, Widget? _) {
          return Transform(
            transform: value,
            child: Scaffold(
              appBar: AppBar(
                title: Text('Kindacode.com'),
              ),
              body: Center(
                child: Container(width: 200, height: 200, color: Colors.amber),
              ),
              floatingActionButton: FloatingActionButton(
                  onPressed: () {
                    setState(() {
                      if (_target == _begin) {
                        _target = _midway;
                      } else if (_target == _midway) {
                        _target = _final;
                      } else {
                        _target = _begin;
                      }
                    });
                  },
                  child: Icon(Icons.rotate_left)),
            ),
          );
        });
  }
}
```

## 结论

以上示例，您应该对使用 Transform 在您的应用程序中创建酷炫效果有了更好的理解并更有信心。
