---
title: "在Flutter中从API获取数据的2种方法"
date: 2021-09-06T16:54:53+08:00
draft: true
---

本文向您展示了从 Flutter 中的 API 获取数据的几种不同方法。第一种方法是使用**HttpClient的**类和第二个是使用[**HTTP**插件](https://pub.dev/packages/http)从**pub.dev**。


![img](https://luckly007.oss-cn-beijing.aliyuncs.com/image/Screen-Shot-2020-11-10-at-14.35.47.jpg)

出于演示目的，我们将使用一个包含 5000 张照片信息的开放 API 端点（感谢 Typicode 团队为此）：

```dart
https://jsonplaceholder.typicode.com/photos
```

单张照片的信息如下所示：

```dart
{
    "albumId": 1,
    "id": 6
    "title": "accusamus ea aliquid et amet sequi nemo",
    "url": "https://via.placeholder.com/600/56a8c2",
    "thumbnailUrl": "https://via.placeholder.com/150/56a8c2"
}
```

在向 API 发送 GET 请求并获得响应后，我们将使用 ListView 显示照片。

## **1. 使用HttpClient**

如果你不喜欢在你的应用程序中使用这么多插件，试试这个方法。

### 示例预览



<video class="" autoplay="" controls="" loop="" muted="" data-lazy-type="video" data-src="https://www.kindacode.com/wp-content/uploads/2020/11/Flutter-http-client.mp4" src="https://www.kindacode.com/wp-content/uploads/2020/11/Flutter-http-client.mp4" __idm_id__="1056611329" style="margin: 0px; padding: 0px; box-sizing: border-box; width: 720px;"></video>

从 API 获取数据的函数：

```dart
Future<void> _fetchData() async {
    const API_URL = 'https://jsonplaceholder.typicode.com/photos';

    HttpClient client = new HttpClient();
    client.autoUncompress = true;

    final HttpClientRequest request = await client.getUrl(Uri.parse(API_URL));
    request.headers
        .set(HttpHeaders.contentTypeHeader, "application/json; charset=UTF-8");
    final HttpClientResponse response = await request.close();

    final String content = await response.transform(utf8.decoder).join();
    final List data = json.decode(content);

    setState(() {
      /* */
    });
}
```

### 完整代码

```dart
// main.dart
import 'package:flutter/material.dart';
import 'dart:io'; // for using HttpClient
import 'dart:convert'; // for using json.decode()

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
  // The list that contains information about photos
  List _loadedPhotos = [];

  // The function that fetches data from the API
  Future<void> _fetchData() async {
    const API_URL = 'https://jsonplaceholder.typicode.com/photos';

    HttpClient client = new HttpClient();
    client.autoUncompress = true;

    final HttpClientRequest request = await client.getUrl(Uri.parse(API_URL));
    request.headers
        .set(HttpHeaders.contentTypeHeader, "application/json; charset=UTF-8");
    final HttpClientResponse response = await request.close();

    final String content = await response.transform(utf8.decoder).join();
    final List data = json.decode(content);

    setState(() {
      _loadedPhotos = data;
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
        appBar: AppBar(
          title: Text('Kindacode.com'),
        ),
        body: SafeArea(
            child: _loadedPhotos.length == 0
                ? Center(
                    child: ElevatedButton(
                      child: Text('Load Photos'),
                      onPressed: _fetchData,
                    ),
                  )
                // The ListView that displays photos
                : ListView.builder(
                    itemCount: _loadedPhotos.length,
                    itemBuilder: (BuildContext ctx, index) {
                      return ListTile(
                        leading: Image.network(
                          _loadedPhotos[index]["thumbnailUrl"],
                          width: 150,
                          fit: BoxFit.cover,
                        ),
                        title: Text(_loadedPhotos[index]['title']),
                        subtitle:
                            Text('Photo ID: ${_loadedPhotos[index]["id"]}'),
                      );
                    },
                  )));
  }
}
```

## **2. 使用插件**

如果您想编写更少的代码，请尝试这种方法。

### **安装**

通过将 http: **^0.13.3**添加到**pubspec.yaml**文件的**依赖项**部分来安装**http**插件，如下所示：

```dart
dependencies: 
  flutter:
    sdk: flutter
  http: ^0.13.3
```



**注意**：您阅读本文时插件的版本可能有所不同。

运行以下命令以完成安装过程：

```
flutter pub get
```

将插件导入代码中：

```dart
import 'package:http/http.dart' as http; 
```

### **获取数据功能**

```dart
Future<void> _fetchData() async {
    const API_URL = 'https://jsonplaceholder.typicode.com/photos';

    final response = await http.get(Uri.parse(API_URL));
    final data = json.decode(response.body);

    setState(() {
      _loadedPhotos = data;
    });
  }
```

它比第一种方法要短得多，不是吗？

### **完整代码**

```dart
// main.dart
import 'package:flutter/material.dart';
import 'package:http/http.dart' as http;
import 'dart:convert'; // for using json.decode()

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
  // The list that contains information about photos
  List _loadedPhotos = [];

  // The function that fetches data from the API
  Future<void> _fetchData() async {
    const API_URL = 'https://jsonplaceholder.typicode.com/photos';

    final response = await http.get(Uri.parse(API_URL));
    final data = json.decode(response.body);

    setState(() {
      _loadedPhotos = data;
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
        appBar: AppBar(
          title: Text('Kindacode.com'),
        ),
        body: SafeArea(
            child: _loadedPhotos.length == 0
                ? Center(
                    child: ElevatedButton(
                      child: Text('Load Photos'),
                      onPressed: _fetchData,
                    ),
                  )
                // The ListView that displays photos
                : ListView.builder(
                    itemCount: _loadedPhotos.length,
                    itemBuilder: (BuildContext ctx, index) {
                      return ListTile(
                        leading: Image.network(
                          _loadedPhotos[index]["thumbnailUrl"],
                          width: 150,
                          fit: BoxFit.cover,
                        ),
                        title: Text(_loadedPhotos[index]['title']),
                        subtitle:
                            Text('Photo ID: ${_loadedPhotos[index]["id"]}'),
                      );
                    },
                  )));
  }
}
```

### 结果



<video class="" autoplay="" controls="" loop="" muted="" data-lazy-type="video" data-src="https://www.kindacode.com/wp-content/uploads/2020/11/Flutter-http.mp4" src="https://www.kindacode.com/wp-content/uploads/2020/11/Flutter-http.mp4" __idm_id__="1056611330" style="margin: 0px; padding: 0px; box-sizing: border-box; width: 720px;"></video>

## 结论

我们已经了解了两种从 Flutter 中的远程 API 获取数据的技术。
