---
title: "Flutter如何读写文本文件"
date: 2021-09-06T17:05:31+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter","dart"]
categories: ["Flutter","dart"]
---

这篇文章是关于在 Flutter 中读写文本文件的。

## 介绍

文本文件（具有**.txt**扩展名）广泛用于持久存储信息，从数字数据到长文本。今天，我们将介绍 2 个使用此文件类型的 Flutter 应用程序示例。

第一个示例快速而简单。它仅使用 rootBundle（来自 services.dart）从 assets 文件夹（或根项目中的另一个文件夹）中的文本加载内容，然后将结果输出到屏幕上。当您只需要读取数据而不需要写入数据时，这很有用。

第二个例子稍微复杂一点。它不仅可以读取用户输入的内容，还可以将用户输入的内容写入文本文件。您将学习如何使用**File 异步方法，**包括**readAsString**和**writeAsString**。

## 示例 1：加载内容

### **预览**

此示例包含一个文本小部件和一个浮动按钮。当这个按钮被按下时，函数**_loadData**将被触发并从文件中加载内容。



<video class="" autoplay="" controls="" loop="" muted="" data-lazy-type="video" data-src="https://www.kindacode.com/wp-content/uploads/2021/01/Flutter-reading-text-files.mp4" playsinline="" src="https://www.kindacode.com/wp-content/uploads/2021/01/Flutter-reading-text-files.mp4" __idm_id__="783505409" style="margin: 0px; padding: 0px; box-sizing: border-box; width: 720px;"></video>

**将文本文件添加到您的项目中**

在项目根目录的**资产**文件夹中创建一个名为**data.txt**的新文本文件（如果尚不存在，则创建一个），然后向其中添加一些虚拟内容，如下所示：

```dart
Lorem ipsum dolor sit amet, consectetur adipiscing elit. In at lacus et mi blandit iaculis sed eget elit. Nulla facilisi. Sed pulvinar erat vel odio pretium, id malesuada tortor sollicitudin.
```

不要忘记在**pubspec.yaml**文件中注册**资产**文件夹：

```dart
flutter:
  assets:
    - assets/
```

### **完整代码**

广告

将以下内容添加到您的**main.dart**：

```dart
// main.dart
import 'package:flutter/material.dart';
import 'package:flutter/services.dart' show rootBundle;
import 'dart:async';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
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
  String _data;

  // This function is triggered when the user presses the floating button
  Future<void> _loadData() async {
    final _loadedData = await rootBundle.loadString('assets/data.txt');
    setState(() {
      _data = _loadedData;
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Kindacode.com'),
      ),
      body: Center(
          child: Container(
              width: 300,
              child: Text(_data != null ? _data : 'Nothing to show',
                  style: TextStyle(fontSize: 24)))),
      floatingActionButton:
          FloatingActionButton(onPressed: _loadData, child: Icon(Icons.add)),
    );
  }
}
```

## 示例 2：阅读和写作

### 获取文件路径

出于安全原因，Android 和 iOS 不允许我们在硬盘驱动器上的任何位置进行读写。我们需要将文本文件保存到**Documents**目录中，应用程序只能在该目录中访问其文件。只有在删除应用程序时才会删除这些文件。

该**文件**目录是**NSDocumentDirectory** iOS和**应用程序数据**在Android上。要获取该目录的完整路径，我们使用**[path_provider](https://pub.dev/packages/path_provider)**包（这是 Flutter 的官方包）。

通过将**path_provider**及其版本添加到**pubspec.yaml**文件的**依赖项**部分来安装包，如下所示：

```dart
dependencies:
  path_provider: ^1.6.27
```

然后运行以下命令：

```dart
flutter pub get
```

广告

并找到如下路径：

```dart
import 'package:path_provider/path_provider.dart';

/* .... */

Future<String> get _getDirPath async {
  final _dir = await getApplicationDocumentsDirectory();
  return _dir.path;
}
```

### **示例预览**

此示例应用程序有一个 TextFiled，允许用户输入他/她的姓名以写入文本文件。它还包含一个文本小部件，显示从该文件读取的名称。

1.00

<video class="" autoplay="" controls="" loop="" muted="" data-lazy-type="video" data-src="https://www.kindacode.com/wp-content/uploads/2021/01/Flutter-write-to-text-file.mp4" playsinline="" src="https://www.kindacode.com/wp-content/uploads/2021/01/Flutter-write-to-text-file.mp4" __idm_id__="783505410" style="margin: 0px; padding: 0px; box-sizing: border-box; width: 720px;"></video>

### **完整代码和解释**

在此示例中，我们不需要手动创建文本文件并将其添加到项目中。第一次写入数据时会自动创建。

这是我们的**main.dart 中**的代码：

```dart
// main.dart
import 'dart:convert';

import 'package:flutter/material.dart';
import 'dart:async';
import 'dart:io';
import 'package:path_provider/path_provider.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
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
  // This will be displayed on the screen
  String _content;

  // Find the Documents path
  Future<String> _getDirPath() async {
    final _dir = await getApplicationDocumentsDirectory();
    return _dir.path;
  }

  // This function is triggered when the "Read" button is pressed
  Future<void> _readData() async {
    final _dirPath = await _getDirPath();
    final _myFile = File('$_dirPath/data.txt');
    final _data = await _myFile.readAsString(encoding: utf8);
    setState(() {
      _content = _data;
    });
  }

  // TextField controller
  final _textController = TextEditingController();
  // This function is triggered when the "Write" buttion is pressed
  Future<void> _writeData() async {
    final _dirPath = await _getDirPath();
    
    final _myFile = File('$_dirPath/data.txt');
    // If data.txt doesn't exist, it will be created automatically

    await _myFile.writeAsString(_textController.text);
    _textController.clear();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Kindacode.com'),
      ),
      body: Padding(
        padding: const EdgeInsets.all(20),
        child: Column(
          children: [
            TextField(
              controller: _textController,
              decoration: InputDecoration(labelText: 'Enter your name'),
            ),
            ElevatedButton(
              child: Text('Save to file'),
              onPressed: _writeData,
            ),
            SizedBox(
              height: 150,
            ),
            Text(
                _content != null
                    ? _content
                    : 'Press the button to load your name',
                style: TextStyle(fontSize: 24, color: Colors.pink)),
            ElevatedButton(
              child: Text('Read my name from the file'),
              onPressed: _readData,
            )
          ],
        ),
      ),
    );
  }
}
```

## 结论

通过本文，您构建了 2 个简单的应用程序，并了解了读写文本文件的基本知识。
