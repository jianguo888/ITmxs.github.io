---
title: "在flutter中使用RxDart同步UI"
date: 2021-09-29T08:45:19+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---

> 本文主要介绍在 Flutter 中使用 RxDart 同步 UI

简短的 RxDart 和 Flutter 指南，向您展示如何使用主题作为消息总线同步 UI 事件。

假设您有一个包含一行按钮的应用程序。当点击其中一个按钮时，您希望被点击的按钮增大，其余按钮缩小。为了实现这个功能，我们可以使用来自[RxDart](https://pub.dartlang.org/packages/rxdart)和[get_it](https://pub.dartlang.org/packages/get_it)的行为主题来在需要的地方获取 tat 主题。在以下情况下可以使用此功能：

- 您有一行按钮，您希望在选择其中一个时停用这些按钮，并且只有一个处于活动状态
- 您有一个自定义工具栏，希望对选择的新项目做出反应
- 您有多个 UI 元素要在可见 UI 中同步到单个事件

## [设置](https://www.filledstacks.com/snippet/use-rx-dart-in-flutter-to-synchronize-ui/#setup)

将软件包添加到您的 pubspec

```yaml
    ... 

    get_it: ^1.0.3
    rxdart: ^0.21.0

    ...
```

在 lib 文件夹中创建 service_locator.dart 文件。

```dart
import 'package:get_it/get_it.dart';

import 'package:connected_buttons/services/button_messagebus.dart';

GetIt locator = GetIt();

void setupLocator() {
  locator.registerSingleton(ButtonMessagebus());
}
```

创建一个名为 services 的新文件夹。在 services 下创建一个名为 button_message_bus.dart 的文件。此“服务”将提供一个管道，您可以在其中放置 Id 并侦听 id。我们将使用`BehaviourSubject`来自 RxDart 的一个，如果您订阅，它将始终广播流上的最后一个值，之后所有发出的值都在那里。我们将流公开为 idStream 并且我们将添加一个函数，我们可以使用该函数将 ID 放置到流中。当一个按钮被点击时，它所做的就是在流上放置一个 id。没有其他的。

该按钮将已注册以收听流，因此它会做出相应的反应。

```dart
import 'package:rxdart/rxdart.dart';

class ButtonMessagebus {
  BehaviorSubject<int> _buttonIdSubject = BehaviorSubject<int>.seeded(-1);

  Stream<int> get idStream => _buttonIdSubject.stream;

  void broadcastId(int id) {
    _buttonIdSubject.add(id);
  }
}
```

创建一个名为 widgets 的新文件夹。在该文件夹中创建一个名为 connected_button.dart 的新文件。它将是一个有状态的小部件，它根据来自流的 id 改变它的宽度和高度。如果 ID 匹配我们想要增长，否则我们会缩小。我们将使用定位器来获取我们的 MessageBus 并在 initState 函数中订阅。

```dart
iimport 'dart:async';

import 'package:connected_buttons/services/button_messagebus.dart';
import 'package:flutter/material.dart';
import '../service_locator.dart';

class ConnectedButton extends StatefulWidget {
  final int id;

  ConnectedButton({@required this.id});

  @override
  _ConnectedButtonState createState() => _ConnectedButtonState();
}

class _ConnectedButtonState extends State<ConnectedButton> {
  bool _active = false;
  double _size = 100;

  ButtonMessagebus _messageBus = locator<ButtonMessagebus>();
  StreamSubscription<int> messageSubscription;

  @override
  void initState() {
    // Listen for Id received
    messageSubscription = _messageBus.idStream.listen(_idReceived);
    super.initState();
  }

  void _idReceived(int id) {
     setState(() {
        if (id == widget.id) {
          _active = true;
          _size = 140;
        } else {
          _active = false;
          _size = 100;
        }
      });
  }

  @override
  Widget build(BuildContext context) {
    return GestureDetector(
      onTap: () {
        // Broadcast Id
        _messageBus.broadcastId(widget.id);
      },
      child: _buttonUi,
    );
  }

  @override
  void dispose() {
    super.dispose();
    messageSubscription.cancel();
  }

  Widget get _buttonUi => AnimatedContainer(
        curve: Curves.easeIn,
        width: _size,
        height: _size,
        alignment: Alignment.center,
        decoration: BoxDecoration(
            color: Colors.grey[500], borderRadius: BorderRadius.circular(10.0)),
        duration: Duration(milliseconds: 150),
        child: Text(
          'id: ${widget.id} -> ${_active.toString()}',
          style: TextStyle(color: Colors.white, fontWeight: FontWeight.bold),
        ),
      );
}
```

上面代码的重要部分是在 initState 函数中，您可以在其中订阅正在发出的 id。您可以在此处执行自定义逻辑。最后，转到 main.dart 文件设置定位器。将三个或更多按钮放入视图中，然后单击即可。

```dart
import 'package:flutter/material.dart';
import 'service_locator.dart';
import './widgets/connected_button.dart';

void main() {
  setupLocator();
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
        ),
        home: Scaffold(
            backgroundColor: Colors.grey[800],
            body: Column(
              mainAxisSize: MainAxisSize.max,
              mainAxisAlignment: MainAxisAlignment.spaceEvenly,
              children: <Widget>[
                ConnectedButton(id: 0),
                ConnectedButton(id: 1),
                ConnectedButton(id: 2)
              ],
            )));
  }
}
```

您可以在应用程序的任何位置收听正在广播的 id。您要做的就是注册到 idStream 并添加您想要在发出 id 时运行的逻辑。