---
title: "Flutter组件Draggable与DragTarget"
date: 2021-08-31T09:48:26+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["flutter","dart"]
categories: ["flutter","dart"]
---

#### 一、认识 Draggable 组件

`Draggable` 顾名思义，是可拖动的组件，它继承自 `StatefulWidget` ，且可接受一个泛型。 构造方法有非常多的入参，其中必须传入的是 `child` 和 `feedback` 两个组件。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b8542b57ff4941be8bf01aa88ab7b6ac~tplv-k3u1fbpfcp-watermark.awebp)

```dart
final Widget child;
final Widget feedback;
复制代码
```

------

##### 1. 拖动的方向:  axis

下面先通过一个小案例认识一下 `Draggable`：下面是三个 `Draggable` 组件，其中 `child` 是蓝色小圆，`feedback` 是红色小圆，三者的区别在于 `axis` 属性不同。左边 `axis` 为 `null` ，表示不限定轴向，可以自由拖动；中间 `axis` 为 `vertical` ，只能在竖直方向拖动；中间 `axis` 为 `horizontal` ，只能在水平方向拖动。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bb6a60461a814c7a9c4fa4068fc02553~tplv-k3u1fbpfcp-watermark.awebp)

```dart
class CustomDraggable extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    List<Axis?> axis = [null, Axis.vertical, Axis.horizontal];
    return Wrap(
        spacing: 30,
        children: axis
            .map((Axis? axis) => Draggable(
                  axis: axis,
                  child: buildContent(),
                  feedback: buildFeedback(),
                ))
            .toList());
  }

  Widget buildContent() {
    return Container(
      width: 30,
      height: 30,
      alignment: Alignment.center,
      decoration: BoxDecoration(
        color: Colors.blue,
        shape: BoxShape.circle,
      ),
    );
  }

  Widget buildFeedback() {
    return Container(
      width: 30,
      height: 30,
      decoration: BoxDecoration(
        color: Colors.red,
        shape: BoxShape.circle,
      ),
    );
  }
}
复制代码
```

------

##### 2.拖动时原位置组件:  childWhenDragging

`Draggable` 可以通过 `childWhenDragging` 属性指定在拖拽过程中`原来位置`的组件。如下，拖动时原来的位置显示为 `橙色小圆` 和 删除图标。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5a65377f987c470f86be16402f11e08a~tplv-k3u1fbpfcp-watermark.awebp)

```dart
Draggable(
  axis: axis,
  childWhenDragging: buildWhenDragging(),
  child: buildContent(),
  feedback: buildFeedback(),
))
  
Widget buildWhenDragging() {
  return Container(
    width: 30,
    height: 30,
    decoration: BoxDecoration(
      color: Colors.orange,
      shape: BoxShape.circle,
    ),
    child: Icon(
      Icons.delete_outline,
      size: 20,
      color: Colors.white,
    ),
  );
}
复制代码
```

------

#### 二、Draggable 与 DragTarget 联合使用

##### 1. 综合测试案例

下面通过一个示例测试一下 `Draggable` 与 `DragTarget` 的联合使用。如下，上面的小球是 `Draggable` ，下面的区域是 `DragTarget` 。可以拖动小球来为 `DragTarget` 着色，并且显示当前操作的信息。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/88e3edfb633a429ba0931f772659e127~tplv-k3u1fbpfcp-watermark.awebp)

`Draggable` 可以监听五个回调：

- `onDragStarted` ：开始拖动时回调，无回调数据。
- `onDragEnd`：结束拖动时回调，可以获取 `DraggableDetails` 数据。
- `onDragUpdate`：拖动更新时回调，可以获取 `DraggableDetails` 数据。
- `onDragCompleted` : 拖入目标区域，并松手完成时回调，无回调数据。
- `onDraggableCanceled`：未在目标区域，拖拽取消回调，可以获取 `Velocity` 、`Offset`数据。

```dart
List<Widget> _buildDraggable() {
  return colors.map(
        (Color color) => Draggable<Color>(
            onDragStarted: _onDragStarted,
            onDragEnd: _onDragEnd,
            onDragUpdate: _onDragUpdate,
            onDragCompleted: _onDragCompleted,
            onDraggableCanceled: _onDraggableCanceled,
            childWhenDragging: childWhenDragging(colors.indexOf(color).toString()),
            child: buildContent(color),
            data: color,
            feedback: buildFeedback(color)),
      ).toList();
}

void _onDragUpdate(DragUpdateDetails details) {
  print('坐标:'
      '（${details.localPosition.dx.toStringAsFixed(1)},'
      '${details.localPosition.dy.toStringAsFixed(1)})');
}

void _onDraggableCanceled(Velocity velocity, Offset offset) {
  _info = '拖拽取消';
}

void _onDragCompleted() {
  _info = '拖拽完成';
}

void _onDragEnd(DraggableDetails details) {
  setState(() => _info = '结束拖拽');
}

void _onDragStarted() {
     setState(() => _info = '开始拖拽');
}
复制代码
```

