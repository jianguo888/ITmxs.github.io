---
title: "Flutter_AnimatedList–教程和示例"
date: 2021-09-07T13:01:49+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter","dart"]
categories: ["Flutter","dart"]
---

AnimatedList 是 Flutter 中的一个内置小部件，用于实现一个列表视图，该视图在插入或删除项目时对其进行动画处理。这有助于用户对列表的更改感觉不那么突然和更愉快。

在本文中，您将通过几个完整的示例了解如何使用 AnimatedList。第一个很简单，第二个稍微复杂一些。

**注意**：本文最近更新以修复一些与空安全相关的错误。

## 示例 1：基本

此示例可帮助您了解 AnimatedList 的基础知识。

### **预览**

<video class="" autoplay="" controls="" loop="" muted="" data-lazy-type="video" data-src="https://www.kindacode.com/wp-content/uploads/2021/01/Flutter-AnimatedList.mp4" playsinline="" src="https://www.kindacode.com/wp-content/uploads/2021/01/Flutter-AnimatedList.mp4" __idm_id__="566868993" style="margin: 0px; padding: 0px; box-sizing: border-box; width: 720px;"></video>

为简单起见，示例应用程序仅包含一个列表和一个浮动按钮(我们将在第二个示例中进行高级操作)。

### **重点是什么？**

- 当用户按下按钮时，一个新项目将被添加到列表中。我们使用**[_listKey].currentState.insertItem()**方法来初始化动画。
- 当用户触摸一个项目时，它将被删除。我们使用***\*[_listKey].currentState.removeItem()\**** 方法来触发动画。

### **完整代码**

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
  // Items in the list
  final _items = [];

  // The key of the list
  final GlobalKey<AnimatedListState> _key = GlobalKey();

  // Add a new item to the list
  // This is trigger when the floating button is pressed
  void _addItem() {
    _items.insert(0, "Item ${_items.length + 1}");
    _key.currentState!.insertItem(0, duration: Duration(seconds: 1));
  }

  // Remove an item
  // This is trigger when the trash icon associated with an item is tapped
  void _removeItem(int index) {
    _key.currentState!.removeItem(index, (_, animation) {
      return SizeTransition(
        sizeFactor: animation,
        child: Card(
          margin: EdgeInsets.all(10),
          elevation: 10,
          color: Colors.purple,
          child: ListTile(
            contentPadding: EdgeInsets.all(15),
            title: Text("Goodbye", style: TextStyle(fontSize: 24)),
          ),
        ),
      );
      ;
    }, duration: Duration(seconds: 1));

    _items.removeAt(index);
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Kindacode.com'),
      ),
      body: AnimatedList(
        key: _key,
        initialItemCount: 0,
        padding: EdgeInsets.all(10),
        itemBuilder: (_, index, animation) {
          return SizeTransition(
            key: UniqueKey(),
            sizeFactor: animation,
            child: Card(
              margin: EdgeInsets.all(10),
              elevation: 10,
              color: Colors.orange,
              child: ListTile(
                contentPadding: EdgeInsets.all(15),
                title: Text(_items[index], style: TextStyle(fontSize: 24)),
                trailing: IconButton(
                  icon: Icon(Icons.delete),
                  onPressed: () => _removeItem(index),
                ),
              ),
            ),
          );
        },
      ),
      floatingActionButton:
          FloatingActionButton(onPressed: _addItem, child: Icon(Icons.add)),
    );
  }
}
```

## 示例 2：更高级一点

这个类似于上面的例子，但有一些区别：

- **密钥**不再用于删除项目。相反，我们使用**上下文**和**AnimatedList.of(context).removeItem()**方法。
- 许多动画效果组合使用。

### **预览**

<video class="" autoplay="" controls="" loop="" muted="" data-lazy-type="video" data-src="https://www.kindacode.com/wp-content/uploads/2021/01/Flutter-AnimatedList-2.mp4" playsinline="" src="https://www.kindacode.com/wp-content/uploads/2021/01/Flutter-AnimatedList-2.mp4" __idm_id__="566868994" style="margin: 0px; padding: 0px; box-sizing: border-box; width: 720px;"></video>

### **完整代码**

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
      debugShowCheckedModeBanner: false,
      title: 'Kindacode.com',
      theme: ThemeData(
          primaryColor: Colors.green, accentColor: Colors.greenAccent),
      home: HomePage(),
    );
  }
}

class HomePage extends StatefulWidget {
  @override
  _HomePageState createState() => _HomePageState();
}

class _HomePageState extends State<HomePage> {
  // Items in the list
  final _items = ["Item 0"];

  // The key of the list
  final GlobalKey<AnimatedListState> _key = GlobalKey();

  // Add a new item to the list
  // This is trigger when the floating button is pressed
  void _addItem() {
    _items.insert(0, "Item ${_items.length + 1}");
    _key.currentState!.insertItem(0, duration: Duration(seconds: 1));
  }

  // Remove an item
  // This is trigger when an item is tapped
  void _removeItem(int index, BuildContext context) {
    AnimatedList.of(context).removeItem(index, (_, animation) {
      return FadeTransition(
        opacity: animation,
        child: SizeTransition(
          sizeFactor: animation,
          child: SizedBox(
            height: 150,
            child: Card(
              margin: const EdgeInsets.symmetric(vertical: 20),
              elevation: 10,
              color: Colors.red[400],
              child: Center(
                child: Text("I am going away", style: TextStyle(fontSize: 28)),
              ),
            ),
          ),
        ),
      );
    }, duration: Duration(seconds: 1));

    _items.removeAt(index);
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Kindacode.com'),
        actions: [
          IconButton(
            onPressed: _addItem,
            icon: Icon(Icons.plus_one_outlined),
          )
        ],
      ),
      body: AnimatedList(
        key: _key,
        initialItemCount: 1,
        padding: EdgeInsets.all(10),
        itemBuilder: (context, index, animation) {
          return SlideTransition(
            key: UniqueKey(),
            position: Tween<Offset>(
              begin: const Offset(-1, -0.5),
              end: Offset(0, 0),
            ).animate(animation),
            child: RotationTransition(
              turns: animation,
              child: SizeTransition(
                axis: Axis.vertical,
                sizeFactor: animation,
                child: SizedBox(
                  height: 150,
                  child: InkWell(
                    onTap: () => _removeItem(index, context),
                    child: Card(
                      margin: const EdgeInsets.symmetric(vertical: 20),
                      elevation: 10,
                      color: Colors.primaries[
                          (index * 100) % Colors.primaries.length][300],
                      child: Center(
                        child:
                            Text(_items[index], style: TextStyle(fontSize: 28)),
                      ),
                    ),
                  ),
                ),
              ),
            ),
          );
        },
      ),
    );
  }
}
```

