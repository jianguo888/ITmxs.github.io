---
title: "DFlutter全局悬浮按钮"
date: 2021-08-22T12:45:13+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---

> 本文主要介绍

<!--more-->



# 方法一

![image-20210822124957439](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210822124957439.png)





```dart
  Offset _offset = Offset.zero;
  Scaffold(
        body: Stack(
          children: [
            _pageList[_currentIndex],
            Positioned(
              left: _offset.dx,
              top: _offset.dy,
              child: GestureDetector(
                onPanUpdate: (d) =>
                    setState(() => _offset += Offset(d.delta.dx, d.delta.dy)),
                child: FloatingActionButton(
                  onPressed: () {},
                  backgroundColor: Colors.orange,
                  child: Icon(Icons.add),
                ),
              ),
            ),
          ],
        ),
```



# 方法二

```dart
 WidgetsBinding.instance
        .addPostFrameCallback((_) => _insertOverlay(context));
```



```dart
void _insertOverlay(BuildContext context) {
    return Overlay.of(context).insert(
      OverlayEntry(builder: (context) {
        final size = MediaQuery.of(context).size;
        print(size.width);
        return Positioned(
          width: 56,
          height: 56,
          top: size.height - 72,
          left: size.width - 72,
          child: Material(
            color: Colors.transparent,
            child: GestureDetector(
              onTap: () => print('ON TAP OVERLAY!'),
              child: Container(
                decoration: BoxDecoration(
                    shape: BoxShape.circle, color: Colors.redAccent),
              ),
            ),
          ),
        );
      }),
    );
  }
```



# 方法三

1.场景
现在需要做一个Test按钮，悬浮在所有页面之上，并且可以拖拽。

2.思路
1）悬浮按钮可以使用flutter提供的Overlay + OverlayEntry 组合实现

2）拖拽功能可以使用GestureDetector手势按钮或者Draggable实现（PS：我做了一版Draggable实现的，但是发现它会有原本的widget浮在原地，显然不是我要的效果）

3）点击的时候我是让它弹出一个底部弹框，这里你们可以自由发挥，本篇文章不做多余赘述

```dart
PubScaffold(
      child: MaterialApp(
        theme: CustomTheme.lightTheme,
        darkTheme: CustomTheme.darkTheme,
        themeMode: currentTheme.currentTheme,
        home: Scaffold(
          body: Stack(
            children: [
              _pageList[_currentIndex],
              // Positioned(
              //   left: _offset.dx,
              //   top: _offset.dy,
              //   child: GestureDetector(
              //     onPanUpdate: (d) =>
              //         setState(() => _offset += Offset(d.delta.dx, d.delta.dy)),
              //     child: FloatingActionButton(
              //       onPressed: () {},
              //       backgroundColor: Colors.orange,
              //       child: Icon(Icons.add),
              //     ),
              //   ),
              // ),
            ],
          ),
          bottomNavigationBar: CurvedNavigationBar(
            // key: _bottomNavigationKey,
            index: 0,
            height: 60.0,
            items: <Widget>[
              Icon(Icons.home, size: 30),
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
      ),
    );
```

这里的`PubScaffold`就是我封装的一个悬浮按钮组件，把它包裹在MaterialApp外面，就可以实现悬浮在所有的组件之上的一个按钮啦（当然也可以不是按钮，具体样式可以自己定义）。下面我们来看一下`PubScaffold`中的代码吧～

