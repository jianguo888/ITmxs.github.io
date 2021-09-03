---
title: "《在Flutter中制作Glassmorphism效果侧边栏应用程序》"
subtitle: ""
date: 2020-11-17T23:00:02+08:00
lastmod: 2020-11-17T23:00:02+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter"]
categories: ["Flutter"]

hiddenFromHomePage: false
hiddenFromSearch: false
twemoji: false

fontawesome: true
linkToMarkdown: true
rssFullText: false


featuredImage: "https://luckly007.oss-cn-beijing.aliyuncs.com/img/90c6cc12-742e-4c9f-b318-b912f163b8d0.png"
featuredImagePreview: "https://luckly007.oss-cn-beijing.aliyuncs.com/img/90c6cc12-742e-4c9f-b318-b912f163b8d0.png"

toc:
  enable: true
  auto: true
math:
  enable: false
lightgallery: false
license: ""
share:
  enable: true
comment: true
mapbox:
  accessToken: ""
---



> 本文主要介绍使用 Flutter 制作Glass  Morphism 导航抽屉

<!--more-->

在这篇文章中，我将向您展示如何制作一个很酷的 Glass morphism 效果并使用 Flutter 将其应用到您的抽屉中，当然，您可以将在这里学到的内容应用到任何其他类型的小部件中，并且您可以设置您的样式应用程序使用这种新的设计趋势，所以不用多说，让我们开始吧。



## 1- 创建一个新的 Flutter 项目

使用你喜欢的IDE新建一个Flutter项目，把Flutter创建的默认代码去掉，换成这个代码👇

```dart
import 'package:flutter/material.dart';

class App extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Container(
      child: MaterialApp(
        home: HomeApp(),
      ),
    );
  }
}

class HomeApp extends StatefulWidget {
  HomeApp({Key key}) : super(key: key);

  @override
  _HomeAppState createState() => _HomeAppState();
}

class _HomeAppState extends State<HomeApp> {
  @override
  Widget build(BuildContext context) {
    return Container(
       child: Scaffold(
         appBar: AppBar(),
         drawer: , //all the code is going to be here,
         body: Container(),
       ),
    );
  }
}
```



## 2-制作抽屉

好的，从现在开始，我们将只处理 Scaffold 的 drawer 属性。在 drawer 属性中，我们将制作一个像这样的 Container 小部件

```dart
drawer: Container(
          width: 300,
          height: double.infinity,
          decoration: BoxDecoration(
            color: Color.fromARGB(180, 250, 250, 250),
            boxShadow: [
              BoxShadow(
                color: Color.fromRGBO(31, 38, 135, 0.4),
                blurRadius: 8.0,
              )
            ],
            border: Border(
              right: BorderSide(
                color: Colors.white70,
              ),
            ),
          ),
          child: Stack(),
```

现在在 Stack Widget 中添加这些组件。

```dart
child: Stack(
            children: [
              SizedBox(
                child: ClipRRect(
                  child: BackdropFilter(
                    filter: ImageFilter.blur(
                      sigmaX: 4.0,
                      sigmaY: 4.0,
                    ),
                    child: Container(
                      decoration: BoxDecoration(
                        gradient: LinearGradient(colors: [
                          Colors.grey.withOpacity(0.0),
                          Colors.white.withOpacity(0.2),
                        ]),
                      ),
                    ),
                  ),
                ),
              ),
              Column(
                children: [
                  DrawerHeader(
                    child: Row(
                      children: [
                        CircleAvatar(
                          backgroundImage: NetworkImage(
                              "https://randomuser.me/api/portraits/men/46.jpg"),
                          radius: 30.0,
                        ),
                        SizedBox(
                          width: 20.0,
                        ),
                        Text("User Name")
                      ],
                    ),
                  ),
                  Expanded(
                    child: ListView(
                      children: [
                        ListTile(
                          onTap: () {},
                          leading: Icon(
                            Icons.home,
                            color: Colors.black,
                          ),
                          title: Text("Home Page"),
                        ),
                        ListTile(
                          onTap: () {},
                          leading: Icon(
                            Icons.person,
                            color: Colors.black,
                          ),
                          title: Text("Profile Page"),
                        ),
                        ListTile(
                          onTap: () {},
                          leading: Icon(
                            Icons.settings,
                            color: Colors.black,
                          ),
                          title: Text("Settings"),
                        ),
                        ListTile(
                          onTap: () {},
                          leading: Icon(
                            Icons.logout,
                            color: Colors.black,
                          ),
                          title: Text("Log Out"),
                        )
                      ],
                    ),
                  )
                ],
              ),
            ],
          ),
```

