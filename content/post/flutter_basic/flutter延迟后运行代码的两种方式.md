---
title: "Flutter延迟后运行代码的两种方式"
date: 2021-09-06T16:03:09+08:00
draft: true
---

本文演示了在 Flutter 中延迟后执行一段代码的 2 种不同方式。第一种方法是使用**Future.delayed**，第二种方法是使用**计时器**。闲话少说，让我们动手编写一些代码

## 使用 Future.delayed

**快速示例：**

```
Future.delayed(Duration(milliseconds: 1500), () {
    print('Hello');
});
```

### **完整示例**

这个演示应用程序在按下按钮 2 秒后显示一个问候对话框。

**预览：**

1.00

<video class="" autoplay="" controls="" loop="" muted="" data-lazy-type="video" data-src="https://www.kindacode.com/wp-content/uploads/2021/08/Flutter-Future.delayed.mp4" playsinline="" src="https://www.kindacode.com/wp-content/uploads/2021/08/Flutter-Future.delayed.mp4" __idm_id__="771976193" style="margin: 0px; padding: 0px; box-sizing: border-box; width: 720px;"></video>

**完整的源代码：**

```
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

class _HomePageState extends State<HomePage> {
  void _showDialog() {
    Future.delayed(Duration(seconds: 2), () {
      showDialog(
          context: context,
          builder: (_) => SimpleDialog(
                title: Text('Have a nice day'),
                children: [Text('Happy coding with Flutter')],
                contentPadding: EdgeInsets.all(25),
              ));
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Kindacode.com'),
      ),
      body: Center(
        child: ElevatedButton(
          onPressed: _showDialog,
          child: Text('Show the Dialog'),
        ),
      ),
    );
  }
}
```

## 使用定时器

最小的例子：

```
@override
void initState() {
    super.initState();
    Timer(Duration(seconds: 5), () {
      print('Hello');
    });
  }
```

### 完整示例

**预览**

一开始，文本小部件显示“请稍候……”。5 秒后，您将看到“一切就绪”。



<video class="" autoplay="" controls="" loop="" muted="" data-lazy-type="video" data-src="https://www.kindacode.com/wp-content/uploads/2021/08/Flutter-dealy-2.mp4" playsinline="" src="https://www.kindacode.com/wp-content/uploads/2021/08/Flutter-dealy-2.mp4" __idm_id__="771976194" style="margin: 0px; padding: 0px; box-sizing: border-box; width: 720px;"></video>



**编码：**

```
// main.dart
import 'package:flutter/material.dart';
import 'dart:async';

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

class _HomePageState extends State<HomePage> {
  String _text = 'Please wait...';

  @override
  void initState() {
    super.initState();
    Timer(Duration(seconds: 5), () {
      setState(() {
        _text = 'Everything is ready';
      });
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Kindacode.com'),
      ),
      body: Center(
        child: Text(
          _text,
          style: TextStyle(fontSize: 30),
        ),
      ),
    );
  }
}
```

您可以在[本文中](https://www.kindacode.com/article/flutter-timer/)查看有关 Timer 的详细信息。
