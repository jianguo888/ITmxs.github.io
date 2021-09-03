---
title: "《在Flutter中构建笑话生成器应用程序》"
subtitle: ""
date: 2021-07-10T23:09:06+08:00
lastmod: 2021-07-10T23:09:06+08:00
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



> 在本教程中，我将向您展示如何使用 Flutter 制作一个简单的笑话生成器应用程序

<!--more-->



对于这个项目，我们将从 RESTful API 获取数据

API的链接： [随机笑话](https://official-joke-api.appspot.com/jokes/programming/random)

对于这个项目，我不会关注应用程序的 UI，我们只会关注如何从 URL 中获取数据，以及如何显示它们

在我们开始之前，您必须将此包添加到您的 pubspec.yaml 文件中

依赖项：http：^0.12.2   

有关 HTTP 包的更多信息，请访问：[HTTP 包 Flutter](https://pub.dev/packages/http)

源代码：

```dart
import 'dart:convert';

import 'package:flutter/material.dart';
import 'package:http/http.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      home: JokeApp(),
    );
  }
}

class JokeApp extends StatefulWidget {
  @override
  _JokeAppState createState() => _JokeAppState();
}

class _JokeAppState extends State<JokeApp> {
  //performing the http request
  String url = "https://official-joke-api.appspot.com/jokes/programming/random";
  String setup = "";
  String punchline = "";
  String type = "";
  Future<String> getData() async {
    Response res = await get(url, headers: {"Accept": "application/json"});
    var data = jsonDecode(res.body);
    print(data[0]['setup']);
    print(data[0]['punchline']);
    setState(() {
      setup = data[0]['setup'];
      punchline = data[0]['punchline'];
      type = data[0]['type'];
    });
    print(res.body);
  }

  @override
  void initState() {
    // TODO: implement initState
    getData();
    super.initState();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      backgroundColor: Color(0xFF212121),
      body: Container(
        width: double.infinity,
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          crossAxisAlignment: CrossAxisAlignment.center,
          children: [
            Text(
              "${type} Jokes",
              style: TextStyle(
                color: Colors.white,
                fontSize: 26.0,
              ),
            ),
            SizedBox(
              height: 30.0,
            ),
            Card(
              child: Container(
                width: MediaQuery.of(context).size.width * 0.85,
                height: MediaQuery.of(context).size.height * 0.6,
                child: Padding(
                  padding: const EdgeInsets.all(12.0),
                  child: Column(
                    mainAxisAlignment: MainAxisAlignment.center,
                    crossAxisAlignment: CrossAxisAlignment.center,
                    children: [
                      Text(
                        "- ${setup}",
                        style: TextStyle(
                          fontSize: 26.0,
                        ),
                      ),
                      SizedBox(
                        height: 10.0,
                      ),
                      Text(
                        "- ${punchline}",
                        style: TextStyle(
                          fontSize: 26.0,
                        ),
                      ),
                    ],
                  ),
                ),
              ),
            ),
            SizedBox(
              height: 20.0,
            ),
            RaisedButton(
              onPressed: () {
                getData();
              },
              color: Color(0xFFFAFAFA),
              child: Padding(
                padding:
                    const EdgeInsets.symmetric(horizontal: 15.0, vertical: 8.0),
                child: Text("Another Joke"),
              ),
            )
          ],
        ),
      ),
    );
  }
}

```


