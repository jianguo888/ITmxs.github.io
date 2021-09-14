---
title: "Flutter如何在没有插件的情况下制作旋转动画"
date: 2021-09-14T17:00:18+08:00
draft: false
tags: ["Flutter"]
categories: ["Flutter"]
---

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/image/Screen-Shot-2021-09-11-at-12.50.56.jpg)

本文将向您展示如何使用Flutter 中内置的**RotationTransition**小部件创建旋转动画。

## 快速说明

该**RotationTransition**小部件用于创建一个旋转的转变。它可以采用一个子部件和一个控制该子部件旋转的动画：

```go
RotationTransition(
   turns: _animation,
   child: /* Your widget here */
}
```

您可以创建一个无限旋转的动画，如下所示：

```go
// Create a controller
late final AnimationController _controller = AnimationController(
    duration: const Duration(seconds: 2),
    vsync: this,
)..repeat(reverse: false);

// Create an animation with value of type "double"
late final Animation<double> _animation = CurvedAnimation(
    parent: _controller,
    curve: Curves.linear,
);
```

要停止动画，只需调用***stop()\***方法：

```go
_controller.stop()
```

要开始动画，请使用***repeat()\***方法：

```go
_controller.repeat()
```



为了更清楚，请参阅下面的示例。



## 完整示例

我们将要构建的应用程序包含一个浮动操作按钮和一个由四种不同颜色的四个圆圈组合而成的小部件。一开始，小部件会自行无限旋转。但是，您可以使用浮动按钮停止和重新启动动画。

### 应用预览



<video class="" autoplay="" controls="" loop="" muted="" data-lazy-type="video" data-src="https://www.kindacode.com/wp-content/uploads/2021/09/Flutter-spinning.mp4" playsinline="" src="https://www.kindacode.com/wp-content/uploads/2021/09/Flutter-spinning.mp4" __idm_id__="864126978" style="margin: 0px; padding: 0px; box-sizing: border-box; width: 720px;"></video>

### 编码

**main.dart 中**的完整源代码和解释：

```dart
// main.dart
import 'package:flutter/material.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
        // Remove the debug banner
        debugShowCheckedModeBanner: false,
        title: 'Kindacode.com',
        theme: ThemeData(
          primarySwatch: Colors.indigo,
        ),
        home: HomePage());
  }
}

class HomePage extends StatefulWidget {
  const HomePage({Key? key}) : super(key: key);
  @override
  _HomePageState createState() => _HomePageState();
}

class _HomePageState extends State<HomePage> with TickerProviderStateMixin {
  // Create a controller
  late final AnimationController _controller = AnimationController(
    duration: const Duration(seconds: 2),
    vsync: this,
  )..repeat(reverse: false);

  // Create an animation with value of type "double"
  late final Animation<double> _animation = CurvedAnimation(
    parent: _controller,
    curve: Curves.linear,
  );

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Kindacode.com'),
      ),
      body: Center(
        child: RotationTransition(
          turns: _animation,
          child: Padding(
            padding: EdgeInsets.all(8.0),
            child: Container(
              child: Wrap(
                children: [
                  Container(
                    width: 150,
                    height: 150,
                    decoration: BoxDecoration(
                        color: Colors.amber, shape: BoxShape.circle),
                  ),
                  Container(
                    width: 150,
                    height: 150,
                    decoration: BoxDecoration(
                        color: Colors.green, shape: BoxShape.circle),
                  ),
                  Container(
                    width: 150,
                    height: 150,
                    decoration: BoxDecoration(
                        color: Colors.indigo, shape: BoxShape.circle),
                  ),
                  Container(
                    width: 150,
                    height: 150,
                    decoration: BoxDecoration(
                        color: Colors.red, shape: BoxShape.circle),
                  )
                ],
              ),
            ),
          ),
        ),
      ),
      // This button is used to toggle the animation
      floatingActionButton: FloatingActionButton(
        child: Icon(Icons.moving),
        onPressed: () {
          if (_controller.isAnimating) {
            _controller.stop(); // Stop the animation
          } else {
            _controller.repeat(); // Start the animation
          }
        },
      ),
    );
  }

  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }
}
```



您可以在[官方文档](https://api.flutter.dev/flutter/widgets/RotationTransition-class.html)(flutter.dev) 中找到有关 RotationTransition 类的更多信息。

## 结论

您已经在不使用任何第三方软件包的情况下构建了自己的旋转动画。如果您想了解有关 Flutter 中动画和其他有趣内容的更多信息，
