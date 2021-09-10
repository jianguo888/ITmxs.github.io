---
title: "FutureBuilder示例(空安全)"
date: 2021-09-06T17:17:07+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter","dart"]
categories: ["Flutter","dart"]
---

本文通过一个完整的示例向您展示如何在 Flutter 中使用 FutureBuilder 小部件。

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/image/Screen-Shot-2020-11-07-at-22.55.59.jpg)

简单来说，FutureBuilder 小部件在您的 Flutter 应用程序处理 HTTP 请求、读取本地数据时显示一件事，并在完成后显示另一件事。

## **例子**

我们将制作一个小的 Flutter 应用程序，它从一个开放的 API中获取和显示一些**帖子**。在加载数据时，应用程序会显示一个**CircularProgressIndicator**。完成后，它会显示帖子。

API URL(感谢 Typicode 团队为测试目的提供了很棒的 API)：

```dart
https://jsonplaceholder.typicode.com/posts
```

API 提供的单个帖子具有如下结构：

```dart
 {
    "userId": 1,
    "id": 3,
    "title": "ea molestias quasi exercitationem repellat qui ipsa sit aut",
    "body": "et iusto sed quo iure\nvoluptatem occaecati omnis eligendi aut ad\nvoluptatem doloribus vel accusantium quis pariatur\nmolestiae porro eius odio et labore et velit aut"
  },
```

### **入门**

**注意**：通过使用 FutureBuilder，在这个例子中不需要调用 setState()。

1、为了方便发送HTTP请求，我们使用了pub.dev的**[http](https://pub.dev/packages/http/install)**包。通过执行以下命令将插件添加到您的项目中：

```
dart pub add http
```

\2. 删除**main.dart 中**所有不需要的东西并添加以下内容：

```dart
import 'package:flutter/material.dart';

// import this to be able to call json.decode()
import 'dart:convert';

// import this to easily send HTTP request
import 'package:http/http.dart' as http;

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      // Hide the debug banner
      debugShowCheckedModeBanner: false,
      theme: ThemeData(accentColor: Colors.amber),
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
  Future<List> _loadData() async {
    List posts = [];
    try {
      // This is an open REST API endpoint for testing purposes
      const API = 'https://jsonplaceholder.typicode.com/posts';

      final http.Response response = await http.get(Uri.parse(API));
      posts = json.decode(response.body);
    } catch (err) {
      print(err);
    }

    return posts;
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
        appBar: AppBar(
          title: Text('Kindacode.com'),
        ),
        body: FutureBuilder(
            future: _loadData(),
            builder: (BuildContext ctx, AsyncSnapshot<List> snapshot) =>
                snapshot.hasData
                    ? ListView.builder(
                        itemCount: snapshot.data!.length,
                        itemBuilder: (BuildContext context, index) => Card(
                          margin: const EdgeInsets.all(10),
                          child: ListTile(
                            contentPadding: const EdgeInsets.all(10),
                            title: Text(snapshot.data![index]['title']),
                            subtitle: Text(snapshot.data![index]['body']),
                          ),
                        ),
                      )
                    : Center(
                        child: CircularProgressIndicator(),
                      )));
  }
}
```

3.检查结果：



<video class="" autoplay="" controls="" loop="" muted="" data-lazy-type="video" data-src="https://www.kindacode.com/wp-content/uploads/2020/11/Flutter-Future-Builder.mp4" src="https://www.kindacode.com/wp-content/uploads/2020/11/Flutter-Future-Builder.mp4" __idm_id__="686264321" style="margin: 0px; padding: 0px; box-sizing: border-box; width: 720px;"></video>

## 结论

 

我们已经研究了在 Flutter 中使用 FutureBuilder 从互联网异步加载数据的完整示例。
