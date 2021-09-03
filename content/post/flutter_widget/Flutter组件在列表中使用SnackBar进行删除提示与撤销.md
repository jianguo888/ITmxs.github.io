---
title: "Flutter组件在列表中使用SnackBar进行删除提示与撤销"
date: 2021-09-01T08:46:47+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter","dart"]
categories: ["Flutter","dart"]
---

## 一、SnackBar

SnackBar 是 Flutter 提供的一种提示 Widget，附带操作（Action）功能

构造如下：

```dart
  const SnackBar({
    Key key,
    @required this.content,
    this.backgroundColor,
    this.elevation,
    this.shape,
    this.behavior,
    this.action,
    this.duration = _snackBarDisplayDuration,
    this.animation,
  })
```

其中 `this.content` 是必传，不能是 null，而 elevation 则是 null 或者是 非负值

默认 elevation 是 6.0

```dart
final double elevation = this.elevation ?? snackBarTheme.elevation ?? 6.0;
```

SnackBar 的显示和隐藏并不需要自己控制， Widget 本身提供了方法 `hideCurrentSnackBar` 和 `showSnackBar` 自己完成动画的渐变出现和消失



## 二、使用 SnackBar



### 1、创建 SnackBar

使用 SnackBar 首先需要创建一个 SnackBar

```dart
 // show snackBar
      SnackBar snackBar = SnackBar(
        content: Text('已经删除'),
        action: SnackBarAction(
            label: '撤销',
            onPressed: () {
              }
            }),
      );
```

上面创建了一个带 action 的 SnackBar



### 2、显示 SnackBar

```dart
Scaffold.of(context).showSnackBar(snackBar);
```



### 3、隐藏当前的 SnackBar

```dart
 Scaffold.of(context).hideCurrentSnackBar();
```



## 三、在 ListView 中实现删除提示及撤销功能



### 1、创建一个 ListView

`_getList()` 方法返回一个 `List<Widget>` 用于构建列表

```dart
  List<Widget> _getList() {
    List<Widget> list = [];
    for (int i = 0; i < _list.length; i++) {
      list.add(ListItem(
        title: _list[i]['title'],
        subTtitle: _list[i]['subTitle'],
        onDelete: _onDeleteHandle(i),
      ));
    }
    return list;
  }

  @override
  Widget build(BuildContext context) {
    return ListView(
      children: _getList(),
    );
  }
```

其中每个 `ListItem` 其实有一个 `onDelete` 方法传入，用于进行删除操作，这里直接就操作了删除

```dart
class ListItem extends StatelessWidget {
  final Function onDelete;
  final String title;
  final String subTtitle;
  const ListItem({Key key, this.onDelete, this.title, this.subTtitle})
      : super(key: key);

  @override
  Widget build(BuildContext context) {
    return ListTile(
        title: Text(title),
        subtitle: Text(subTtitle),
        trailing: InkWell(
          child: Icon(Icons.delete),
          onTap: this.onDelete,
        ));
  }
}
```



### 2、处理删除及撤销

删除操作是将一个内容从 ListView 中移除，而我们需要的做的临时记录值和index，进行 SnackBar 提示是否撤销，如果需要撤销，则恢复。

下面代码中 `_deleteCache` 保存了删除的 index 和 data

```dart
  Function _onDeleteHandle(i) {
    return () {
      _deleteCache = {'index': i, 'data': _list[i]};
      setState(() {
        _list.removeAt(i);
      });
      // show snackBar
      SnackBar snackBar = SnackBar(
        content: Text('已经删除'),
        action: SnackBarAction(
            label: '撤销',
            onPressed: () {
              if (_deleteCache != null) {
                setState(() {
                  _list.insert(_deleteCache['index'], _deleteCache['data']);
                });

                _deleteCache = null;
              }
            }),
      );
      Scaffold.of(context).hideCurrentSnackBar();
      Scaffold.of(context).showSnackBar(snackBar);
    };
  }
```

有一点需要注意的是， `showSnackBar` 之前，需要隐藏当前的 SnackBar，否则会弹出两个来，消失也会逐个消失。

```dart
 Scaffold.of(context).hideCurrentSnackBar();
```



### 3、效果

![GIF3.gif](https://luckly007.oss-cn-beijing.aliyuncs.com/image/2364937434.gif)



## 完整代码

```dart
import 'dart:math';

import 'package:flutter/material.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: Scaffold(
        appBar: AppBar(
          title: Text('SnackBar Widget'),
        ),
        body: HomeContent(),
      ),
    );
  }
}

class HomeContent extends StatefulWidget {
  HomeContent({Key key}) : super(key: key);

  _HomeContentState createState() => _HomeContentState();
}

class _HomeContentState extends State<HomeContent> {
  List<Map> _list = [
    {'title': '标题1', 'subTitle': '二级标题1'},
    {'title': '标题2', 'subTitle': '二级标题2'},
    {'title': '标题3', 'subTitle': '二级标题3'},
  ];

  Map _deleteCache;

  Function _onDeleteHandle(i) {
    return () {
      _deleteCache = {'index': i, 'data': _list[i]};
      setState(() {
        _list.removeAt(i);
      });
      // show snackBar
      SnackBar snackBar = SnackBar(
        content: Text('已经删除'),
        action: SnackBarAction(
            label: '撤销',
            onPressed: () {
              if (_deleteCache != null) {
                setState(() {
                  _list.insert(_deleteCache['index'], _deleteCache['data']);
                });
                
                _deleteCache = null;
              }
            }),
      );
      Scaffold.of(context).hideCurrentSnackBar();
      Scaffold.of(context).showSnackBar(snackBar);
    };
  }

  List<Widget> _getList() {
    List<Widget> list = [];
    for (int i = 0; i < _list.length; i++) {
      list.add(ListItem(
        title: _list[i]['title'],
        subTtitle: _list[i]['subTitle'],
        onDelete: _onDeleteHandle(i),
      ));
    }
    return list;
  }

  @override
  Widget build(BuildContext context) {
    return ListView(
      children: _getList(),
    );
  }
}

class ListItem extends StatelessWidget {
  final Function onDelete;
  final String title;
  final String subTtitle;
  const ListItem({Key key, this.onDelete, this.title, this.subTtitle})
      : super(key: key);

  @override
  Widget build(BuildContext context) {
    return ListTile(
        title: Text(title),
        subtitle: Text(subTtitle),
        trailing: InkWell(
          child: Icon(Icons.delete),
          onTap: this.onDelete,
        ));
  }
}
```

