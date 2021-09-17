---
title: "在flutter中创建反转卡片动画的2种方法"
date: 2021-09-17T21:28:43+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---

> 本文将带您了解在 Flutter 中制作翻转卡片动画的两个完整示例。第一个示例从头开始实现，第二个示例使用第三方包。闲话少说，让我们动手吧。

<!--more-->



## 使用自写代码

本示例使用变换小部件创建翻转卡片效果。

### 预览

我们将要构建的演示应用程序显示了两张隐藏一些秘密的卡片。您可以通过按“**揭示秘密”**按钮来揭开面具背后的东西。最上面的卡片展示了一个水平翻转动画，底部一张展示了一个垂直翻转动画。



<video class="" autoplay="" controls="" loop="" muted="" data-lazy-type="video" data-src="https://www.kindacode.com/wp-content/uploads/2021/09/Flutter-Flipping-Card.mp4" playsinline="" src="https://www.kindacode.com/wp-content/uploads/2021/09/Flutter-Flipping-Card.mp4" __idm_id__="785937410" style="margin: 0px; padding: 0px; box-sizing: border-box; width: 720px;"></video>

### 完整代码

```dart
// main.dart
import 'package:flutter/material.dart';
import 'dart:math';

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
          primarySwatch: Colors.amber,
        ),
        home: HomePage());
  }
}

class HomePage extends StatefulWidget {
  const HomePage({Key? key}) : super(key: key);

  @override
  _HomePageState createState() => _HomePageState();
}

class _HomePageState extends State with SingleTickerProviderStateMixin {
  late AnimationController _controller;
  late Animation _animation;
  AnimationStatus _status = AnimationStatus.dismissed;

  @override
  void initState() {
    super.initState();
    _controller =
        AnimationController(vsync: this, duration: Duration(seconds: 1));
    _animation = Tween(end: 1.0, begin: 0.0).animate(_controller)
      ..addListener(() {
        setState(() {});
      })
      ..addStatusListener((status) {
        _status = status;
      });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Kindacode.com'),
      ),
      body: Center(
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.center,
          children: [
            SizedBox(
              height: 30,
            ),
            // Horizontal Flipping
            Transform(
              alignment: FractionalOffset.center,
              transform: Matrix4.identity()
                ..setEntry(3, 2, 0.0015)
                ..rotateY(pi * _animation.value),
              child: Card(
                child: _animation.value <= 0.5
                    ? Container(
                        color: Colors.deepOrange,
                        width: 240,
                        height: 300,
                        child: Center(
                            child: Text(
                          '?',
                          style: TextStyle(fontSize: 100, color: Colors.white),
                        )))
                    : Container(
                        width: 240,
                        height: 300,
                        color: Colors.grey,
                        child: Image.network(
                          'https://www.kindacode.com/wp-content/uploads/2021/09/girl.jpeg',
                          fit: BoxFit.cover,
                        )),
              ),
            ),
            // Vertical Flipping
            SizedBox(
              height: 30,
            ),
            Transform(
              alignment: FractionalOffset.center,
              transform: Matrix4.identity()
                ..setEntry(3, 2, 0.0015)
                ..rotateX(pi * _animation.value),
              child: Card(
                child: _animation.value <= 0.5
                    ? Container(
                        color: Colors.deepPurple,
                        width: 240,
                        height: 300,
                        child: Center(
                            child: Text(
                          '?',
                          style: TextStyle(fontSize: 100, color: Colors.white),
                        )))
                    : Container(
                        width: 240,
                        height: 300,
                        color: Colors.grey,
                        child: RotatedBox(
                          quarterTurns: 2,
                          child: Image.network(
                            'https://www.kindacode.com/wp-content/uploads/2021/09/flower.jpeg',
                            fit: BoxFit.cover,
                          ),
                        )),
              ),
            ),
            ElevatedButton(
                onPressed: () {
                  if (_status == AnimationStatus.dismissed) {
                    _controller.forward();
                  } else {
                    _controller.reverse();
                  }
                },
                child: Text('Reveal The Secrets'))
          ],
        ),
      ),
    );
  }
}
```

## 使用第三个插件

从头开始编写代码可能既麻烦又耗时。如果您想快速而整洁地完成工作，那么使用插件中的预制小部件是一个不错的选择。下面的示例使用了一个名为[flip_card](https://pub.dev/packages/flip_card)的很棒的包。

### 预览



<video class="" autoplay="" controls="" loop="" muted="" data-lazy-type="video" data-src="https://www.kindacode.com/wp-content/uploads/2021/09/Flutter-flip-card.mp4" playsinline="" src="https://www.kindacode.com/wp-content/uploads/2021/09/Flutter-flip-card.mp4" __idm_id__="785937411" style="margin: 0px; padding: 0px; box-sizing: border-box; width: 720px;"></video>

### 编码

1.将插件添加到您的项目中：

```
flutter pub add flip_card
```

您可能需要运行：

```
flutter pub get
```

安装插件。

2.实现插件提供的**FlipCard**小部件：

```dart
Center(
        child: FlipCard(
          direction: FlipDirection.HORIZONTAL, 
          front: Container(
            width: 300,
            height: 400,
            color: Colors.red,
          ),
          back: Container(
            width: 300,
            height: 400,
            color: Colors.blue,
          ),
        ),
      ),
```

## 结论

我们已经通过几个在应用程序中实现翻转效果的示例。