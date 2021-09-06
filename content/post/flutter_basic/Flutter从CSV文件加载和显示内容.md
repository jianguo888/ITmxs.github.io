---
title: "Flutter从CSV文件加载和显示内容"
date: 2021-09-06T17:08:52+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter","dart"]
categories: ["Flutter","dart"]
---

CSV 代表逗号分隔值，是一种广泛用于以纯文本形式存储表格数据（数字和文本）的文件类型，其中每行将具有相同数量的字段。在本文中，我们将探索如何在 Flutter 中从 CSV 文件中读取内容并将该内容显示在屏幕上。

以下是以下示例中使用的 CSV 文件。只需在深入代码之前下载它：

```
https://www.kindacode.com/wp-content/uploads/2021/01/kindacode.csv
```

这个文件里面的内容很简单，如下：

```dart
Id,Name,Age
1,John Doe,40
2,Kindacode,41
3,Voldermort,71
4,Joe Biden,80
5,Ryo Hanamura,35
```



## 完整示例

### **预览**

这个例子有一个浮动按钮。按下此按钮时，CSV 文件中的数据将被加载并显示在 ListView 中：

1.00

<video class="" autoplay="" controls="" loop="" muted="" data-lazy-type="video" data-src="https://www.kindacode.com/wp-content/uploads/2021/01/Flutter-CSV.mp4" playsinline="" src="https://www.kindacode.com/wp-content/uploads/2021/01/Flutter-CSV.mp4" __idm_id__="908313601" style="margin: 0px; padding: 0px; box-sizing: border-box; width: 720px;"></video>

### 步骤

\1. 将 CSV 文件复制到项目根目录下的**assets**文件夹（如果不存在则创建一个）。不要忘记在**pubspec.yaml 中**声明这个**资产**文件夹：

```dart
# The following section is specific to Flutter.
flutter:
  assets:
    - assets/
```

2.通过将以下内容添加到**pubspec.yaml**来安装[csv](https://pub.dev/packages/csv)包：

```dart
dependencies:
  csv: ^4.1.0
```

然后运行：

```dart
flutter pub get
```

\3. 将以下内容添加到您的**main.dart**：

```dart
import 'package:flutter/material.dart';
import 'package:flutter/services.dart' show rootBundle;

import 'package:csv/csv.dart';

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
  List<List<dynamic>> _data = [];

  // This function is triggered when the floating button is pressed
  void _loadCSV() async {
    final _rawData = await rootBundle.loadString("assets/kindacode.csv");
    List<List<dynamic>> _listData = CsvToListConverter().convert(_rawData);
    setState(() {
      _data = _listData;
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text("Kindacode.com"),
      ),
      body: ListView.builder(
        itemCount: _data.length,
        itemBuilder: (_, index) {
          return Card(
            margin: const EdgeInsets.all(3),
            color: index == 0 ? Colors.amber : Colors.white,
            child: ListTile(
              leading: Text(_data[index][0].toString()),
              title: Text(_data[index][1]),
              trailing: Text(_data[index][2].toString()),
            ),
          );
        },
      ),
      floatingActionButton:
          FloatingActionButton(child: Icon(Icons.add), onPressed: _loadCSV),
    );
  }
}
```



现在运行你的应用程序，你会看到它像上面的视频一样工作。

如果您更喜欢实现 Table 而不是 ListView，请查看本指南：[在 Flutter 中使用 Table](https://www.kindacode.com/article/working-with-table-in-flutter/)。

## 结论

在本文中，我们介绍了从 CSV 文件读取内容并将该内容显示给用户的完整示例。

