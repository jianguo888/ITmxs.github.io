---
title: "Dart如何获取列表Flutter中所选项目的索引_键"
date: 2021-08-31T08:57:57+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter","dart"]
categories: ["Flutter","dart"]
---

我正在使用`ListTile`创建列表中的每个项目。每个项目都是从数据数组动态创建的。 `ListTile`提供`onTap`，但这对我来说还不够，因为我需要通过获取键或索引来找出单击了哪个项目。

ListTile:

```
     new ListTile(
        //leading: const Icon(Icons.flight_land),
        title: const Text('Trix\'s airplane'),
        subtitle:  const Text('The airplane is only in Act II.'),
        enabled: true,
        onTap: () { //TODO: get the identifier for this item },
        trailing: new Container(
          child: new Row(
            children: [
              new Text("70%"),
              const Icon(Icons.flight_land)
]
)
        ),
    )
```



**最佳答案**

您可能希望在`ListTile`中构建`ListView`的列表，并使用`List.generate`构造函数获取`children`的索引，这是一个简单的示例:

[![enter image description here](https://i.stack.imgur.com/4twWr.gif)](https://i.stack.imgur.com/4twWr.gif)

```
import "package:flutter/material.dart";

class ListTest extends StatefulWidget {
  @override
  _ListTestState createState() => new _ListTestState();
}

class _ListTestState extends State<ListTest> {
  final GlobalKey<ScaffoldState> _scaffoldKey = new GlobalKey<ScaffoldState>();
  int _id;
  @override
  Widget build(BuildContext context) {
    return new Scaffold(
      key: _scaffoldKey,
      appBar: new AppBar(title: new Text("List"),),
      body: new ListView(
          children: new List.generate(10, (int index){
            return new ListTile(title: new Text("item#$index"),
            onTap:(){
              setState((){
                _id = index; //if you want to assign the index somewhere to check
              });
              _scaffoldKey.currentState.showSnackBar(new SnackBar(content: new Text("You clicked item number $_id")));
            },
            );
          })

      ),
    );
  }
}
```


请记住，`List.generate`适用于小型列表，如果您正在阅读可扩展列表(例如，用户列表)，则需要使用`ListView.builder`而不是`ListView`，它具有`builder`参数，允许您通过以下方式遍历列表索引也是如此。

```
new ListView.builder(itemBuilder: (BuildContext context, int index) {
        //return your list
      }, 
```
