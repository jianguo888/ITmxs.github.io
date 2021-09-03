---
title: "《在Flutter中制作Whatsappcloneapp应用程序》"
subtitle: ""
date: 2020-12-17T23:13:18+08:00
lastmod: 2020-12-17T23:13:18+08:00
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



> 本文主要介绍使用 Flutter 制作 Whatsapp 克隆应用 UI

<!--more-->





![image-20210717231412403](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210717231412403.png)

在这篇文章中，我将向您展示如何仅使用 Flutter 制作 WhatsApp 克隆应用程序 UI。在我们开始之前，我想告诉您，我们将只创建应用程序的 UI，我们不会实现任何功能，例如聊天功能或登录/注册...。

main.dart

```dart
import 'package:flutter/material.dart';
import 'package:whatsapp/components/chattile.dart';
import 'package:whatsapp/components/storybtn.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      home: Whatsapp(),
    );
  }
}

class Whatsapp extends StatefulWidget {
  @override
  _WhatsappState createState() => _WhatsappState();
}

class _WhatsappState extends State<Whatsapp> {
  //Adding the main color of the app
  Color mainColor = Color(0xFF177767);
  var containerRadius = Radius.circular(30.0);
  //Adding a list of image URL to simulate the avatar picture
  List<String> imageUrl = [
    "https://i.pinimg.com/originals/2e/2f/ac/2e2fac9d4a392456e511345021592dd2.jpg",
    "https://randomuser.me/api/portraits/men/86.jpg",
    "https://randomuser.me/api/portraits/women/80.jpg",
    "https://randomuser.me/api/portraits/men/43.jpg",
    "https://randomuser.me/api/portraits/women/49.jpg",
    "https://randomuser.me/api/portraits/women/45.jpg",
    "https://randomuser.me/api/portraits/women/0.jpg",
    "https://randomuser.me/api/portraits/women/1.jpg",
    "https://randomuser.me/api/portraits/men/0.jpg"
  ];

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      backgroundColor: mainColor,
      appBar: AppBar(
        elevation: 0.0,
        title: Text("WhatsApp Clone"),
        backgroundColor: mainColor,
        actions: [
          IconButton(
            onPressed: () {},
            icon: Icon(Icons.search),
          ),
          IconButton(
            onPressed: () {},
            icon: Icon(Icons.more_horiz),
          ),
        ],
      ),
      body: Column(
        children: [
          //First let's create the Story time line container
          Container(
            height: 100.0,
            child: Padding(
              padding:
                  const EdgeInsets.symmetric(horizontal: 12.0, vertical: 8.0),
              child: ListView(
                scrollDirection: Axis.horizontal,
                children: [
                  //Let's create a custom widget for our story button
                  storyButton(imageUrl[0], "Davie yo"),
                  storyButton(imageUrl[1], "Jack Brell"),
                  storyButton(imageUrl[2], "Anjie wo"),
                  storyButton(imageUrl[3], "Joseph "),
                  storyButton(imageUrl[4], "Juline kujo"),
                  storyButton(imageUrl[5], "Juline kujo"),
                  storyButton(imageUrl[6], "Juline kujo"),
                ],
              ),
            ),
          ),

          //Now let's create our chat timeline
          Expanded(
            child: Container(
              decoration: BoxDecoration(
                color: Colors.white,
                borderRadius: BorderRadius.only(
                    topLeft: containerRadius, topRight: containerRadius),
              ),
              child: Padding(
                padding:
                    const EdgeInsets.only(left: 12.0, right: 12.0, top: 10.0),
                child: ListView(
                  children: [
                    //Now let's create our chat tile custom widget
                    chatTile(imageUrl[0], "userName", "msg", "9Am", false),
                    chatTile(imageUrl[1], "userName", "msg", "8Am", true),

 
                    chatTile(imageUrl[2], "userName", "msg", "6Am", true),
                    chatTile(
                        imageUrl[3], "userName", "msg", "Yesterday", false),
                    chatTile(
                        imageUrl[5], "userName", "msg", "Yesterday", false),
                    chatTile(imageUrl[4], "userName", "msg", "San 20", true),
                    chatTile(imageUrl[6], "userName", "msg", "San20", true),
                    chatTile(imageUrl[7], "userName", "msg", "San20", true),
                  ],
                ),
              ),
            ),
          )
        ],
      ),
    );
  }
}

```

现在我将创建一个名为 components 的新文件夹，我们将添加 2 个文件，story button widget and the other for the chat list tile

storybutton.dart

```dart
import 'package:flutter/material.dart';

Widget storyButton(String imgUrl, String userName) {
  return Padding(
    padding: const EdgeInsets.only(right: 10.0),
    child: Column(
      children: [
        CircleAvatar(
          backgroundImage: NetworkImage(imgUrl),
          radius: 26.0,
        ),
        SizedBox(
          height: 5.0,
        ),
        Text(
          userName,
          style: TextStyle(color: Colors.white),
        ),
      ],
    ),
  );
}

```

chattile.dart

```dart
import 'package:flutter/material.dart';

Widget chatTile(
    String imgUrl, String userName, String msg, String date, bool seen) {
  return InkWell(
    onTap: () {},
    child: Padding(
      padding: const EdgeInsets.symmetric(vertical: 12.0),
      child: Row(
        crossAxisAlignment: CrossAxisAlignment.start,
        mainAxisAlignment: MainAxisAlignment.start,
        children: [
          CircleAvatar(
            backgroundImage: NetworkImage(imgUrl),
            radius: 28.0,
          ),
          SizedBox(
            width: 8.0,
          ),
          Expanded(
            child: Column(
              mainAxisAlignment: MainAxisAlignment.start,
              crossAxisAlignment: CrossAxisAlignment.start,
              children: [
                Row(
                  children: [
                    Expanded(
                      child: Text(
                        userName,
                        style: TextStyle(
                          fontSize: 18.0,
                          fontWeight: FontWeight.w500,
                        ),
                      ),
                    ),
                    Text(date),
                  ],
                ),
                SizedBox(
                  height: 5.0,
                ),
                Row(
                  children: [
                    Expanded(child: Text(userName)),
                    if (seen)
                      Icon(
                        Icons.check_circle,
                        size: 18.0,
                        color: Colors.green,
                      ),
                    if (!seen)
                      Icon(
                        Icons.check_circle_outline,
                        color: Colors.grey,
                        size: 18.0,
                      ),
                  ],
                ),
              ],
            ),
          ),
        ],
      ),
    ),
  );
}


```