## 应用程序接口

### **构造函数**

```dart
AnimatedList({
  Key key, 
  @required AnimatedListItemBuilder itemBuilder, 
  int initialItemCount: 0, 
  Axis scrollDirection: Axis.vertical, 
  bool reverse: false, 
  ScrollController controller, 
  bool primary, 
  ScrollPhysics physics, 
  bool shrinkWrap: false, 
  EdgeInsetsGeometry padding
})
```

### **参数详情**

下表中列出的前 4 个参数最常用：

| 范围                | 类型                                                         | 描述                                                         |
| ------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| itemBuilder(必需) | [动画列表项生成器](https://api.flutter.dev/flutter/widgets/AnimatedListItemBuilder.html) | 当列表中的项目滚动到视图中时构建它们                         |
| 初始物品计数        | 整数                                                         | 开始时的项目数                                               |
| 控制器              | 滚动控制器                                                   | 控制列表的滚动位置                                           |
| 钥匙                | 钥匙                                                         | 用于从外部访问列表                                           |
| 滚动方向            | 轴                                                           | 确定项目的滚动行为                                           |
| 逆转                | 布尔值                                                       | 判断scroll view是否在阅读方向滚动                            |
| 物理                | 滚动物理                                                     | 决定滚动在用户输入上的行为方式                               |
| 收缩包装            | 布尔值                                                       | 确定列表的大小是应占用全部可用空间还是与列表中项目的大小相匹配 |
| 填充                | EdgeInsetsGeometry                                           | 在项目列表周围设置填充                                       |
| 基本的              | 布尔值                                                       | 这是否是与父 PrimaryScrollController 关联的主滚动视图        |

## 结论

我们学习和发现了很多关于 AnimatedList 的东西，帮助我们在实际应用的实现中有更多的选择。
