---
title: "Flutter组件FadeTransition"
date: 2021-08-19T22:51:57+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
url: /xxx/xxx.html
---

> 本文主要介绍flutter组件FadeTransition

<!--more-->

伴随着令人惊叹的动画小部件排列，可为您的 flutter 应用程序添加运动和装饰。尽管如此，想象一下您需要一些真正基本的东西的场景。也许像使小部件褪色一样简单。Flutter SDK 为您提供了 FadeTransition 小部件。Flutter 有一个惊人的动画引擎，可以为您的 Flutter 应用程序添加运动和影响。然而，偶尔，您只需要完成一些简单的事情——比如在小部件中淡入淡出。

在本篇博客，我们将**探索 Flutter 中的 FadeTransition Widget**。 我们将看到如何实现淡入淡出过渡小部件的演示程序，以及如何`FadeTransition`在您的 Flutter 应用程序中使用小部件来为小部件的不透明度设置动画。

# 特性：

> *小部件的**一些属性*`*FadeTransition*`*是：*

- **>**`Key key` : 小部件的键，用于控制是否应替换。
- **>**`Animation<double> opacity` : 控制孩子淡入淡出过渡的动画。
- **>**`bool alwaysIncludeSemantics` : 是否一直包含孩子的语义信息。默认为`false`.
- **>**`Widget child`：树中此小部件下的小部件，将在其中应用动画



代码

```dart
import 'package:flutter/material.dart';

class FadeTransitionDemo extends StatefulWidget {
  _FadeTransitionDemoState createState() => _FadeTransitionDemoState();
}

class _FadeTransitionDemoState extends State<FadeTransitionDemo>
    with TickerProviderStateMixin {

  AnimationController _controller;
  Animation<double> _animation;

  initState() {
    super.initState();

    _controller = AnimationController(
      duration: const Duration(seconds: 3),
      vsync: this,

    )..repeat(reverse:true);
    _animation = CurvedAnimation(
        parent: _controller,
        curve: Curves.easeIn
    );
  }

  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      backgroundColor: Color(0xffffffff),
      appBar: AppBar(
        automaticallyImplyLeading: false,
        title: Text( 'Flutter FadeTransition Widget Demo',),
      ),
      body: Center(
        child: FadeTransition(
          opacity: _animation,
          child: Column(
            mainAxisAlignment: MainAxisAlignment.center,
            children: [
              Text("Flutter Dev's",style: TextStyle(
                  fontSize: 20,
                  fontWeight: FontWeight.bold),
              ),
              Image.asset("assets/devs.jpg"),
            ],
          ),
        ),
      ),
    );
  }
}
```

