---
title: "《在Flutter中的Sliver》"
subtitle: ""
date: 2021-05-26T19:34:59+08:00
lastmod: 2021-05-26T19:34:59+08:00
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



> 本文主要介绍

<!--more-->



```
CustomScrollView( ///视窗
        slivers: <Widget>[
          //转接器
SliverToBoxAdapter(child: FlutterLogo(
  size: 500,
),),SliverToBoxAdapter(child: FlutterLogo(
  size: 500,
),)


        
        ],
      )
```



```
 CustomScrollView(
        ///视窗
        slivers: <Widget>[
          //转接器

          SliverToBoxAdapter(
            child: Text("表头"),
          ),
          SliverToBoxAdapter(
            child: Text("表头2"),
          ),
          SliverToBoxAdapter(
            child: FlutterLogo(),
          ),
          SliverList(delegate: SliverChildBuilderDelegate((con, index) {
            return Container(
              height: 200,
              color: Colors.accents[index % 200],
            );
          }))
        ],
      )
```



```
CustomScrollView(
        ///视窗
        slivers: <Widget>[
          //转接器

          SliverToBoxAdapter(
            child: Text("表头"),
          ),
          SliverToBoxAdapter(
            child: Text("表头2"),
          ),
          SliverGrid(
              delegate: SliverChildBuilderDelegate((con, index) {
                return Container(
                  height: 200,
                  color: Colors.accents[index % 18],
                );
              },childCount: 23),
              gridDelegate:
                  SliverGridDelegateWithFixedCrossAxisCount(crossAxisCount: 4)),
          SliverList(delegate: SliverChildBuilderDelegate((con, index) {
            return Container(
              height: 200,
              color: Colors.primaries[index % Colors.primaries.length],
            );
          }))
        ],
      )
```





用按钮的边框实现读秒功能https://dartpad.dev/517c90856bcb43c00e9367a71b75fd58

