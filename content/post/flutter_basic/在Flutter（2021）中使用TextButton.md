---
title: "在Flutter（2021）中使用TextButton"
date: 2021-09-06T16:51:04+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter","dart"]
categories: ["Flutter","dart"]
---



按钮是每个 Flutter Web 和移动应用程序的重要组成部分。在本文中，我们将看看 TextButton 类。您将学习如何实现文本按钮，以及如何禁用它们。我们还将介绍我们可以做些什么来设置文本按钮的样式和一些示例，以便更好地理解。

## 什么是文本按钮

TextButton 小部件只是显示在零高度材质小部件上的文本标签。默认情况下，它没有可见的边框，并通过填充背景颜色来响应触摸。

**TEXT按钮**是替代**FlatButton**这曾经是一个非常受欢迎的组件，但是现在已经过时了。

要使用**TextButton**消除烦人的警告或错误，您应该使用 Flutter 1.22.0 或更新版本。

## 文本按钮构造函数

### **文本按钮**

```dart
TextButton({
  Key key, 
  @required VoidCallback onPressed, 
  VoidCallback onLongPress, 
  ButtonStyle style, 
  FocusNode focusNode, 
  bool autofocus: false, 
  Clip clipBehavior: Clip.none, 
  @required Widget child
})
```

例子：

```dart
TextButton(
            onPressed: () {},
            child: Text('Simple Button'),
          ),
```

截屏：

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/image/Screen-Shot-2020-12-13-at-14.13.06.jpg)

### **带图标的文本按钮**

```dart
TextButton.icon({
  Key key, 
  @required VoidCallback onPressed, 
  VoidCallback onLongPress, 
  ButtonStyle style, 
  FocusNode focusNode, 
  bool autofocus, 
  Clip clipBehavior, 
  @required Widget icon, 
  @required Widget label
})
```

例子：

```dart
TextButton.icon(
            icon: Icon(Icons.camera),
            label: Text('Take A Photo'),
            onPressed: () {},
          )
```

截屏：

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/image/Screen-Shot-2020-12-13-at-14.13.48.jpg)

## 如何禁用文本按钮

 

禁用按钮是对触摸没有反应的按钮。要禁用文本按钮，只需将**onPressed**和**onLongPress**设置为**null**（默认情况下**onLongPress**为 null）。

例子：

```dart
Column(
            children: [
              TextButton(
                onPressed: null,
                child: Text('Disabled Button'),
              ),
              TextButton(
                onPressed: () {},
                child: Text('Enabled Button'),
              ),
              TextButton.icon(
                  onPressed: null,
                  icon: Icon(Icons.room_rounded),
                  label: Text('Disabled Icon Button')),
              TextButton.icon(
                  onPressed: () {},
                  icon: Icon(Icons.room_rounded),
                  label: Text('Enabled Icon Button'))
            ],
          ),
```

截屏：

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/image/Screen-Shot-2020-12-13-at-14.25.51.jpg)

## 如何设置 TextButton 的样式

您可以使用 TextButton.styleFrom 静态方法或使用 ButtonStyle 类来设置文本按钮的样式。第一种方法比第二种方法更方便。

### **使用 TextButton.styleFrom**

所有可用选项：

```dart
styleFrom({
  Color? primary, 
  Color? onSurface, 
  Color? backgroundColor, 
  Color? shadowColor, 
  double? elevation, 
  TextStyle? textStyle, 
  EdgeInsetsGeometry? padding, 
  Size? minimumSize, 
  Size? fixedSize, 
  BorderSide? side, 
  OutlinedBorder? shape, 
  MouseCursor? enabledMouseCursor, 
  MouseCursor? disabledMouseCursor, 
  VisualDensity? visualDensity, 
  MaterialTapTargetSize? tapTargetSize, 
  Duration? animationDuration, 
  bool? enableFeedback, 
  AlignmentGeometry? alignment, 
  InteractiveInkFeatureFactory? splashFactory
})
```

 

例子：

```
TextButton(
            onPressed: () {},
            child: Text(
              'Text Button',
            ),
            style: TextButton.styleFrom(
                primary: Colors.purple,
                backgroundColor: Colors.amber,
                textStyle: TextStyle(fontSize: 24, fontStyle: FontStyle.italic)),
          ),
```