------

`DragTarget` 会通过 `builder` 回调来构建组件，其中会回调 `candidateData` 和 `rejectedData` 两个列表，其中包含`接受` 和 `拒绝` 的数据。由于 `Draggable` 支持多个同时拖动，使用是数据列表。

```dart
DragTarget<Color>(
  onLeave: _onLeave,
  onAccept: _onAccept,
  onWillAccept: _onWillAccept,
  builder: _buildTarget,
)
  
Widget _buildTarget(BuildContext context, List<Color?> candidateData, List rejectedData) {
  return Container(
      width: 150.0,
      height: 50.0,
      color: _color,
      child: Center(
        child: Text(
          _info,
          style: TextStyle(color: Colors.white),
        ),
      ));
}

void _onLeave(Color? data) {
  print("onLeave: data = $data ");
  setState(() => _info = 'onLeave');
}

void _onAccept(Color data) {
  print("onAccept: data = $data ");
  setState(() => _color = data);
}

bool _onWillAccept(Color? data) {
  print("onWillAccept: data = $data ");
  setState(() => _info = 'onWillAccept');
  return data != null;
}
复制代码
```

`onWillAccept` 是 `DragTarget` 中比较重要的一个回调，当拖动的组件到达目标区域后，`onWillAccept` 会触发。从下面源码中可以看出 `_candidateAvatars` 和 `_rejectedAvatars` 和 `onWillAccept` 的返回值有关。如果 `onWillAccept` 返回 false ，则数据会被简入到 `_rejectedAvatars` 。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d0728504f39542fdb5542d6bcbc8fe0c~tplv-k3u1fbpfcp-watermark.awebp)

而 `builder` 中的回调入参  `candidateData` 和 `rejectedData` 就是根据上面两个列表计算的。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7e0173a5a5be4d5da2d8c4ea4337f8fb~tplv-k3u1fbpfcp-watermark.awebp)

------

##### 2.拖拽删除案例

如下示例，通过拓展组件目标到指定位置进行移除，通过 `Draggable 和 DragTarget` 联合就很容易实现。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/61a654e1e838450bab1e85b49b0a4e51~tplv-k3u1fbpfcp-watermark.awebp)

代码实现如下，通过颜色数组 `colors` 生成不同颜色的 `Draggable` ，并拥有 `int` 泛型，传递的数值为可拖拽组件的索引，这样在 `DragTarget` 的 `onAccept` 中可以获取拖入进的索引数据，从而实现删除功能。

```dart
class DeleteDraggable extends StatefulWidget {
  @override
  _DeleteDraggableState createState() => _DeleteDraggableState();
}

class _DeleteDraggableState extends State<DeleteDraggable> {
  List<Color> colors = [
    Colors.red, Colors.yellow, Colors.blue, Colors.green,
    Colors.orange, Colors.purple, Colors.cyanAccent];

  @override
  Widget build(BuildContext context) {
    return  Column(
        mainAxisSize: MainAxisSize.min,
        children: <Widget>[
          Wrap(
            children: _buildDraggable(),
            spacing: 10,
          ),
          SizedBox(
            height: 20,
          ),
          DragTarget<int>(
              onAccept: _onAccept,
              onWillAccept: (data) => data != null,
              builder: buildTarget
          )
        ],
    );
  }

  Widget buildTarget(context, candidateData, rejectedData) => Container(
      width: 40.0,
      height: 40.0,
      decoration: BoxDecoration(color: Colors.red, shape: BoxShape.circle),
      child: Center(
        child: Icon(Icons.delete_sweep, color: Colors.white),
      ));

  List<Widget> _buildDraggable() => colors
      .map((Color color) => Draggable<int>(
            child: buildContent(color),
            data: colors.indexOf(color),
            childWhenDragging: buildWhenDragging(),
            feedback: buildFeedback(color)),
      ).toList();

  Widget buildContent(Color color) {
    return Container(
      width: 30,
      height: 30,
      alignment: Alignment.center,
      child: Text(
        colors.indexOf(color).toString(),
        style: TextStyle(color: Colors.white, fontWeight: FontWeight.bold),
      ),
      decoration: BoxDecoration(color: color, shape: BoxShape.circle),
    );
  }

  Widget buildFeedback(Color color) {
    return Container(
      width: 25,
      height: 25,
      decoration:
          BoxDecoration(color: color.withAlpha(100), shape: BoxShape.circle),
    );
  }

  Widget buildWhenDragging() {
    return Container(
      width: 30,
      height: 30,
      decoration: BoxDecoration(color: Colors.red, shape: BoxShape.circle),
      child: Icon(Icons.delete_outline, size: 20, color: Colors.white,
      ),
    );
  }

  void _onAccept(int data) {
    setState(() {
      colors.removeAt(data);
    });
  }
}
复制代码
```

通过`Draggable 和 DragTarget` 的联合使用，不需要我们自己去实现拖拽逻辑，可以很轻松解决很多目标拖拽的问题。那本文到这里就结束了，谢谢观看，明天见~