```dart
import 'dart:math';
import 'dart:ui' as ui;

import 'package:flutter/material.dart';
import 'package:flutter/rendering.dart';

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
        visualDensity: VisualDensity.adaptivePlatformDensity,
      ),
      home: MyHomePage(
        title: 'Lint',
      ),
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
  @override
  void initState() {
    super.initState();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text(widget.title),
      ),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: <Widget>[
            Countdown(
              radius: 35.0,
              width: 180.0,
              height: 70.0,
              duration: Duration(seconds: 60),
            ),
            SizedBox(height: 40),
            Countdown(
              radius: 20.0,
              width: 180.0,
              height: 70.0,
              duration: Duration(seconds: 10),
            ),
            SizedBox(height: 40),
            Countdown(
              radius: 0.0,
              width: 180.0,
              height: 70.0,
              duration: Duration(seconds: 60),
            ),
          ],
        ),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: () {},
        tooltip: 'Increment',
        child: Icon(Icons.add),
      ),
    );
  }
}

enum Status { ready, animating, end }

/// 这里还有2中情况还没考虑进去，后续有需要在写，目前能满足视频需求
class Countdown extends StatefulWidget {
  const Countdown({this.radius = 0.0, @required this.width, @required this.height, @required this.duration});

  final double radius;
  final double width;
  final double height;
  final Duration duration;

  @override
  _CountdownState createState() => _CountdownState();
}

class _CountdownState extends State<Countdown> with SingleTickerProviderStateMixin {
  AnimationController animationController;

  /// 矩形顶部横线线 ，从中间开始到结束
  Animation<double> topEndLineAnimation;

  /// 矩形右上角圆角
  Animation<double> topRightArcAnimation;

  /// 矩形右边竖线
  Animation<double> rightLineAnimation;

  /// 矩形右下角圆角
  Animation<double> bottomRightArcAnimation;

  /// 矩形底部横线
  Animation<double> bottomLineAnimation;

  /// 矩形左下角圆角
  Animation<double> bottomLeftArcAnimation;

  /// 矩形左边竖线
  Animation<double> leftLineAnimation;

  /// 矩形左上角圆角
  Animation<double> topLeftArcAnimation;

  /// 矩形顶部横线线 ，从0开始到中间
  Animation<double> startTopLineAnimation;

  /// 圆形
  Animation<double> circleAnimation;

  /// 当前按钮状态
  Status _status = Status.ready;

  /// 按钮半径
  double radius = 0.0;

  /// 按钮宽
  double width = 0.0;

  /// 按钮高
  double height = 0.0;

  /// 绘制动画时间
  Duration duration = const Duration(seconds: 0);

  /// 是否是矩形
  bool isRectangle() {
    return widget.radius <= 0;
  }

  /// 是否是圆形
  bool isCircle() {
    return widget.height == widget.width && widget.radius >= widget.height / 2;
  }

  /// 是否是圆角矩形
  bool isArcRectangle() {
    return widget.radius > 0 && widget.height / 2 > widget.radius && widget.width / 2 > widget.radius;
  }

  /// 是否上下整个圆角
  bool isHeightArc() {
    return widget.radius >= widget.width / 2 && widget.height > widget.width;
  }

  /// 是否左右整个圆角
  bool isWidthArc() {
    return widget.radius >= widget.height / 2 && widget.width > widget.height;
  }

  /// 是否上下左右都是圆角
  bool isBothArc() {
    return widget.radius >= widget.width / 2 &&
        widget.radius >= widget.height / 2 &&
        (widget.radius < widget.width || widget.radius < widget.height);
  }

  double circleLength() {
    return 2 * pi * _radius();
  }

  double arcLength() {
    return circleLength() / 4;
  }

  double squareLength() {
    return 2 * (widget.height + widget.width);
  }

  double arcSquareLength() {
    final double longLength = (widget.height - 2 * widget.radius) * 2;
    final double widthLength = (widget.width - 2 * widget.radius) * 2;
    return longLength + widthLength + circleLength();
  }

  /// yuan
  double arcWidthSquareLength() {
    final double longLength = (widget.width - 2 * widget.radius) * 2;
    return longLength + circleLength();
  }

  double _radius() {
    if (isCircle()) {
      if (widget.radius > widget.height / 2 || widget.radius > widget.width / 2) {
        return widget.height / 2;
      }
    }
    if (isWidthArc()) {
      return widget.height / 2;
    }
    return widget.radius ?? 0;
  }

  @override
  void initState() {
    super.initState();
    width = widget.width;
    height = widget.height;
    radius = widget.radius;
    duration = widget.duration;
    animationController = AnimationController(vsync: this)..duration = widget.duration ?? const Duration(seconds: 5);
    _initAnimate();
  }

  @override
  void didUpdateWidget(covariant Countdown oldWidget) {
    width = widget.width;
    height = widget.height;
    radius = widget.radius;
    duration = widget.duration;
    super.didUpdateWidget(oldWidget);
    if (oldWidget != widget) {
      _initAnimate();
    }
  }

  void _initAnimate() {
    animationController.addListener(() {
      setState(() {
        if (animationController.isCompleted) {
          _status = Status.end;
        }
      });
    });

    if (isCircle()) {
      circleAnimation = Tween<double>(begin: 0.0, end: 1.0).animate(animationController);
    } else if (isRectangle()) {
      final double totalLength = squareLength();
      final double widthTime = width / totalLength / 2;
      final double heightTime = height / totalLength;
      topEndLineAnimation = Tween<double>(begin: 0.0, end: 1.0)
          .animate(CurvedAnimation(parent: animationController, curve: Interval(0, widthTime)));
      rightLineAnimation = Tween<double>(begin: 0.0, end: 1.0)
          .animate(CurvedAnimation(parent: animationController, curve: Interval(widthTime, widthTime + heightTime)));
      bottomLineAnimation = Tween<double>(begin: 0.0, end: 1.0).animate(CurvedAnimation(
          parent: animationController, curve: Interval(widthTime + heightTime, widthTime * 3 + heightTime)));
      leftLineAnimation = Tween<double>(begin: 0.0, end: 1.0).animate(CurvedAnimation(
          parent: animationController, curve: Interval(widthTime * 3 + heightTime, widthTime * 3 + heightTime * 2)));
      startTopLineAnimation = Tween<double>(begin: 0.0, end: 1.0)
          .animate(CurvedAnimation(parent: animationController, curve: Interval(widthTime * 3 + heightTime * 2, 1)));
    } else if (isWidthArc()) {
      final double totalLength = arcWidthSquareLength();
      final double startLineTime = (widget.width - 2 * widget.radius) / 2 / totalLength;
      final double arcTime = circleLength() / 2 / totalLength;
      topEndLineAnimation = Tween<double>(begin: 0.0, end: 1.0)
          .animate(CurvedAnimation(parent: animationController, curve: Interval(0.0, startLineTime)));
      topRightArcAnimation = Tween<double>(begin: 0.0, end: 1.0).animate(
          CurvedAnimation(parent: animationController, curve: Interval(startLineTime, startLineTime + arcTime)));
      bottomLineAnimation = Tween<double>(begin: 0.0, end: 1.0).animate(CurvedAnimation(
          parent: animationController, curve: Interval(startLineTime + arcTime, startLineTime * 3 + arcTime)));
      topLeftArcAnimation = Tween<double>(begin: 0.0, end: 1.0).animate(CurvedAnimation(
          parent: animationController, curve: Interval(startLineTime * 3 + arcTime, startLineTime * 3 + arcTime * 2)));
      startTopLineAnimation = Tween<double>(begin: 0.0, end: 1.0)
          .animate(CurvedAnimation(parent: animationController, curve: Interval(startLineTime * 3 + arcTime * 2, 1.0)));
    } else {
      final double totalLength = arcSquareLength();
      final double leftLineTime = (widget.height - 2 * widget.radius) / totalLength;
      final double topLineTime = (widget.width - 2 * widget.radius) / 2 / totalLength;
      final double arcTime = arcLength() / totalLength;
      topEndLineAnimation = Tween<double>(begin: 0.0, end: 1.0)
          .animate(CurvedAnimation(parent: animationController, curve: Interval(0.0, topLineTime)));
      topRightArcAnimation = Tween<double>(begin: 0.0, end: 1.0)
          .animate(CurvedAnimation(parent: animationController, curve: Interval(topLineTime, topLineTime + arcTime)));
      rightLineAnimation = Tween<double>(begin: 0.0, end: 1.0).animate(CurvedAnimation(
          parent: animationController, curve: Interval(topLineTime + arcTime, topLineTime + arcTime + leftLineTime)));
      bottomRightArcAnimation = Tween<double>(begin: 0.0, end: 1.0).animate(CurvedAnimation(
          parent: animationController,
          curve: Interval(topLineTime + arcTime + leftLineTime, topLineTime + arcTime * 2 + leftLineTime)));
      bottomLineAnimation = Tween<double>(begin: 0.0, end: 1.0).animate(CurvedAnimation(
          parent: animationController,
          curve: Interval(topLineTime + arcTime * 2 + leftLineTime, topLineTime * 3 + arcTime * 2 + leftLineTime)));
      bottomLeftArcAnimation = Tween<double>(begin: 0.0, end: 1.0).animate(CurvedAnimation(
          parent: animationController,
          curve: Interval(topLineTime * 3 + arcTime * 2 + leftLineTime, topLineTime * 3 + arcTime * 3 + leftLineTime)));
      leftLineAnimation = Tween<double>(begin: 0.0, end: 1.0).animate(CurvedAnimation(
          parent: animationController,
          curve: Interval(
              topLineTime * 3 + arcTime * 3 + leftLineTime, topLineTime * 3 + arcTime * 3 + leftLineTime * 2)));
      topLeftArcAnimation = Tween<double>(begin: 0.0, end: 1.0).animate(CurvedAnimation(
          parent: animationController,
          curve: Interval(
              topLineTime * 3 + arcTime * 3 + leftLineTime * 2, topLineTime * 3 + arcTime * 4 + leftLineTime * 2)));
      startTopLineAnimation = Tween<double>(begin: 0.0, end: 1.0).animate(CurvedAnimation(
          parent: animationController, curve: Interval(topLineTime * 3 + arcTime * 4 + leftLineTime * 2, 1.0)));
    }
  }

  void _changeStatus() {
    switch (_status) {
      case Status.ready:
        setState(() {
          _status = Status.animating;
          animationController.forward();
        });

        break;
      case Status.animating:
        setState(() {
          _status = Status.ready;
          animationController.reset();
        });
        break;
      case Status.end:
        break;
    }
  }

  @override
  Widget build(BuildContext context) {
    return Container(
      child: GestureDetector(
        onTap: _changeStatus,
        child: Container(
          width: widget.width,
          height: widget.height,
          child: CustomPaint(
            painter: CountdownPainter(
              isRectangle: isRectangle(),
              isBothArc: isBothArc(),
              isWidthArc: isWidthArc(),
              isHeightArc: isHeightArc(),
              isCircle: isCircle(),
              isArcRectangle: isArcRectangle(),
              status: _status,
              endTopLine: topEndLineAnimation?.value ?? 0,
              topRightArc: topRightArcAnimation?.value ?? 0,
              rightLine: rightLineAnimation?.value ?? 0,
              bottomRightArc: bottomRightArcAnimation?.value ?? 0,
              bottomLine: bottomLineAnimation?.value ?? 0,
              bottomLeftArc: bottomLeftArcAnimation?.value ?? 0,
              leftLine: leftLineAnimation?.value ?? 0,
              topLeftArc: topLeftArcAnimation?.value ?? 0,
              startTopLine: startTopLineAnimation?.value ?? 0,
              radius: _radius(),
              circle: circleAnimation?.value ?? 0,
            ),
          ),
        ),
      ),
    );
  }
}

/// 自定义绘画控件
class CountdownPainter extends CustomPainter {
  CountdownPainter({
    this.isHeightArc = false,
    this.isWidthArc = false,
    this.isRectangle = true,
    this.isCircle = false,
    this.isBothArc = false,
    this.isArcRectangle = false,
    this.status = Status.ready,
    this.endTopLine = 0.0,
    this.topRightArc = 0.0,
    this.rightLine = 0.0,
    this.bottomRightArc = 0.0,
    this.bottomLine = 0.0,
    this.bottomLeftArc = 0.0,
    this.leftLine = 0.0,
    this.topLeftArc = 0.0,
    this.startTopLine = 0.0,
    this.circle = 0.0,
    this.radius = 0.0,
  });

  /// 是否圆形
  final bool isCircle;

  /// 是否矩形
  final bool isRectangle;
  final bool isHeightArc;
  final bool isWidthArc;
  final bool isBothArc;

  /// 是否圆角矩形
  final bool isArcRectangle;
  final double radius;
  final double endTopLine;
  final double topRightArc;
  final double rightLine;
  final double bottomRightArc;
  final double bottomLine;
  final double bottomLeftArc;
  final double leftLine;
  final double topLeftArc;
  final double startTopLine;
  final double circle;

  final Status status;

  final Paint paintArc = Paint()
    ..style = PaintingStyle.stroke
    ..color = Colors.grey
    ..strokeWidth = 3;

  final Paint rectPaint = Paint()..strokeWidth = 3;

  Color _rectColor() {
    return status == Status.end ? Colors.grey : Colors.blue;
  }

  PaintingStyle _rectPaintingStyle() {
    return status == Status.ready ? PaintingStyle.fill : PaintingStyle.stroke;
  }

  /// 绘制矩形
  void _drawRect(Canvas canvas, Size size) {
    rectPaint
      ..color = _rectColor()
      ..style = _rectPaintingStyle();
    final RRect outRect = RRect.fromLTRBR(0, 0, size.width, size.height, Radius.circular(radius));
    canvas.drawRRect(outRect, rectPaint);
  }

  Color _textColor() {
    switch (status) {
      case Status.animating:
        return Colors.blue;
      case Status.end:
        return Colors.grey;
      case Status.ready:
      default:
        return Colors.white;
    }
  }

  String _text() {
    switch (status) {
      case Status.animating:
        return '取消';
      case Status.end:
        return '完成';
      case Status.ready:
      default:
        return '开始';
    }
  }

  /// 绘制文字
  void _drawText(Canvas canvas, Size size) {
    final double width = size.width;
    final double height = size.height;
    final ui.ParagraphBuilder builder = ui.ParagraphBuilder(ui.ParagraphStyle(
      textAlign: TextAlign.center,
      fontWeight: FontWeight.w600,
      fontStyle: FontStyle.normal,
      fontSize: 18,
    ))
      ..pushStyle(ui.TextStyle(color: _textColor()))
      ..addText(_text());
    final ui.ParagraphConstraints constraints = ui.ParagraphConstraints(width: width);
    final ui.Paragraph paragraph = builder.build()..layout(constraints);
    final double x = (width - paragraph.width) / 2;
    final double y = (height - paragraph.height) / 2;
    canvas.drawParagraph(paragraph, Offset(x, y));
  }

  /// 绘制圆
  void _drawCircle(Canvas canvas, Size size) {
    canvas.drawArc(Rect.fromCircle(center: Offset(radius, radius), radius: radius), 2 * pi / 4 * 3, 2 * pi * circle,
        false, paintArc);
  }

  /// 绘制矩形
  void _drawRectangle(Canvas canvas, Size size) {
    final double width = size.width;
    final double height = size.height;
    final double halfWidth = width / 2;
    if (endTopLine > 0) {
      canvas.drawLine(Offset(halfWidth, 0), Offset(halfWidth + halfWidth * endTopLine, 0), paintArc);
    }
    if (rightLine > 0) {
      canvas.drawLine(Offset(width, 0), Offset(width, height * rightLine), paintArc);
    }
    if (bottomLine > 0) {
      canvas.drawLine(Offset(width, height), Offset(width - width * bottomLine, height), paintArc);
    }
    if (leftLine > 0) {
      canvas.drawLine(Offset(0, height), Offset(0, height - height * leftLine), paintArc);
    }
    if (startTopLine > 0) {
      canvas.drawLine(Offset.zero, Offset(halfWidth * startTopLine, 0), paintArc);
    }
  }

  /// 绘制圆角矩形
  void _drawArcRectangle(Canvas canvas, Size size) {
    final double width = size.width;
    final double height = size.height;
    final double halfWidth = width / 2;
    final double internalWidth = width - radius;
    final double internalHeight = height - radius;
    final double sweepAngle = pi / 2;

    // 矩形顶部横线，从中间开始
    if (endTopLine > 0) {
      canvas.drawLine(Offset(halfWidth, 0), Offset(halfWidth + (halfWidth - radius) * endTopLine, 0), paintArc);
    }

    // 右上角圆角
    if (topRightArc > 0) {
      canvas.drawArc(Rect.fromCircle(center: Offset(internalWidth, radius), radius: radius), 2 * pi / 4 * 3,
          sweepAngle * topRightArc, false, paintArc);
    }

    // 矩形右边竖线
    if (rightLine > 0) {
      canvas.drawLine(Offset(width, radius), Offset(width, radius + (height - 2 * radius) * rightLine), paintArc);
    }

    // 右下角圆角
    if (bottomRightArc > 0) {
      canvas.drawArc(Rect.fromCircle(center: Offset(internalWidth, internalHeight), radius: radius), 0,
          sweepAngle * bottomRightArc, false, paintArc);
    }

    // 矩形底部横线
    if (bottomLine > 0) {
      canvas.drawLine(
          Offset(internalWidth, height), Offset(internalWidth - ((width - 2 * radius) * bottomLine), height), paintArc);
    }

    // 左下角圆角
    if (bottomLeftArc > 0) {
      canvas.drawArc(Rect.fromCircle(center: Offset(radius, internalHeight), radius: radius), pi / 2,
          sweepAngle * bottomLeftArc, false, paintArc);
    }

    // 矩形左边竖线
    if (leftLine > 0) {
      canvas.drawLine(
          Offset(0, internalHeight), Offset(0, internalHeight - ((height - 2 * radius) * leftLine)), paintArc);
    }

    // 左上角圆角
    if (topLeftArc > 0) {
      canvas.drawArc(Rect.fromCircle(center: Offset(radius, radius), radius: radius), pi, sweepAngle * topLeftArc,
          false, paintArc);
    }

    // 矩形顶部横线，从0开始到中间
    if (startTopLine > 0) {
      canvas.drawLine(Offset(radius, 0), Offset(radius + (width - 2 * radius) / 2 * startTopLine, 0), paintArc);
    }
  }

  /// 绘制左右整个圆角矩形
  void _drawWidthArcRectangle(Canvas canvas, Size size) {
    final double width = size.width;
    final double height = size.height;
    final double halfWidth = width / 2;
    final double halfHeight = height / 2;
    final double internalWidth = width - radius;
    final double sweepAngle = pi;

    // 矩形顶部横线，从中间开始
    if (endTopLine > 0) {
      canvas.drawLine(Offset(halfWidth, 0), Offset(halfWidth + (halfWidth - radius) * endTopLine, 0), paintArc);
    }

    // 右上角圆角
    if (topRightArc > 0) {
      canvas.drawArc(Rect.fromCircle(center: Offset(internalWidth, halfHeight), radius: radius), 2 * pi / 4 * 3,
          sweepAngle * topRightArc, false, paintArc);
    }
    // 矩形底部横线
    if (bottomLine > 0) {
      canvas.drawLine(
          Offset(internalWidth, height), Offset(internalWidth - ((width - 2 * radius) * bottomLine), height), paintArc);
    }

    // 左上角圆角
    if (topLeftArc > 0) {
      canvas.drawArc(Rect.fromCircle(center: Offset(radius, halfHeight), radius: radius), pi / 2,
          sweepAngle * topLeftArc, false, paintArc);
    }

    // 矩形顶部横线，从0开始到中间
    if (startTopLine > 0) {
      canvas.drawLine(Offset(radius, 0), Offset(radius + (width - 2 * radius) / 2 * startTopLine, 0), paintArc);
    }
  }

  @override
  void paint(Canvas canvas, Size size) {
    // 绘制圆角矩形
    _drawRect(canvas, size);

    // 绘制文字文字
    _drawText(canvas, size);

    if (status == Status.animating) {
      // 绘制圆
      if (isCircle) {
        _drawCircle(canvas, size);
        return;
      }
      // 绘制矩形
      if (isRectangle) {
        _drawRectangle(canvas, size);
        return;
      }

      // 绘制圆角矩形
      if (isArcRectangle) {
        _drawArcRectangle(canvas, size);
        return;
      }

      // 绘制左右整个圆角矩形
      if (isWidthArc) {
        _drawWidthArcRectangle(canvas, size);
        return;
      }

      // 绘制上下整个圆角矩形

      // 绘制上下左右圆角矩形

    }
  }

  @override
  bool shouldRepaint(covariant CustomPainter oldDelegate) {
    return true;
  }
}

```