截屏：

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/image/Screen-Shot-2020-12-13-at-14.49.46.jpg)

### **使用按钮样式**

参数：

```dart
ButtonStyle({
  MaterialStateProperty<TextStyle?>? textStyle, 
  MaterialStateProperty<Color?>? backgroundColor, 
  MaterialStateProperty<Color?>? foregroundColor, 
  MaterialStateProperty<Color?>? overlayColor, 
  MaterialStateProperty<Color?>? shadowColor, 
  MaterialStateProperty<double?>? elevation, 
  MaterialStateProperty<EdgeInsetsGeometry?>? padding, 
  MaterialStateProperty<Size?>? minimumSize, 
  MaterialStateProperty<Size?>? fixedSize, 
  MaterialStateProperty<BorderSide?>? side, 
  MaterialStateProperty<OutlinedBorder?>? shape, 
  MaterialStateProperty<MouseCursor?>? mouseCursor, 
  VisualDensity? visualDensity, 
  MaterialTapTargetSize? tapTargetSize, 
  Duration? animationDuration, bool? enableFeedback, 
  AlignmentGeometry? alignment, 
  InteractiveInkFeatureFactory? splashFactory
})
```

例子：

```dart
TextButton(
              onPressed: () {},
              child: Text(
                'Text Button',
              ),
              style: ButtonStyle(
                  side: MaterialStateProperty.all(
                      BorderSide(width: 2, color: Colors.red)),
                  foregroundColor: MaterialStateProperty.all(Colors.purple),
                  padding: MaterialStateProperty.all(
                      EdgeInsets.symmetric(vertical: 10, horizontal: 50)),
                  textStyle:
                      MaterialStateProperty.all(TextStyle(fontSize: 30))
              )
),
```

截屏：

![img](https://www.kindacode.com/wp-content/uploads/2020/10/Screen-Shot-2020-12-13-at-14.55.24.jpg)

### 给孩子造型

 

如果您只想设置文本样式，您可以简单地这样做：

```dart
TextButton(
            onPressed: () {},
            child: Text(
              'Text Button',
              style: TextStyle(fontSize: 30, color: Colors.red),
            ),
          ),
```

截屏：

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/image/Screen-Shot-2020-12-13-at-14.45.31.jpg)

## 文本按钮主题

使用主题可以让您一次设置多个文本按钮的样式。

例子：

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
            textButtonTheme: TextButtonThemeData(
                style: TextButton.styleFrom(
                    primary: Colors.red,
                    textStyle: TextStyle(
                        fontSize: 24,
                        fontWeight: FontWeight.bold,
                        fontStyle: FontStyle.italic)))),
        home: HomePage());
  }
}

class HomePage extends StatelessWidget {
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Kindacode.com'),
      ),
      body: Padding(
        padding: EdgeInsets.all(20),
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: [
            TextButton(onPressed: () {}, child: Text('Text Button 1')),
            TextButton.icon(
                onPressed: () {},
                icon: Icon(Icons.camera),
                label: Text('Text Button 2'))
          ],
        ),
      ),
    );
  }
}
```

输出：

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/image/Screen-Shot-2021-08-13-at-17.36.59.jpg)

## 再举一个例子

**./lib/main.dart 中**的代码：

```dart
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
      home: MyHomePage(),
    );
  }
}

class MyHomePage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('TextButton example'),
        backgroundColor: Colors.black,
      ),
      body: Padding(
        padding: EdgeInsets.all(20),
        child: Column(children: [
          /// Text Button #1
          TextButton(
              child: Text(
                'Button #1',
              ),
              onPressed: () {
                print('Button #1 is clicked!');
              }),

          /// Text Button #2
          TextButton(
            child: Text('Button #2',
                style: TextStyle(
                    color: Colors.red,
                    fontSize: 30,
                    fontWeight: FontWeight.bold)),
            onPressed: () {
              print('Button #2 is clicked');
            },
            onLongPress: () {
              print('Button #2 is long clicked!');
            },
          )
        ]),
      ),
    );
  }
}
```

Android 和 iOS 上的结果：

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/image/Screen-Shot-2020-10-08-at-16.36.36.jpg)

## 结论

本文介绍了在 Flutter 中使用文本按钮的最重要方面。你应该避免使用文本按钮，因为它们看起来像一个链接。
