---
title: "在Flutter中使用Timer和Timer"
date: 2021-09-06T16:05:46+08:00
draft: true
---

在本文中，我们将了解Flutter 中**Timer**类的基础知识，并介绍在应用程序中使用它的几个不同示例。

## 概述

在使用 TImer 类之前，需要导入**dart:async**库：

```
import 'dart:async';
```

要在指定的时间段后安排代码执行，我们使用**Timer**构造函数，如下所示：

```
Timer(Duration duration, void callback());
```

如果有一段代码应该以指定的时间间隔执行多次，则可以使用**Timer.periodic**构造函数来执行该代码：

```
Timer.periodic(Duration duration, void callback(Timer timer))
```

请注意，您可以使用**cancel()**方法取消任何计时器。

为了更清楚，请参阅下面的示例。

## **示例**1：定时器

此示例 Flutter 应用程序在屏幕中央包含一个按钮。用户按下该按钮 3 秒钟后，将显示一个蓝色框。

### **预览**



<video class="" autoplay="" controls="" loop="" muted="" data-lazy-type="video" data-src="https://www.kindacode.com/wp-content/uploads/2020/11/Flutter-Timer-1.mp4" src="https://www.kindacode.com/wp-content/uploads/2020/11/Flutter-Timer-1.mp4" __idm_id__="695780353" style="margin: 0px; padding: 0px; box-sizing: border-box; width: 720px;"></video>

### 完整代码

```dart
import 'package:flutter/material.dart';
import 'dart:async';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      // Hide the debug banner
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

class _HomePageState extends State<HomePage> {
  // If the user presses the button, _isButtonPressed is set to true
  // Then the button will be disable
  bool _isButtonPressed = false; 

  // show a blue box when _showBox == true 
  bool _showBox = false;

  void _startTimer() {
    // Disable the button after it has been pressed
    setState(() {
      _isButtonPressed = true;
    });

    Timer(Duration(seconds: 3), () {
      setState(() {
        _showBox = true;
      });
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
        body: SafeArea(
      child: SizedBox(
        width: double.infinity,
        child: Column(
            mainAxisAlignment: MainAxisAlignment.center,
            crossAxisAlignment: CrossAxisAlignment.center,
            children: [
              ElevatedButton(
                onPressed: _isButtonPressed == false ? _startTimer : null,
                child: Text('Show The Blue Box'),
              ),
              _showBox == true
                  ? Container(
                      width: 300,
                      height: 300,
                      color: Colors.blue,
                    )
                  : Text(
                      'Press the button and wait for 3 seconds to see the box')
            ]),
      ),
    ));
  }
}
```

## 示例 2：Timer.periodic

此示例应用程序包含一个列表视图。每过一秒，就会有一个新元素添加到列表中。要停止此过程，用户可以按下浮动按钮。

### 预览

1.00

<video class="" autoplay="" controls="" loop="" muted="" data-lazy-type="video" data-src="https://www.kindacode.com/wp-content/uploads/2020/11/Flutter-Timer.pediodic.mp4" playsinline="" src="https://www.kindacode.com/wp-content/uploads/2020/11/Flutter-Timer.pediodic.mp4" __idm_id__="695780354" style="margin: 0px; padding: 0px; box-sizing: border-box; width: 720px;"></video>

### 完整代码

```dart
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
        // Hide the debug banner
        debugShowCheckedModeBanner: false,
        title: 'Kindacode.com',
        home: HomeScreen());
  }
}

class HomeScreen extends StatefulWidget {
  const HomeScreen({Key? key}) : super(key: key);

  @override
  _HomeScreenState createState() => _HomeScreenState();
}

class _HomeScreenState extends State<HomeScreen> {
  // This list hold the items of the list view
  List<String> _items = [];

  // This variable determines whether the timer runs or not
  bool _isRunning = true;

  // This function will be triggered every 1 second
  void _addItem() {
    final DateTime now = DateTime.now();
    setState(() {
      _items.add("${now.hour}:${now.minute}:${now.second}");
    });
  }

  @override
  void initState() {
    Timer.periodic(Duration(seconds: 1), (Timer timer) {
      if (!_isRunning) {
        timer.cancel();
      }
      _addItem();
    });
    super.initState();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Kindacode.com'),
      ),
      body: ListView.builder(
        itemCount: _items.length,
        itemBuilder: (_, index) {
          return Card(
            margin: EdgeInsets.all(10),
            color: Colors.amber,
            elevation: 5,
            child: ListTile(
              title: Text(_items[index]),
            ),
          );
        },
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: () {
          setState(() {
            _isRunning = false;
          });
        },
        child: Icon(Icons.stop_circle),
      ),
    );
  }
}
```

## 结论



您已经学习了如何使用 Timer 在指定的时间后或以指定的时间间隔重复触发回调函数
