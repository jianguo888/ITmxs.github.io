---
title: "创建可移动的stack小部件"
date: 2021-10-05T17:05:07+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---

> 本文主要介绍我为桌面和 Web 设计的一个超级秘密 Flutter 项目使用了画布和可拖动节点界面。本教程将展示我如何使用堆栈来使用小部件完成可拖动功能

如下所示。

![可拖动小部件示例抖动](https://luckly007.oss-cn-beijing.aliyuncs.com/img/draggable.82a2fbd.ca5d44b83e7158d058866e37a2dc27ea.gif)

我们将动态地将项目添加到堆栈中并区分它们，我将使用 RandomColor 类型器。所以我们必须添加那个包。

```yaml
random_color:
```

然后我们可以创建包含我们的堆栈的 HomeView

```dart
class HomeView extends StatefulWidget {
  @override
  _HomeViewState createState() => _HomeViewState();
}

class _HomeViewState extends State<HomeView> {
  List<Widget> movableItems = [];

  @override
  Widget build(BuildContext context) {
    return Scaffold(
        body: Stack(
          children: movableItems,
        ));
  }
}
```

功能非常简单。我们将有一个`MoveableStackItem`有状态的小部件。它会跟踪自己的位置和颜色。颜色在初始化时设置，位置通过 更新`GestureDetector`。

```dart
class _MoveableStackItemState extends State<MoveableStackItem> {
  double xPosition = 0;
  double yPosition = 0;
  Color color;

  @override
  void initState() {
    color = RandomColor().randomColor();
    super.initState();
  }

  @override
  Widget build(BuildContext context) {
    return Positioned(
      top: yPosition,
      left: xPosition,
      child: GestureDetector(
        onPanUpdate: (tapInfo) {
          setState(() {
            xPosition += tapInfo.delta.dx;
            yPosition += tapInfo.delta.dy;
          });
        },
        child: Container(
          width: 150,
          height: 150,
          color: color,
        ),
      ),
    );
  }
}
```

最后要做的是向`MoveableStackItem`视图添加一个新的。我们将通过 HomeView 中的浮动操作按钮来实现。

```dart
 return Scaffold(
  floatingActionButton: FloatingActionButton(
    onPressed: () {
      setState(() {
        movableItems.add(MoveableStackItem());
      });
    },
  ),
  body: Stack(
    children: movableItems,
  ));
```

就是这样。现在您的视图上有一个可移动的堆栈项。