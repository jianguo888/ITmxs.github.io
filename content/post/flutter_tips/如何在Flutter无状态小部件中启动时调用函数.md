---
title: "如何在Flutter无状态小部件中启动时调用函数"
date: 2021-09-29T08:36:01+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---

> 本文主要介绍如何在 Flutter 无状态小部件中启动时调用函数

有没有想过如何从无状态小部件在 Flutter 启动时调用异步函数？

移动开发中最常见的场景之一是在显示新视图时调用异步函数。在 Flutter 中，这可以使用有状态的小部件并在`initState`函数中调用您的代码来完成。

```dart
class Example extends StatefulWidget {
  Example({Key key}) : super(key: key);

  _ExampleState createState() => _ExampleState();
}

class _ExampleState extends State<Example> {

  @override
  void initState() {
    _getThingsOnStartup().then((value){
      print('Async done');
    });
    super.initState();
  }

  @override
  Widget build(BuildContext context) {
    return Container();
  }

  Future _getThingsOnStartup() async {
    await Future.delayed(Duration(seconds: 2));
  }
} 
```

如果您想从无状态小部件调用它怎么办？嗯，这也是可能的。使用有状态小部件作为根小部件，您也可以提供回调函数来执行启动逻辑。请参阅下面的示例。

创建一个 StatefulWrapper，它接受一个要调用的函数和一个要显示的子项。

```dart
/// Wrapper for stateful functionality to provide onInit calls in stateles widget
class StatefulWrapper extends StatefulWidget {
  final Function onInit;
  final Widget child;

  const StatefulWrapper({@required this.onInit, @required this.child});

  @override
  _StatefulWrapperState createState() => _StatefulWrapperState();
}

class _StatefulWrapperState extends State<StatefulWrapper> {

@override
  void initState() {
    if(widget.onInit != null) {
      widget.onInit();
    }
    super.initState();
  }

  @override
  Widget build(BuildContext context) {
    return widget.child;
  }
}
```

将 stateless 小部件的 UI 包装在 StatefulWrapper 中，并传递您想要运行的 onInit 逻辑

```dart
class StartupCaller extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return StatefulWrapper(
      onInit: () {
        _getThingsOnStartup().then((value) {
          print('Async done');
        });
      },
      child: Container(),
    );
  }

  Future _getThingsOnStartup() async {
    await Future.delayed(Duration(seconds: 2));
  }
}
```

就是这样。该函数只会在有状态小部件初始化时调用一次。