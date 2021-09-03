---
title: "Flutter中的流管理"
date: 2021-08-15T12:50:01+08:00
draft: false
tags: ["Flutter"]
categories: ["Flutter"]
---



# Flutter 中的流管理

在每个项目中，都有一个很大的任务是我们如何以更好的方式管理异步类型的数据流和动态数据控制结构。

对于这篇文章，我将尝试彻底解释它。我们将如何在Flutter 中管理流。
Flutter 中有很多用于流管理的库。
像 mobx、reduX、bloc 等。

但在使用之前我们需要知道。我们将如何在Flutter 中管理状态。

首先，什么是Flutter 中的流。

Stream 提供了一种接收一系列事件的方法。每个事件要么……

举个简单的例子，我们就知道了。这个怎么运作。

```dart
import 'dart:async';
import 'package:flutter/material.dart';

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: MyHomePage(),
    );
  }
}

class MyHomePage extends StatefulWidget {
  @override
  _MyHomePageState createState() => _MyHomePageState();
}

class _MyHomePageState extends State<MyHomePage> {
  Stream<int> numberStream() async* {
    for (int i = 1; i <= 10; i++) {
      await Future.delayed(Duration(seconds: 1));
      yield i;
    }
  }

  @override
  void dispose() {
// TODO: implement dispose
    super.dispose();
  }

  @override
  void initState() {
// TODO: implement initState
    super.initState();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text("Stream"),
      ),
      body: Center(
          child: StreamBuilder(
        stream: numberStream().map((number) => "number $number"),
        builder: (context, snapshot) {
          if (snapshot.hasError)
            return Text("hey there is some error");
          else if (snapshot.connectionState == ConnectionState.waiting)
            return CircularProgressIndicator();
          return Text(
            "${snapshot.data}",
            style: Theme.of(context).textTheme.display1,
          );
        },
      )),
    );
  }
}

```