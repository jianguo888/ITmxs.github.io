---
title: "Flutter_ExpansionPanelList和ExpansionPanelList"
date: 2021-09-23T15:47:18+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/image/Screen-Shot-2021-09-21-at-15.29.24.jpg)

下面是两个在 Flutter中实现**ExpansionPanelList**和**ExpansionPanelList.radio 的**例子。



## 基本扩展面板列表

本示例创建一个面板列表，其中可以同时展开多个面板。如果您希望一次只能打开一个面板，请参见第二个示例。

### 示例预览



<video class="" autoplay="" controls="" loop="" muted="" data-lazy-type="video" data-src="https://www.kindacode.com/wp-content/uploads/2021/09/Flutter-Expansion-Panel-List.mp4" playsinline="" src="https://www.kindacode.com/wp-content/uploads/2021/09/Flutter-Expansion-Panel-List.mp4" __idm_id__="454488065" style="margin: 0px; padding: 0px; box-sizing: border-box; width: 720px;"></video>

### 编码

带解释的代码：

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
          primarySwatch: Colors.indigo,
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
  // Generating some dummy data
  List<Map<String, dynamic>> _items = List.generate(
      20,
      (index) => {
            'id': index,
            'title': 'Item $index',
            'description':
                'This is the description of the item $index. There is nothing important here. In fact, it is meaningless.',
            'isExpanded': false
          });

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Kindacode.com'),
      ),
      body: SingleChildScrollView(
        child: ExpansionPanelList(
          elevation: 3,
          // Controlling the expansion behavior
          expansionCallback: (index, isExpanded) {
            setState(() {
              _items[index]['isExpanded'] = !isExpanded;
            });
          },
          animationDuration: Duration(milliseconds: 600),
          children: _items
              .map(
                (item) => ExpansionPanel(
                  canTapOnHeader: true,
                  backgroundColor:
                      item['isExpanded'] == true ? Colors.amber : Colors.white,
                  headerBuilder: (_, isExpanded) => Container(
                      padding:
                          EdgeInsets.symmetric(vertical: 15, horizontal: 30),
                      child: Text(
                        item['title'],
                        style: TextStyle(fontSize: 20),
                      )),
                  body: Container(
                    padding: EdgeInsets.symmetric(vertical: 15, horizontal: 30),
                    child: Text(item['description']),
                  ),
                  isExpanded: item['isExpanded'],
                ),
              )
              .toList(),
        ),
      ),
    );
  }
}
```

## 扩展面板列表.radio

您可以使用**ExpansionPanelList.radio**构造函数构建一个扩展面板列表，该列表一次最多只能打开一个面板。当您展开新面板时，旧的展开面板将自动折叠。逻辑由 Flutter 处理，如果没有特殊需要，不需要添加自己的**expandCallback**函数。因此，代码更简单、更整洁。

### 示例预览



<video class="" autoplay="" controls="" loop="" muted="" data-lazy-type="video" data-src="https://www.kindacode.com/wp-content/uploads/2021/09/Flutter-ExpansionPanelList.radio_.mp4" playsinline="" src="https://www.kindacode.com/wp-content/uploads/2021/09/Flutter-ExpansionPanelList.radio_.mp4" style="margin: 0px; padding: 0px; box-sizing: border-box; width: 720px;"></video>

### 编码

带解释的代码：

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
          primarySwatch: Colors.indigo,
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
  // Generating some dummy data
  List<Map<String, dynamic>> _items = List.generate(
      20,
      (index) => {
            'id': index,
            'title': 'Item $index',
            'description':
                'This is the description of the item $index. There is nothing important here. In fact, it is meaningless.',
          });

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Kindacode.com'),
      ),
      body: SingleChildScrollView(
        child: ExpansionPanelList.radio(
          elevation: 3,
          animationDuration: Duration(milliseconds: 600),
          children: _items
              .map(
                (item) => ExpansionPanelRadio(
                  value: item['id'],
                  canTapOnHeader: true,
                  headerBuilder: (_, isExpanded) => Container(
                      padding:
                          EdgeInsets.symmetric(vertical: 15, horizontal: 30),
                      child: Text(
                        item['title'],
                        style: TextStyle(fontSize: 20),
                      )),
                  body: Container(
                    padding: EdgeInsets.symmetric(vertical: 15, horizontal: 30),
                    child: Text(item['description']),
                  ),
                ),
              )
              .toList(),
        ),
      ),
    );
  }
}
```

## 参考



您可以在官方文档中找到有关本文中使用的小部件的更多信息：
