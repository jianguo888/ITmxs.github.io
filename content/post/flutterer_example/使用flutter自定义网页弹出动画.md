---
title: "使用flutter自定义网页弹出动画"
date: 2021-09-01T22:14:55+08:00
draft: false
tags: ["Flutter"]
categories: ["Flutter"]
---

> 本文主要介绍使用flutter自定义网页弹出动画

<!--more-->

我们从组件的开头开始，为弹出动画创建一个有状态的类。
我们需要创建一个包含图标文本的容器，以及一个包含堆叠在堆栈中的图标的容器。

让我们构建鼠标悬停在对象上时弹出的动画。动画部分使用“ AnimatedOpacity ”小部件完成，以使其在必要时可见和不可见。“ AnimatedContainer ”小部件用于将对象(小部件)移动(弹出操作)到任何地方()。您还可以随时随地调整位置属性。

```dart
import 'dart:math';

import 'package:flutter/material.dart';
import 'package:flutter_vector_icons/flutter_vector_icons.dart';

void main() {
  runApp(
    MaterialApp(
      home: MyApp(),
    ),
  );
}

class MyApp extends StatefulWidget {
  @override
  _MyAppState createState() => _MyAppState();
}

class _MyAppState extends State<MyApp> {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Container(
        height: MediaQuery.of(context).size.height,
        width: MediaQuery.of(context).size.width,
        color: Colors.black87,
        child: Row(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            PopupIcon(
              icon: FontAwesome.instagram,
              color: Color(0xffe1306c),
              text: 'Instagram',
            ),
            PopupIcon(
              icon: FontAwesome.github,
              color: Color(0xff333333),
              text: 'GitHub',
            ),
            PopupIcon(
              icon: FontAwesome.telegram,
              color: Color(0xff0088cc),
              text: 'Telegram',
            ),
            PopupIcon(
              icon: FontAwesome.youtube_play,
              color: Color(0xffde463b),
              text: 'Youtube',
            ),
            PopupIcon(
              icon: FontAwesome.linkedin,
              color: Color(0xff0e76a8),
              text: 'LinkedIn',
            ),
          ],
        ),
      ),
    );
  }
}

class PopupIcon extends StatefulWidget {
  final IconData icon;
  final Color color;
  final String text;

  const PopupIcon({
    Key key,
    this.icon,
    this.color,
    this.text,
  }) : super(key: key);

  @override
  _PopupIconState createState() => _PopupIconState();
}

class _PopupIconState extends State<PopupIcon> {
  bool onIt = false;
  @override
  Widget build(BuildContext context) {
    return Container(
      margin: EdgeInsets.all(5.0),
      height: 140.0,
      width: 100.0,
      child: Center(
        child: Stack(
          children: [
            AnimatedOpacity(
              opacity: onIt ? 1.0 : 0.0,
              curve: onIt ? Curves.easeOutBack : Curves.easeIn,
              duration: Duration(milliseconds: 200),
              child: AnimatedAlign(
                curve: onIt ? Curves.easeOutBack : Curves.easeIn,
                alignment: onIt ? Alignment(0.0, -1.0) : Alignment(0.0, 0.0),
                duration: Duration(milliseconds: 200),
                child: Stack(
                  clipBehavior: Clip.none,
                  children: [
                    Positioned(
                      left: 45.0,
                      top: 30.0,
                      child: Transform(
                        transform: Matrix4.rotationZ(pi / 4),
                        alignment: FractionalOffset.center,
                        child: Container(
                          height: 10.0,
                          width: 10.0,
                          color: widget.color,
                        ),
                      ),
                    ),
                    Container(
                      height: 35.0,
                      width: 100.0,
                      decoration: BoxDecoration(
                        borderRadius: BorderRadius.circular(10.0),
                        color: widget.color,
                      ),
                      child: Center(
                        child: Text(
                          widget.text,
                          style: TextStyle(
                            color: Colors.white,
                            fontSize: 16.0,
                            fontWeight: FontWeight.w500,
                          ),
                        ),
                      ),
                    ),
                  ],
                ),
              ),
            ),
            Align(
              child: MouseRegion(
                onEnter: (value) {
                  setState(() {
                    onIt = true;
                  });
                },
                onExit: (value) {
                  setState(() {
                    onIt = false;
                  });
                },
                child: AnimatedContainer(
                  height: 50.0,
                  width: 50.0,
                  duration: Duration(milliseconds: 375),
                  decoration: BoxDecoration(
                    color: onIt ? widget.color : Colors.white,
                    borderRadius: BorderRadius.circular(50.0),
                  ),
                  child: Icon(
                    widget.icon,
                    color: onIt ? Colors.white : widget.color,
                  ),
                ),
              ),
            ),
          ],
        ),
      ),
    );
  }
}
```

