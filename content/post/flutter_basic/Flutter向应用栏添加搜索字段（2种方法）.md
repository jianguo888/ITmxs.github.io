---
title: "Flutter向应用栏添加搜索字段(2种方法)"
date: 2021-09-07T10:48:31+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter","dart"]
categories: ["Flutter","dart"]
---

# Flutter：向应用栏添加搜索字段(2 种方法)

本文将带您了解在 Flutter 中向应用栏添加搜索字段的几个示例。第一个使用**AppBar**小部件(最常见于娱乐应用程序)，第二个使用**SliverAppBar**小部件(通常用于电子商务应用程序)。事不宜迟，让我们直接进入。



## 示例 1：AppBar 内的搜索字段

通常，许多娱乐应用(包括 Facebook、Youtube、Spotify 等大型应用)默认不显示搜索字段，而是显示搜索图标按钮。按下此按钮时，将以某种方式显示搜索字段。

### 预览

我们将要制作的演示应用程序包含 2 个屏幕(页面)：**HomePage**和**SearchPage**。用户可以通过点击搜索图标按钮从主页移动到搜索页面。搜索字段将通过使用SearchPage 的 AppBar的**title**参数来实现。

让我们看看它是如何工作的：

1.00

<video class="" autoplay="" controls="" loop="" muted="" data-lazy-type="video" data-src="https://www.kindacode.com/wp-content/uploads/2021/08/Flutter-appbar-search.mp4" playsinline="" src="https://www.kindacode.com/wp-content/uploads/2021/08/Flutter-appbar-search.mp4" __idm_id__="261654529" style="margin: 0px; padding: 0px; box-sizing: border-box; width: 720px;"></video>

### 编码

**./lib/main.dart 中**的完整源代码及说明：

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
        theme: ThemeData(
          primarySwatch: Colors.indigo,
        ),
        home: HomePage());
  }
}

// Home Page
class HomePage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Kindacode.com'),
        actions: [
          // Navigate to the Search Screen
          IconButton(
              onPressed: () => Navigator.of(context)
                  .push(MaterialPageRoute(builder: (_) => SearchPage())),
              icon: Icon(Icons.search))
        ],
      ),
    );
  }
}

// Search Page
class SearchPage extends StatelessWidget {
  const SearchPage({Key? key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
          // The search area here
          title: Container(
        width: double.infinity,
        height: 40,
        decoration: BoxDecoration(
            color: Colors.white, borderRadius: BorderRadius.circular(5)),
        child: Center(
          child: TextField(
            decoration: InputDecoration(
                prefixIcon: Icon(Icons.search),
                suffixIcon: IconButton(
                  icon: Icon(Icons.clear),
                  onPressed: () {
                    /* Clear the search field */
                  },
                ),
                hintText: 'Search...',
                border: InputBorder.none),
          ),
        ),
      )),
    );
  }
}
```

## 示例 2：搜索字段和 SliverAppBar

搜索是许多电子商务应用程序最重要的功能之一，因此它们通常以最易识别的方式显示搜索字段，并且从一开始就占用大量空间(亚马逊、Shopee 等)。

### 预览

1.00

<video class="" autoplay="" controls="" loop="" muted="" data-lazy-type="video" data-src="https://www.kindacode.com/wp-content/uploads/2021/08/Flutter-SliverAppBar-search.mp4" playsinline="" src="https://www.kindacode.com/wp-content/uploads/2021/08/Flutter-SliverAppBar-search.mp4" __idm_id__="261654530" style="margin: 0px; padding: 0px; box-sizing: border-box; width: 720px;"></video>

### 编码

完整来源：

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
        theme: ThemeData(
          primarySwatch: Colors.deepPurple,
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
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: CustomScrollView(
        slivers: [
          SliverAppBar(
            floating: true,
            pinned: true,
            snap: false,
            centerTitle: false,
            title: Text('Kindacode.com'),
            actions: [
              IconButton(
                icon: Icon(Icons.shopping_cart),
                onPressed: () {},
              ),
            ],
            bottom: AppBar(
              title: Container(
                width: double.infinity,
                height: 40,
                color: Colors.white,
                child: Center(
                  child: TextField(
                    decoration: InputDecoration(
                        hintText: 'Search for something',
                        prefixIcon: Icon(Icons.search),
                        suffixIcon: Icon(Icons.camera_alt)),
                  ),
                ),
              ),
            ),
          ),
          // Other Sliver Widgets
          SliverList(
            delegate: SliverChildListDelegate([
              Container(
                height: 400,
                child: Center(
                  child: Text(
                    'This is an awesome shopping platform',
                  ),
                ),
              ),
              Container(
                height: 1000,
                color: Colors.pink,
              ),
            ]),
          ),
        ],
      ),
    );
  }
}
```

## 回顾

我们研究了在移动应用中实现搜索栏的 2 种常见方法。
