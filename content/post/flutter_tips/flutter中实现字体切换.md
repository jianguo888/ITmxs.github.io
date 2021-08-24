---
title: "Flutter中实现字体切换"
date: 2021-08-24T08:53:20+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["flutter"]
categories: ["flutter"]
---



## 在 Flutter 中更改文本的字体系列

将字体 .ttf 文件添加到应用程序的文件夹中。说。`assets/font/`



将资产和字体添加到 pubspec.yaml 文件中的 flutter 属性。您可以向应用程序添加一种或多种字体系列。在本教程中，我们将添加两种字体。

```clike
flutter:

  uses-material-design: true

  assets:
    - assets/font/

  fonts:
    - family: Font1
      fonts:
        - asset: assets/font/font1.ttf
    - family: Font2
      fonts:
        - asset: assets/font/font2.ttf
```



**main.dart**

```clike
import 'package:flutter/material.dart';

void main() {
  runApp(MaterialApp(
    home: MyApp(),
  ));
}

class MyApp extends StatefulWidget {
  @override
  _MyAppState createState() => _MyAppState();
}

class _MyAppState extends State<MyApp> {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: new Text("Flutter Tutorial - googleflutter.com"),
      ),
      body: Center(
          child: Column(children: <Widget>[
        Container(
          padding: EdgeInsets.all(20),
          child: Text(
            'Welcome to Flutter Tutorial by googleflutter.com',
            style: TextStyle(
                fontFamily: "Font1", fontSize: 40, fontWeight: FontWeight.bold),
          ),
        ),
        Container(
            padding: EdgeInsets.all(20),
            child: Text(
              'Welcome to Flutter Tutorial by googleflutter.com',
              style: TextStyle(
                  fontFamily: "Font2",
                  fontSize: 40,
                  fontWeight: FontWeight.bold),
            )),
      ])),
    );
  }
}
```

**截图**

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/image/flutter_change_font_family.png)

https://googleflutter.com/flutter-text-change-font-family/





Flutter 可以很方便的使用自定义字体，不仅能够将其用于整个应用里，还可以用在某个单独的 widget 中。请参照下面的步骤使用自定义字体：

## 







https://www.geeksforgeeks.org/customizing-fonts-in-flutter/

