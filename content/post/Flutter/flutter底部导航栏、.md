---
title: "Flutter底部导航栏、"
date: 2021-08-19T15:15:19+08:00
draft: false
---









首先我的目录结构请你了解

<img src="https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210819151930012.png" alt="image-20210819151930012" style="zoom:80%;" />



```dart
import 'package:flutter/material.dart';

import 'tabs/app.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      theme: ThemeData(
        primarySwatch: Colors.red,
      ),
      home: Tabs(),
    );
  }
}

```





```dart
import 'package:flutter/material.dart';

import 'home.dart';
import 'msg.dart';
import 'user.dart';

class Tabs extends StatefulWidget {
  const Tabs({Key key}) : super(key: key);

  @override
  _AppState createState() => _AppState();
}

class _AppState extends State<Tabs> {
  final List<Widget> _pageList = [HomePage(), MsgPage(), UserPage()];
  int _currentIndex = 0;
  PageController _pageController;

  final List<BottomNavigationBarItem> bottomNavItems = [
    BottomNavigationBarItem(icon: Icon(Icons.home), title: Text("首页")),
    BottomNavigationBarItem(icon: Icon(Icons.category), title: Text("分类")),
    BottomNavigationBarItem(icon: Icon(Icons.people), title: Text("我的"))
  ];

  /*切换页面*/
  void _changePage(int index) {
    /*如果点击的导航项不是当前项  切换 */
    if (index != _currentIndex) {
      setState(() {
        _currentIndex = index;
      });
    }
  }

  @override
  void initState() {
    // TODO: implement initState
    super.initState();
    this._pageController = new PageController(initialPage: this._currentIndex);
  }

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: Scaffold(
        body: PageView(
          controller: this._pageController,
          children: this._pageList,
          onPageChanged: (index) {
            setState(() {
              this._currentIndex = index;
            });
          },
          physics: NeverScrollableScrollPhysics(), //禁止pageView滑动
        ),
        bottomNavigationBar: BottomNavigationBar(
          items: bottomNavItems,
          currentIndex: this._currentIndex,
          onTap: (index) {
            setState(() {
              this._currentIndex = index;
              this._pageController.jumpToPage(index);
            });
          },
          type: BottomNavigationBarType.fixed,
          fixedColor: Colors.red,
        ),
      ),
    );
  }
}

```







```dart


import 'package:flutter/material.dart';

class HomePage extends StatefulWidget {
  const HomePage({Key key}) : super(key: key);

  @override
  _HomePageState createState() => _HomePageState();
}

class _HomePageState extends State<HomePage> {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text("首页"),
      ),
      body: Container(
        child: Text("首页"),
      ),
    );
  }
}

```





```dart

import 'package:flutter/material.dart';

class MsgPage extends StatefulWidget {
  const MsgPage({Key key}) : super(key: key);

  @override
  _MsgPageState createState() => _MsgPageState();
}

class _MsgPageState extends State<MsgPage> {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text("消息"),
      ),
      body: Container(
        child: Text("消息"),
      ),
    );
  }
}

```





```dart


import 'package:flutter/material.dart';

class UserPage extends StatefulWidget {
  const UserPage({Key key}) : super(key: key);

  @override
  _UserPageState createState() => _UserPageState();
}

class _UserPageState extends State<UserPage> {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text("个人"),
      ),
      body: Container(
        child: Text("个人"),
      ),
    );
  }
}

```







改造

```dart
import 'package:curved_navigation_bar/curved_navigation_bar.dart';
import 'package:flutter/material.dart';

import 'home.dart';
import 'msg.dart';
import 'user.dart';

class Tabs extends StatefulWidget {
  const Tabs({Key key}) : super(key: key);

  @override
  _AppState createState() => _AppState();
}

class _AppState extends State<Tabs> {
  final List<Widget> _pageList = [HomePage(), MsgPage(), UserPage()];
  int _currentIndex = 0;
  PageController _pageController;

  final List<BottomNavigationBarItem> bottomNavItems = [
    BottomNavigationBarItem(icon: Icon(Icons.home), title: Text("首页")),
    BottomNavigationBarItem(icon: Icon(Icons.category), title: Text("分类")),
    BottomNavigationBarItem(icon: Icon(Icons.people), title: Text("我的"))
  ];

  /*切换页面*/
  void _changePage(int index) {
    /*如果点击的导航项不是当前项  切换 */
    if (index != _currentIndex) {
      setState(() {
        _currentIndex = index;
      });
    }
  }

  @override
  void initState() {
    // TODO: implement initState
    super.initState();
    this._pageController = new PageController(initialPage: this._currentIndex);
  }

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: Scaffold(
        body: _pageList[_currentIndex],
        bottomNavigationBar: CurvedNavigationBar(
          // key: _bottomNavigationKey,
          index: 0,
          height: 60.0,
          items: <Widget>[
            Text(
              "首页",
              style: TextStyle(fontSize: 30),
            ),
            Icon(Icons.list, size: 30),
            Icon(Icons.compare_arrows, size: 30),
            // Icon(Icons.call_split, size: 30),
          ],
          color: Colors.white,
          buttonBackgroundColor: Colors.white,
          backgroundColor: Colors.blueAccent,
          animationCurve: Curves.easeInOut,
          animationDuration: Duration(milliseconds: 600),
          onTap: (index) {
            setState(() {
              _currentIndex = index;
            });
          },
          // letIndexChange: (index) => true,
        ),
      ),
    );
  }
}

```