```dart
import 'dart:math';

import 'package:flutter/material.dart';

class PubScaffold extends StatefulWidget {
  final Widget child;
  PubScaffold({this.child});

  @override
  _PubScaffoldState createState() => _PubScaffoldState();
}

class _PubScaffoldState extends State<PubScaffold> {
  bool draggable = false;

  //静止状态下的offset
  Offset idleOffset = Offset(0, 0);
  //本次移动的offset
  Offset moveOffset = Offset(0, 0);
  //最后一次down事件的offset
  Offset lastStartOffset = Offset(0, 0);

  int count = 0;

  final List<String> testWidgetList = [
    '测试1',
    '测试2',
  ];

  testAppFun(e) {
    // TODO: 你的代码逻辑
  }

  // 显示一个底部弹窗，这里是一个测试列表
  showTestList() {
    showModalBottomSheet(
      context: context,
      enableDrag: false,
      shape: RoundedRectangleBorder(
        borderRadius: BorderRadius.only(
          topLeft: Radius.circular(20.0),
          topRight: Radius.circular(20.0),
        ),
      ),
      builder: (BuildContext context) {
        return ListView(
          children: testWidgetList
              .map(
                (e) => Container(
                  decoration: BoxDecoration(
                    border: Border(
                      bottom: BorderSide(color: Color(0xFFe3e3e3)),
                    ),
                  ),
                  child: ListTile(
                    onTap: () => testAppFun(e),
                    title: Text(e),
                  ),
                ),
              )
              .toList(),
        );
      },
    );
  }

  @override
  Widget build(BuildContext context) {
    return LayoutBuilder(
      builder: (context, constraints) {
        // 显示悬浮按钮
        WidgetsBinding.instance
            .addPostFrameCallback((_) => _insertOverlay(context));
        return widget.child;
      },
    );
  }

  // 悬浮按钮，可以拖拽（可自定义样式）
  void _insertOverlay(BuildContext context) {
    return Overlay.of(context).insert(
      OverlayEntry(builder: (context) {
        final size = MediaQuery.of(context).size;
        print(size.width);
        return Positioned(
          top: draggable ? moveOffset.dy : size.height - 102,
          left: draggable ? moveOffset.dx : size.width - 72,
          child: GestureDetector(
            // 移动开始
            onPanStart: (DragStartDetails details) {
              setState(() {
                lastStartOffset = details.globalPosition;
                draggable = true;
              });
              if (count <= 1) {
                count++;
              }
            },
            // 移动中
            onPanUpdate: (DragUpdateDetails details) {
              setState(() {
                moveOffset =
                    details.globalPosition - lastStartOffset + idleOffset;
                if (count > 1) {
                  moveOffset = Offset(max(0, moveOffset.dx), moveOffset.dy);
                } else {
                  moveOffset = Offset(max(0, moveOffset.dx + (size.width - 72)),
                      moveOffset.dy + (size.height - 102));
                }
              });
            },
            // 移动结束
            onPanEnd: (DragEndDetails detail) {
              setState(() {
                idleOffset = moveOffset * 1;
              });
            },
            child: TestContainer(
              onPress: () => showTestList(),
            ),
          ),
        );
      }),
    );
  }
}

// 悬浮按钮的样式
class TestContainer extends StatelessWidget {
  final Function onPress;
  TestContainer({this.onPress});
  @override
  Widget build(BuildContext context) {
    return Material(
      color: Colors.transparent,
      child: GestureDetector(
        onTap: onPress,
        child: Container(
          width: 56,
          height: 56,
          alignment: Alignment.center,
          decoration: BoxDecoration(
            shape: BoxShape.circle,
            color: Colors.green[600],
          ),
          child: Text(
            "Test",
            style: TextStyle(
              color: Colors.white,
              fontWeight: FontWeight.bold,
            ),
          ),
        ),
      ),
    );
  }
}

```

## 1.全局悬浮按钮

这里我们用的是flutter自带的material库中的Overlay组件，具体使用方法如下：

```dart
void _insertOverlay(BuildContext context) {
    return Overlay.of(context).insert(
      OverlayEntry(builder: (context) {
        final size = MediaQuery.of(context).size;
        print(size.width);
        return Positioned(
          top: draggable ? moveOffset.dy : size.height - 102,
          left: draggable ? moveOffset.dx : size.width - 72,
          child: GestureDetector(
            // 移动开始
            onPanStart: (DragStartDetails details) {
              setState(() {
                lastStartOffset = details.globalPosition;
                draggable = true;
              });
              if (count <= 1) {
                count++;
              }
            },
            // 移动中
            onPanUpdate: (DragUpdateDetails details) {
              setState(() {
                moveOffset =
                    details.globalPosition - lastStartOffset + idleOffset;
                if (count > 1) {
                  moveOffset = Offset(max(0, moveOffset.dx), moveOffset.dy);
                } else {
                  moveOffset = Offset(max(0, moveOffset.dx + (size.width - 72)),
                      moveOffset.dy + (size.height - 102));
                }
              });
            },
            // 移动结束
            onPanEnd: (DragEndDetails detail) {
              setState(() {
                idleOffset = moveOffset * 1;
              });
            },
            child: TestContainer(
              onPress: () => showTestList(),
            ),
          ),
        );
      }),
    );
  }
```

