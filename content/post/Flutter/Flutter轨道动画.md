---
title: "Flutter轨道动画"
date: 2021-08-16T17:21:54+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---

在本文中，我们将学习如何重新创建此动画。这是一个基本的动画，主要使用这两个小部件，**Stack**和**RotationTransition。**

在我继续之前，我假设您熟悉 Flutter 的基础知识，并且对 Flutter UI 布局的工作原理有一个很好的了解。但是，让我们来看看使用的这些小部件是什么以及如何使用它们。

**栈**：这个小部件位置我牛逼的孩子相对于它的框的边缘。如果您想以简单的方式重叠多个子项，例如，将一些文本和图像叠加在渐变和底部的按钮上，则 Stack 类很有用。您可以[***在此处\***](https://api.flutter.dev/flutter/widgets/Stack-class.html)了解有关**Stack**小部件的更多信息***。\***

**RotationTransition：**此小部件为其“子”小部件的旋转设置动画。

在这里和那里使用小部件和一些代码，我们创建了上面的动画

```dart
import 'package:flutter/material.dart';

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      home: Scaffold(
        body: Center(
          child: SizedBox(width: 250, height: 250, child: Orbit()),
        ),
      ),
    );
  }
}

class Orbit extends StatefulWidget {
  @override
  _Orbit createState() => _Orbit();
}

class _Orbit extends State<Orbit>
    with SingleTickerProviderStateMixin {
  AnimationController controller;

  @override
  void initState() {
    super.initState();
    controller = AnimationController(vsync: this);

    controller.repeat(min: 0.0, max: 1.0, period: const Duration(seconds: 3));
  }

  @override
  Widget build(BuildContext context) {
    return Stack(
      alignment: Alignment.center,
      children: <Widget>[
        RotationTransition(
          turns: controller,
          child: Align(
            alignment: Alignment.topCenter,
            child: Container(
              decoration: const BoxDecoration(
                borderRadius: BorderRadius.all(Radius.circular(30)),
                color: Colors.green,
              ),
              height: 50.0,
              width: 50.0,
            ),
          ),
        ),
        Container(
              width: 215,
              height: 215,
              decoration: BoxDecoration(color: const Color(0xFF0C7D0D).withOpacity(0.2), borderRadius: const BorderRadius.all(Radius.circular(120))),
            ),
      ],
    );
  }
}
```

这两个小部件的组合可用于创建更令人印象深刻的动画设计，本文只是让我们深入了解如何以最原始的形式使用它。
