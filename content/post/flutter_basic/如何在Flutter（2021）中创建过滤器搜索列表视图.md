---
title: "如何在Flutter（2021）中创建过滤器搜索列表视图"
date: 2021-09-06T17:13:18+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter","dart"]
categories: ["Flutter","dart"]
---




![img](https://luckly007.oss-cn-beijing.aliyuncs.com/image/Screen-Shot-2021-04-06-at-15.50.55.jpg)

这篇文章是关于在 Flutter 中制作**过滤/搜索 ListView**。我们将快速浏览完成工作的方法，然后通过**一个具体而完整的示例**来应用该方法。不需要第三方软件包。







## 概述

我们将创建一个函数来过滤结果，当文本字段更改 (onChanged) 时将调用此函数。搜索算法可能因情况而异，但最简单和最流行的是使用以下方法：

- **where()**：返回一个新的惰性 Iterable，其中包含满足一个或多个条件的所有元素。
- **contains()**：用于确定一个字符串是否包含另一个字符串（您可以尝试其他字符串方法，如**startsWith()**、**endsWith()**等）。
- **toLowerCase()**：这个字符串方法会将这个字符串中的所有字符转换为小写，这样搜索关键字是大写还是小写都没有关系。

这些词可能会令人困惑。请参阅示例以获得更清晰的信息。

## 例子

假设我们有一个用户列表，其中包含一些信息，包括 ID、姓名和年龄。一开始，所有这些用户都显示在一个 ListView 中。如果您在搜索字段中键入内容，则只会显示名称与关键字匹配的用户。如果您清除搜索字段，将再次显示完整的用户列表。

### 预览



<video class="" autoplay="" loop="" muted="" data-lazy-type="video" data-src="https://www.kindacode.com/wp-content/uploads/2021/04/Flutter-filter-list-view.mp4" playsinline="" src="https://www.kindacode.com/wp-content/uploads/2021/04/Flutter-filter-list-view.mp4" __idm_id__="337946625" style="margin: 0px; padding: 0px; box-sizing: border-box; width: 720px;"></video>

### 编码

**lib/main.dart 中**的完整源代码以及注释中的解释：

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
      home: HomePage(),
    );
  }
}

class HomePage extends StatefulWidget {
  @override
  _HomePageState createState() => _HomePageState();
}

class _HomePageState extends State<HomePage> {
  // This holds a list of fiction users
  // You can use data fetched from a database or cloud as well
  final List<Map<String, dynamic>> _allUsers = [
    {"id": 1, "name": "Andy", "age": 29},
    {"id": 2, "name": "Aragon", "age": 40},
    {"id": 3, "name": "Bob", "age": 5},
    {"id": 4, "name": "Barbara", "age": 35},
    {"id": 5, "name": "Candy", "age": 21},
    {"id": 6, "name": "Colin", "age": 55},
    {"id": 7, "name": "Audra", "age": 30},
    {"id": 8, "name": "Banana", "age": 14},
    {"id": 9, "name": "Caversky", "age": 100},
    {"id": 10, "name": "Becky", "age": 32},
  ];

  // This list holds the data for the list view
  List<Map<String, dynamic>> _foundUsers = [];
  @override
  initState() {
    // at the beginning, all users are shown
    _foundUsers = _allUsers;
    super.initState();
  }

  // This function is called whenever the text field changes
  void _runFilter(String enteredKeyword) {
    List<Map<String, dynamic>> results = [];
    if (enteredKeyword.isEmpty) {
      // if the search field is empty or only contains white-space, we'll display all users
      results = _allUsers;
    } else {
      results = _allUsers
          .where((user) =>
              user["name"].toLowerCase().contains(enteredKeyword.toLowerCase()))
          .toList();
      // we use the toLowerCase() method to make it case-insensitive
    }

    // Refresh the UI
    setState(() {
      _foundUsers = results;
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Kindacode.com'),
      ),
      body: Padding(
        padding: const EdgeInsets.all(10),
        child: Column(
          children: [
            SizedBox(
              height: 20,
            ),
            TextField(
              onChanged: (value) => _runFilter(value),
              decoration: InputDecoration(
                  labelText: 'Search', suffixIcon: Icon(Icons.search)),
            ),
            SizedBox(
              height: 20,
            ),
            Expanded(
              child: _foundUsers.length > 0
                  ? ListView.builder(
                      itemCount: _foundUsers.length,
                      itemBuilder: (context, index) => Card(
                        key: ValueKey(_foundUsers[index]["id"]),
                        color: Colors.amberAccent,
                        elevation: 4,
                        margin: EdgeInsets.symmetric(vertical: 10),
                        child: ListTile(
                          leading: Text(
                            _foundUsers[index]["id"].toString(),
                            style: TextStyle(fontSize: 24),
                          ),
                          title: Text(_foundUsers[index]['name']),
                          subtitle: Text(
                              '${_foundUsers[index]["age"].toString()} years old'),
                        ),
                      ),
                    )
                  : Text(
                      'No results found',
                      style: TextStyle(fontSize: 24),
                    ),
            ),
          ],
        ),
      ),
    );
  }
}
```

实际上，在这种情况下我们不需要**TextEditingController**。

## 结论

您已经学习了如何在 Flutter 中创建过滤器/搜索 ListView。