好的，我们刚刚完成了我们的抽屉菜单，它可以使用了，结果也很棒，所以你可以自己尝试，也可以尝试定制。

## 3- 完整代码



```dart
import 'dart:ui';

import 'package:flutter/material.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: MyHomePage(title: 'Flutter Demo Home Page'),
    );
  }
}

class MyHomePage extends StatefulWidget {
  MyHomePage({Key key, this.title}) : super(key: key);

  final String title;

  @override
  _MyHomePageState createState() => _MyHomePageState();
}

class _MyHomePageState extends State<MyHomePage> {
  String _imageBg =
      "https://images.unsplash.com/photo-1561708232-fc2ac7e36676?ixid=MXwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHw%3D&ixlib=rb-1.2.1&auto=format&fit=crop&w=634&q=80";
  String _userAvatar = "https://randomuser.me/api/portraits/men/46.jpg";

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        elevation: 0.0,
        title: Text(widget.title),
      ),
      drawerScrimColor: Colors.transparent,
      drawer: Container(
        width: 300,
        height: double.infinity,
        decoration: BoxDecoration(
            color: Color.fromARGB(180, 250, 250, 250),
            boxShadow: [
              BoxShadow(
                color: Color.fromRGBO(31, 38, 135, 0.4),
                blurRadius: 8.0,
              )
            ],
            border: Border(
                right: BorderSide(
              color: Colors.white70,
            ))),
        child: Stack(
          children: [
            SizedBox(
              child: ClipRRect(
                child: BackdropFilter(
                  filter: ImageFilter.blur(
                    sigmaX: 4.0,
                    sigmaY: 4.0,
                  ),
                  child: Container(
                    decoration: BoxDecoration(
                        gradient: LinearGradient(colors: [
                      Colors.grey.withOpacity(0.0),
                      Colors.white.withOpacity(0.2),
                    ])),
                  ),
                ),
              ),
            ),
            Column(
              children: [
                DrawerHeader(
                  child: Row(
                    children: [
                      CircleAvatar(
                        backgroundImage: NetworkImage(_userAvatar),
                        radius: 30.0,
                      ),
                      SizedBox(
                        width: 20.0,
                      ),
                      Text("User Name")
                    ],
                  ),
                ),
                Expanded(
                  child: ListView(
                    children: [
                      ListTile(
                        onTap: () {},
                        leading: Icon(
                          Icons.home,
                          color: Colors.black,
                        ),
                        title: Text("Home Page"),
                      ),
                      ListTile(
                        onTap: () {},
                        leading: Icon(
                          Icons.person,
                          color: Colors.black,
                        ),
                        title: Text("Profile Page"),
                      ),
                      ListTile(
                        onTap: () {},
                        leading: Icon(
                          Icons.settings,
                          color: Colors.black,
                        ),
                        title: Text("Settings"),
                      ),
                      ListTile(
                        onTap: () {},
                        leading: Icon(
                          Icons.logout,
                          color: Colors.black,
                        ),
                        title: Text("Log Out"),
                      )
                    ],
                  ),
                )
              ],
            ),
          ],
        ),
      ),
      body: Container(
        decoration: BoxDecoration(
            image: DecorationImage(
          image: NetworkImage(_imageBg),
          fit: BoxFit.cover,
        )),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: () {},
        tooltip: 'Increment',
        child: Icon(Icons.add),
      ),
    );
  }
}
```

