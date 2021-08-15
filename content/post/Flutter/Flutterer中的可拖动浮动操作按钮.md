---
title: "Flutterer中的可拖动浮动操作按钮"
date: 2021-08-15T19:06:10+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---

**lutter**允许您使用 FloatingActionButton 小部件添加浮动操作按钮。尽管如此，它不允许您拖动按钮。考虑您需要使其可拖动的可能性。这篇文章有一个模型，它揭示了你需要做什么来制作一个浮动的动作按钮，只要它在父小部件内，就可以在屏幕周围的任何地方拖拽。

在在这篇博客中，我们将探索**Flutter 中**的**Draggable Floating Action Button**。 我们将看到如何实现可拖动浮动操作按钮的演示程序，并展示如何在您的 Flutter 应用程序中创建它。

[TOC]



# 介绍：

下面的演示视频展示了如何在 flutter 中创建可拖动的浮动操作按钮。它展示了可拖动的浮动操作按钮如何在您的 Flutter 应用程序中工作。它显示代码何时成功运行，然后用户将浮动操作按钮拖动到屏幕周围的任何位置，只要它在父小部件内即可。它将显示在您的设备上。

> *演示模块：*

![img](https://miro.medium.com/freeze/max/26/1*r_zdnL6TD64krL8XrfY_FA.gif?q=20)

![img](https://miro.medium.com/max/621/1*r_zdnL6TD64krL8XrfY_FA.gif)

# 如何在 dart 文件中实现代码：

您需要分别在您的代码中实现它：

> `*main.dart*`*在**文件夹**内**创建一个新的 dart 文件*`*lib*`*。*

首先，我们将创建一个 Globalkey 并将其命名为 _parentKey

```dart
final GlobalKey _parentKey = GlobalKey();
```

在正文中，我们将添加一个具有高度和宽度的 Container 小部件。这是子属性，我们将添加 Stack 小部件。在这个小部件中，我们将添加一个键、文本和一个**DraggableFloatingActionButton()**。在按钮内部，我们将添加一个具有高度和宽度的容器。在其子属性上添加图像。此外，我们将添加 initialOffset、父键和 onPressed。我们将深入定义下面的代码。

```dart
Container(
  width: 300,
  height: 300,
  child: Stack(
    key: _parentKey,
    children: [
      Container(color: Colors.cyan),
      Center(
        child: const Text(
          "FlutterDev's.com",
          style: const TextStyle(color: Colors.white, fontSize: 24),
        ),
      ),
      DraggableFloatingActionButton(
        child: Container(
          width: 60,
          height: 60,
          decoration: ShapeDecoration(
            shape: CircleBorder(),
            color: Colors.white,
          ),
          child: Image.asset("assets/logo.png"),
        ),
        initialOffset: const Offset(120, 70),
        parentKey: _parentKey,
        onPressed: () {},
      ),
    ],
  ),
)
```

> `*draggable_floating_action_button.dart*`*在**文件夹**内**创建一个新的 dart 文件*`*lib*`*。*

我们将为这样的小部件创建一个类。我们需要处理的主要事情是使按钮在指针之后可拖动的能力。可以使用的小部件之一是侦听器，它可以识别指针移动事件并提供移动细节。本质上，按钮应该被包装为 Listener 的孩子。

该`Listener`具有`onPointerMove`可被利用来传递当指针移动，这将被认为是一个回呼的争用。回调函数应该有一个边界 PointerMoveEvent，它包含 x 和 y 标题（delta.dx 和 delta.dy）中的发展增量。捕获的偏移量应由移动增量刷新。

以下是用于制作可拖动浮动操作按钮的类。它有一些包含`child`、initialOffset 和 onPressed 的争用。该`child`部件交付利用被定位小部件依赖于电流偏移。它另外包装为 Listener 小部件的子级。还有一个策略 _updatePosition 可以根据移动增量刷新当前偏移量。

```
class DraggableFloatingActionButton extends StatefulWidget {

  final Widget child;
  final Offset initialOffset;
  final VoidCallback onPressed;

  DraggableFloatingActionButton({
    required this.child,
    required this.initialOffset,
    required this.onPressed,
  });

  @override
  State<StatefulWidget> createState() => _DraggableFloatingActionButtonState();
}

class _DraggableFloatingActionButtonState extends State<DraggableFloatingActionButton> {

  bool _isDragging = false;
  late Offset _offset;

  @override
  void initState() {
    super.initState();
    _offset = widget.initialOffset;
  }

  void _updatePosition(PointerMoveEvent pointerMoveEvent) {
    double newOffsetX = _offset.dx + pointerMoveEvent.delta.dx;
    double newOffsetY = _offset.dy + pointerMoveEvent.delta.dy;

    setState(() {
      _offset = Offset(newOffsetX, newOffsetY);
    });
  }

  @override
  Widget build(BuildContext context) {
    return Positioned(
      left: _offset.dx,
      top: _offset.dy,
      child: Listener(
        onPointerMove: (PointerMoveEvent pointerMoveEvent) {
          _updatePosition(pointerMoveEvent);

          setState(() {
            _isDragging = true;
          });
        },
        onPointerUp: (PointerUpEvent pointerUpEvent) {
          print('onPointerUp');

          if (_isDragging) {
            setState(() {
              _isDragging = false;
            });
          } else {
            widget.onPressed();
          }
        },
        child: widget.child,
      ),
    );
  }
}
```

您需要向父小部件添加一个键并将其传递给`DraggableFloatingActionButton`小部件。从key中，可以从`currentContext`property中获取RenderBox，其中有findRenderObject一个策略。然后，此时，您可以从 RenderBox 的 size 属性中获取父级的大小。您应该小心，因为应该在构建树之后调用 findRenderObject 技术。随后，您需要使用 WidgetsBinding 的 addPostFrameCallback 来召唤它。

该`_updatePosition`技术应该被改变了。如果新偏移量低于最小偏移量，则必须将该值设置为最小偏移量。如果新偏移量比最大偏移量更值得注意，则必须将该值设置为最大的最大偏移量。您需要对 x 轴和 y 轴执行此操作。

```dart
class DraggableFloatingActionButton extends StatefulWidget {

  final Widget child;
  final Offset initialOffset;
  final VoidCallback onPressed;

  DraggableFloatingActionButton({
    required this.child,
    required this.initialOffset,
    required this.onPressed,
  });

  @override
  State<StatefulWidget> createState() => _DraggableFloatingActionButtonState();
}

class _DraggableFloatingActionButtonState extends State<DraggableFloatingActionButton> {

  final GlobalKey _key = GlobalKey();

  bool _isDragging = false;
  late Offset _offset;
  late Offset _minOffset;
  late Offset _maxOffset;

  @override
  void initState() {
    super.initState();
    _offset = widget.initialOffset;

    WidgetsBinding.instance?.addPostFrameCallback(_setBoundary);
  }

  void _setBoundary(_) {
    final RenderBox parentRenderBox = widget.parentKey.currentContext?.findRenderObject() as RenderBox;
    final RenderBox renderBox = _key.currentContext?.findRenderObject() as RenderBox;

    try {
      final Size parentSize = parentRenderBox.size;
      final Size size = renderBox.size;

      setState(() {
        _minOffset = const Offset(0, 0);
        _maxOffset = Offset(
            parentSize.width - size.width,
            parentSize.height - size.height
        );
      });
    } catch (e) {
      print('catch: $e');
    }
  }

  void _updatePosition(PointerMoveEvent pointerMoveEvent) {
    double newOffsetX = _offset.dx + pointerMoveEvent.delta.dx;
    double newOffsetY = _offset.dy + pointerMoveEvent.delta.dy;

    if (newOffsetX < _minOffset.dx) {
      newOffsetX = _minOffset.dx;
    } else if (newOffsetX > _maxOffset.dx) {
      newOffsetX = _maxOffset.dx;
    }

    if (newOffsetY < _minOffset.dy) {
      newOffsetY = _minOffset.dy;
    } else if (newOffsetY > _maxOffset.dy) {
      newOffsetY = _maxOffset.dy;
    }

    setState(() {
      _offset = Offset(newOffsetX, newOffsetY);
    });
  }

  @override
  Widget build(BuildContext context) {
    return Positioned(
      left: _offset.dx,
      top: _offset.dy,
      child: Listener(
        onPointerMove: (PointerMoveEvent pointerMoveEvent) {
          _updatePosition(pointerMoveEvent);

          setState(() {
            _isDragging = true;
          });
        },
        onPointerUp: (PointerUpEvent pointerUpEvent) {
          print('onPointerUp');

          if (_isDragging) {
            setState(() {
              _isDragging = false;
            });
          } else {
            widget.onPressed();
          }
        },
        child: Container(
          key: _key,
          child: widget.child,
        ),
      ),
    );
  }
}
```

> *当我们运行应用程序时，我们应该像下面的屏幕截图一样获得屏幕的输出。*

![img](https://miro.medium.com/max/26/1*VSwNFRmAMl67U98uxYdbOg.png?q=20)

![img](https://miro.medium.com/max/700/1*VSwNFRmAMl67U98uxYdbOg.png)

最终输出

# 代码文件：

```dart
import 'package:flutter/cupertino.dart';
import 'package:flutter/material.dart';
import 'package:flutter_draggable_floating/draggable_floating_action_button.dart';
import 'package:flutter_draggable_floating/splash_screen.dart';

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      home: Splash(),
    );
  }
}

class DraggableFloatingActionButtonDemo extends StatelessWidget {

  final GlobalKey _parentKey = GlobalKey();

  @override
  Widget build(BuildContext context) {
    return new Scaffold(
      appBar: AppBar(
        automaticallyImplyLeading: false,
        title: const Text('Flutter Draggable Floating Action Button'),
        backgroundColor: Colors.cyan,
      ),
      body: Column(
        children: [
          Container(
            height: 150,
          ),
          Container(
            width: 300,
            height: 300,
            child: Stack(
              key: _parentKey,
              children: [
                Container(color: Colors.cyan),
                Center(
                  child: const Text(
                    "FlutterDev's.com",
                    style: const TextStyle(color: Colors.white, fontSize: 24),
                  ),
                ),
                DraggableFloatingActionButton(
                  child: Container(
                    width: 60,
                    height: 60,
                    decoration: ShapeDecoration(
                      shape: CircleBorder(),
                      color: Colors.white,
                    ),
                    child: Image.asset("assets/logo.png"),
                  ),
                  initialOffset: const Offset(120, 70),
                  parentKey: _parentKey,
                  onPressed: () {},
                ),
              ],
            ),
          )
        ],
      ),
    );
  }
}
```

# 结论：

在文章中，我已经在flutter中讲解了Draggable Floating Action Button的基本结构；您可以根据自己的选择修改此代码。这是 我对用户交互时的可拖动浮动操作按钮的一个小介绍，我正在使用 Flutter。

我希望这个博客将为您提供足够的信息，试图达到 的**可拖动的浮动操作按钮**在你的flutter项目**。**我们将向您展示介绍是什么？这就是在 Flutter 中制作可拖动的浮动操作按钮的方法。从根本上说，您可以利用 Listener 小部件来区分指针移动事件并根据开发增量更新按钮偏移量。Listener 小部件同样支持区分指针向上事件，按钮的活动应该在这些事件上执行，除非它最近被拖动。您同样需要获取父级和按钮的大小，以防止按钮脱离父级的框。所以请尝试一下。

❤❤感谢阅读本文❤❤