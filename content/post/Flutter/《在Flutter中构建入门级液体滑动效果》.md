---
title: "《在Flutter中构建入门级液体滑动效果》"
subtitle: ""
date: 2021-07-16T23:15:46+08:00
lastmod: 2021-07-16T23:15:46+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["flutter"]
categories: ["flutter"]

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



> 本文主要介绍如何使用 Flutter 制作一个具有酷炫液体滑动效果的酷炫入门页面

<!--more-->

我将向您展示如何使用 Flutter 制作一个具有酷炫液体滑动效果的酷炫入门页面，所以不用多说，让我们开始吧。在本课程中，我们不会

关注应用程序的 UI，但我们将关注如何实现液体滑动效果，以及如何为每张幻灯片创建屏幕。最终结果将如下所示

[![颤动中的液体滑动效果](https://1.bp.blogspot.com/-RmR9U00s-qk/X1dXCI2myTI/AAAAAAAACjg/NyUDFDVn68AsXIR1_t9JiOAN_gRiMHATgCLcBGAsYHQ/w294-h640/onboarding.gif)](https://1.bp.blogspot.com/-RmR9U00s-qk/X1dXCI2myTI/AAAAAAAACjg/NyUDFDVn68AsXIR1_t9JiOAN_gRiMHATgCLcBGAsYHQ/s2340/onboarding.gif)



好的，首先让我们为本教程添加依赖项，我们将使用“ gooey_carousel ”包

访问此链接： [gooey_carousel 包](https://pub.dev/packages/gooey_carousel)

在 pubspec.yaml 文件中添加此依赖项



```
gooey_carousel: ^0.1.2
```

现在使用脚手架创建一个简单的有状态小部件，并在脚手架的主体内添加boardPage小部件

```
return Scaffold(
      body: Scaffold(
        children: [
          boardPage("assets/page1.png", "Create your own notes", Colors.green),
          boardPage(
              "assets/page2.png", "Share your notes with friends", Colors.blue),
          boardPage(
              "assets/page3.png",
              "Protect your data with our authentication system",
              Colors.purple[200]),
        ],
      ),
    );
```

现在创建一个名为 boardpage 的新文件，我们将在其中创建一个自定义小部件 boardPage()。



对于图像资产，我添加了一个名为资产的文件夹并在其中添加了 3 个图像

[![img](https://1.bp.blogspot.com/-WqxPaJQ3niY/X1daq7v-HcI/AAAAAAAACjs/FNzXzbIVMws5QbO1L4DnqaEEFeizB9bFwCLcBGAsYHQ/s320/page1.png)](https://1.bp.blogspot.com/-WqxPaJQ3niY/X1daq7v-HcI/AAAAAAAACjs/FNzXzbIVMws5QbO1L4DnqaEEFeizB9bFwCLcBGAsYHQ/s1034/page1.png)



[![img](https://1.bp.blogspot.com/-0V7xfC0k2NY/X1daq2dCOoI/AAAAAAAACj0/wGNkiZC-R1k-s-LxDkEqTZgbTulLejk0gCLcBGAsYHQ/s320/page2.png)](https://1.bp.blogspot.com/-0V7xfC0k2NY/X1daq2dCOoI/AAAAAAAACj0/wGNkiZC-R1k-s-LxDkEqTZgbTulLejk0gCLcBGAsYHQ/s1224/page2.png)



[![img](https://1.bp.blogspot.com/-g92JJwDfVws/X1daq-GIQHI/AAAAAAAACjw/FPCCXqaxYwA8YgfAYixjzIGqhBvP2aUdgCLcBGAsYHQ/s320/page3.png)](https://1.bp.blogspot.com/-g92JJwDfVws/X1daq-GIQHI/AAAAAAAACjw/FPCCXqaxYwA8YgfAYixjzIGqhBvP2aUdgCLcBGAsYHQ/s1164/page3.png)

boardpage.dart



```
import 'package:flutter/material.dart';

Widget boardPage(String imgUrl, String description, Color color) {
  return Container(
    color: color,
    child: Padding(
      padding: const EdgeInsets.symmetric(horizontal: 24.0),
      child: Column(
        mainAxisAlignment: MainAxisAlignment.center,
        crossAxisAlignment: CrossAxisAlignment.center,
        children: [
          Image.asset(imgUrl),
          Text(
            description,
            textAlign: TextAlign.center,
            style: TextStyle(
              fontSize: 35.0,
              color: Colors.white,
            ),
          ),
        ],
      ),
    ),
  );
}

```

就是这样。

这是 main.dart 文件的完整代码

```
import 'package:flutter/material.dart';
import 'package:gooey_carousel/gooey_carrousel.dart';
import 'package:liquid_swipe/boardpage.dart';

void main() {
  runApp(MaterialApp(
    debugShowCheckedModeBanner: false,
    home: MyApp(),
  ));
}

class MyApp extends StatefulWidget {
  @override
  _MyAppState createState() => _MyAppState();
}

class _MyAppState extends State<MyApp> {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: GooeyCarousel(
        children: [
          boardPage("assets/page1.png", "Create your own notes", Colors.green),
          boardPage(
              "assets/page2.png", "Share your notes with friends", Colors.blue),
          boardPage(
              "assets/page3.png",
              "Protect your data with our authentication system",
              Colors.purple[200]),
        ],
      ),
    );
  }
}
```

