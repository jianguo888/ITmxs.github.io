---
title: "《Flutter中的底部导航栏切换》"
subtitle: ""
date: 2021-07-31T12:11:03+08:00
lastmod: 2021-07-31T12:11:03+08:00
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



> 本文主要介绍flutter中的底部导航栏切换

<!--more-->

做android原生开发时，底部导航栏是通过自定义布局，图片自己上网找，点击之后还要变色，在切换的时候使用fragment，切换下一个的同时上一个隐藏……
现在，来看flutter怎么实现吧

## 效果

![img](https://uploadfiles.nowcoder.com/images/20200417/2967044_1587086806427_1A53AE36C08AE1F9B30B36167817C9C2)

## 解析

除了主界面以外，必然需要这三个界面，界面布局如下
![img](https://uploadfiles.nowcoder.com/images/20200417/2967044_1587086806688_D983E5605585C98765348B4A1E3FE2B4)
然后还需要一个**bottom.dart**表示主界面，**tabs.dart**表示底部栏以及调用上面三个界面，所以总共五个dart文件，不需要资源文件

### 单独界面 Category.dart

每一个界面很简单，只需要显示有色方块即可，用同一个模板，如 分类界面，显示绿色的框

```dart
import 'package:flutter/material.dart';
class CategoryPage extends StatefulWidget{
  CategoryPage({Key key}):super (key:key);
  _CategoryPageState createState()=>_CategoryPageState();
}

class _CategoryPageState extends State<CategoryPage>{
  @override
  Widget build(BuildContext context) {
    // TODO: implement build
    return Container(
      width: 300,
      height: 300,
      color: Colors.green,
    );
  }
}

```

另外两个类似

### 底部导航栏 tabs.dart

bottomNavigationBar组件，可以直接显示底部按钮，onTap方法进行交互,
setState方法可以实时渲染修改界面，currentIndex表示当前按下的位置

```dart
import 'package:flutter/material.dart';
import 'pages/Category.dart';
import 'pages/Home.dart';
import 'pages/Setting.dart';

class Tabs extends StatefulWidget{
  Tabs({Key key}):super(key:key);
  _TabsState createState()=>_TabsState();
}

class _TabsState extends State<Tabs>{
  int _currentIndex=0;
  //下面的三个方法都是三个界面的方法
  List _pageList=[
    HomePage(),
    CategoryPage(),
    SettingPage()
  ];
  @override
  Widget build(BuildContext context) {
    // TODO: implement build
    return
      Scaffold(
        appBar: AppBar(
          title: Text('底部导航栏切换'),
        ),
        /** * 切换底部导航栏的时候动态修改body内容 */
        body:this._pageList[this._currentIndex],
        bottomNavigationBar: BottomNavigationBar(
          currentIndex: this._currentIndex,
          //实现底部导航栏点击选***能
          onTap: (int index){
// this._currentIndex=index;//不会重新渲染
            setState(() {
              this._currentIndex=index;
            });
          },
          items: [
            BottomNavigationBarItem(
                icon: Icon(Icons.home),
                title: Text("首页")
            ),
            BottomNavigationBarItem(
                icon: Icon(Icons.category),
                title: Text("分类")
            ),
            BottomNavigationBarItem(
                icon: Icon(Icons.settings),
                title: Text("我的")
            )
          ],
        ),
      );
  }
}

```

### 主界面 bottom.dart

其实很简单，只需要调用Tabs()即可，具体只是想分离代码模块

```dart
import 'package:flutter/material.dart';
import 'package:flutter_app/tabs.dart';
void main() => runApp(MyApp());

class MyApp extends StatelessWidget{
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home:Tabs()
    );
  }
}


```

所有代码都在，直接运行即可

### 思考

本质上，这个切换是用列表排好的，但是最好应该使用键值对的形式，可能flutter有类似的方法我还没学到吧，不过，以上从理解的简单程度和实现的简单程度都是碾压原生开发的！