---
title: "在flutter中使用stream"
date: 2021-10-05T13:33:10+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---

> 本文将带您**了解**在 Flutter中使用**Stream.periodic**的完整示例。

该**Stream.periodic**构造，顾名思义，是用来创建流，在周期间隔反复广播事件。简单用法：广告

```dart
final Stream _myStream =
    Stream.periodic(const Duration(seconds: 1), (int count) {
       // Do something and return something here
});
```

您可以在[文档中](https://api.flutter.dev/flutter/dart-async/Stream/Stream.periodic.html)找到有关**Stream.periodic 的**更多信息。但是，如果您觉得单词太无聊和令人困惑，并且只想深入研究代码，请继续阅读下面的示例。

## 这个例子

### 预览

我们将要构建的应用程序的背景颜色会随着时间而变化。它还在屏幕中央显示递增的数字。我们可以通过按下浮动按钮来阻止这些无情的行为。

这是它的工作原理：



<video class="" autoplay="" controls="" loop="" muted="" data-lazy-type="video" data-src="https://www.kindacode.com/wp-content/uploads/2021/10/Flutter-Stream-periodic.mp4" playsinline="" src="https://www.kindacode.com/wp-content/uploads/2021/10/Flutter-Stream-periodic.mp4" style="margin: 0px; padding: 0px; box-sizing: border-box; width: 720px;"></video>

### 编码

**main.dart 中**的完整源代码和解释：



```dart
import 'package:flutter/material.dart';
import 'dart:async';
import 'dart:math';

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
      title: 'KindaCode.com',
      theme: ThemeData(
        primarySwatch: Colors.indigo,
      ),
      home: const HomeScreen(),
    );
  }
}

class HomeScreen extends StatefulWidget {
  const HomeScreen({Key? key}) : super(key: key);

  @override
  State<HomeScreen> createState() => _HomeScreenState();
}

class _HomeScreenState extends State<HomeScreen> {
  final Stream _myStream =
      Stream.periodic(const Duration(seconds: 1), (int count) {
    return count;
  });

  // The subscription on events from _myStream
  late StreamSubscription _sub;

  // This number will be displayed in the center of the screen
  // It changes over time
  int _computationCount = 0;

  // Background color
  // In the beginning, it's indigo but it will be a random color later
  Color _bgColor = Colors.indigo;

  @override
  void initState() {
    _sub = _myStream.listen((event) {
      setState(() {
        _computationCount = event;

        // Set the background color to a random color
        _bgColor = Colors.primaries[Random().nextInt(Colors.primaries.length)];
      });
    });
    super.initState();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      backgroundColor: _bgColor,
      appBar: AppBar(
        title: const Text('KindaCode.com'),
        backgroundColor: Colors.transparent,
      ),
      body: Center(
        child: Text(
          _computationCount.toString(),
          style: const TextStyle(fontSize: 150, color: Colors.white),
        ),
      ),
      // This button is used to unsubscribe the stream listener
      floatingActionButton: FloatingActionButton(
        child: const Icon(
          Icons.stop,
          size: 30,
        ),
        onPressed: () => _sub.cancel(),
      ),
    );
  }

  // Cancel the stream listener on dispose
  @override
  void dispose() {
    _sub.cancel();
    super.dispose();
  }
}
```

## 结论

我们已经研究了在 Flutter中实现**Stream.periodic**的实际示例。

