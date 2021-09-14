---
title: "Flutter_TweenAnimationBuilder示例"
date: 2021-09-14T17:09:58+08:00
draft: false
tags: ["Flutter"]
categories: ["Flutter"]
---



在 Flutter 中实现 TweenAnimationBuilder 的几个例子。



## 制造一颗**脉冲**星



### 应用预览



<video class="" autoplay="" controls="" loop="" muted="" data-lazy-type="video" data-src="https://www.kindacode.com/wp-content/uploads/2020/11/Flutter-pulsing-star.mp4" src="https://www.kindacode.com/wp-content/uploads/2020/11/Flutter-pulsing-star.mp4" __idm_id__="204010497" style="margin: 0px; padding: 0px; box-sizing: border-box; width: 720px;"></video>

### **完整的代码**

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
  double _targetSize = 300;

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Container(
        alignment: Alignment.center,
        color: Colors.purple[900],
        child: TweenAnimationBuilder(
          tween: Tween<double>(
            begin: 50,
            end: _targetSize,
          ),
          duration: Duration(seconds: 2),
          onEnd: () {
            setState(() {
              if (_targetSize == 50) {
                _targetSize = 300;
              } else {
                _targetSize = 50;
              }
            });
          },
          curve: Curves.linear,
          builder: (BuildContext _, double size, Widget? __) {
            return Container(
              width: size,
              height: size,
              decoration: BoxDecoration(
                  gradient: RadialGradient(
                      center: Alignment.center,
                      colors: [Colors.yellow, Colors.red]),
                  shape: BoxShape.circle),
            );
          },
        ),
      ),
    );
  }
}
```

## **创建弹性盒**

此示例应用程序包含一个浮动按钮和 2 个框。当用户按下按钮时，橙色框的宽度在 100 和 300 之间切换。





### **应用预览**



<video class="" autoplay="" controls="" loop="" muted="" data-lazy-type="video" data-src="https://www.kindacode.com/wp-content/uploads/2020/11/Flutter-TweenAnimationBuilder.mp4" src="https://www.kindacode.com/wp-content/uploads/2020/11/Flutter-TweenAnimationBuilder.mp4" __idm_id__="204010498" style="margin: 0px; padding: 0px; box-sizing: border-box; width: 720px;"></video>

### **完整代码**

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
  double _targetValue = 100;

  void _changeSize() {
    setState(() {
      if (_targetValue == 100) {
        _targetValue = 300;
      } else {
        _targetValue = 100;
      }
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Kindacode.com'),
      ),
      body: Center(
        child: TweenAnimationBuilder(
          tween: Tween<double>(
            begin: 100,
            end: _targetValue,
          ),
          duration: Duration(seconds: 1),
          curve: Curves.linear,
          builder: (BuildContext _, double value, Widget? __) {
            return Container(
              width: value,
              height: 300,
              color: Colors.amber,
              alignment: Alignment.center,
            );
          },
          child: Container(
            width: 50,
            height: 50,
            color: Colors.purple,
          ),
        ),
      ),
      floatingActionButton: ElevatedButton.icon(
          onPressed: _changeSize,
          icon: Icon(Icons.refresh),
          label: Text('Swich Size')),
    );
  }
}
```

## 总结

我们已经完成了几个使用 TweenAnimationBuilder 制作很酷的动画的例子。