法尔

```
import 'dart:math';
import 'dart:ui';

import 'package:flutter/material.dart';
import 'package:flutter/rendering.dart';
import 'package:http/http.dart' as http;
import 'dart:convert' as convert;

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      theme: ThemeData(
        primarySwatch: Colors.blue,
        visualDensity: VisualDensity.adaptivePlatformDensity,
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
  bool _zoomed = false; //缩放
  @override
  Widget build(BuildContext context) {
    return Scaffold(
        appBar: AppBar(
          title: Text("计时器"),
        ),
        body: Center(
          child: Transform.scale(
            scale: _zoomed ? 2.8 : 1.0,
            child: Column(
              mainAxisAlignment: MainAxisAlignment.center,
              children: [
                CountdownButton(
                  duration: Duration(
                    seconds: 5,
                  ),
                  width: 150,
                  height: 40,
                  radius: 20,
                ),
                const SizedBox(
                  height: 24,
                ),
                CountdownButton(
                  duration: Duration(
                    seconds: 60,
                  ),
                  width: 80,
                  height: 80,
                  radius: 40,
                ),
                const SizedBox(
                  height: 24,
                ),
                CountdownButton(
                  duration: Duration(
                    seconds: 5,
                  ),
                  width: 80,
                  height: 40,
                  radius: 0,
                ),
              ],
            ),
          ),
        ));
  }
}

class CountdownButton extends StatefulWidget {
  final Duration duration;
  final double width, height, radius;
  CountdownButton(
      {Key key, this.duration, this.height, this.radius, this.width})
      : super(key: key);

  @override
  _CountdownButtonState createState() => _CountdownButtonState();
}

class _CountdownButtonState extends State<CountdownButton>
    with SingleTickerProviderStateMixin {
  AnimationController _controller; // late AnimationController _controller;
  int _stage = 0;

  @override
  void initState() {
    // TODO: implement initState
    super.initState();
    _controller = AnimationController(vsync: this, duration: widget.duration)
      ..addStatusListener((status) {
        if (status == AnimationStatus.completed) {}

        setState(() {
          _stage = 2;
        });
      });
  }

  @override
  void dispose() {
    // TODO: implement dispose
    _controller.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    String label;
    Color textColor;
    Color bgColor;

    switch (_stage) {
      case 0:
        label = "send";
        textColor = Colors.white;
        bgColor = Colors.blue;

        break;
      case 1:
        label = "canel";
        // textColor = Colors.white;
        // bgColor = Colors.blue;

        break;
      case 2:
        label = "done";
        textColor = Colors.grey;
        // bgColor = Colors.blue;

        break;
      default:
    }
    return Stack(
      children: [
        CustomPaint(
          painter: BorderPainer(
            buttonWidth: widget.width,
            buttonHeight: widget.height,
            buttonRadius: widget.radius,
            animation: _controller,
          ),
        ),
        SizedBox(
          width: widget.width,
          height: widget.height,
          child: Container(
            decoration: BoxDecoration(
              borderRadius: BorderRadius.circular(widget.radius),
            ),
            child: FlatButton(
              textColor: textColor,
              focusColor: bgColor,
              onPressed: () {
                if (_stage == 0) {
                  _controller.forward(from: 0.0);

                  setState(() {
                    _stage = 1;
                  });
                } else {
                  _controller.reset();
                  setState(() {
                    _stage = 0;
                  });
                }
              },
              child: Text(label),
            ),
          ),
        )
      ],
    );
  }
}

class BorderPainer extends CustomPainter {
  final double buttonWidth;
  final double buttonHeight;
  final double buttonRadius;
  final Animation<double> animation;

  final Paint bluePaint = Paint()
    ..style = PaintingStyle.stroke
    ..strokeWidth = 2.0
    ..color = Colors.blue;
  final Paint greyaint = Paint()
    ..style = PaintingStyle.stroke
    ..strokeWidth = 2.0
    ..color = Colors.red;
  RRect shape; //late

  PathMetric pathMetric; //late

  BorderPainer(
      {this.buttonWidth, this.buttonHeight, this.buttonRadius, this.animation})
      : super(repaint: animation) {
    shape = RRect.fromRectAndRadius(
        Rect.fromCenter(
            center: Offset.zero, width: buttonWidth, height: buttonHeight),
        Radius.circular(buttonRadius));

    final path = Path()..addRRect(shape);
    pathMetric = path.computeMetrics().single; //只有1行
  }

  @override
  void paint(Canvas canvas, Size size) {
    final path = Path();
    final totalLength = pathMetric.length - 100; //得到长度

    final currentLength = totalLength * animation.value;
    // path.addPath(pathMetric.extractPath(0, currentLength), Offset.zero); //简单

    final startingPoint =
        totalLength / 4 + max(buttonHeight / 2 - buttonRadius, 0);
    path.addPath(
        pathMetric.extractPath(startingPoint, currentLength + startingPoint),
        Offset.zero);

    canvas.translate(
      buttonWidth / 2,
      buttonHeight / 2,
    );
    canvas.drawRRect(
      shape,
      bluePaint,
    );
    canvas.drawPath(path, greyaint);
  }

  @override
  bool shouldRepaint(BorderPainer oldDelegate) => false;

  // @override
  // bool shouldRebuildSemantics(BorderPainer oldDelegate) => false;
}

```

