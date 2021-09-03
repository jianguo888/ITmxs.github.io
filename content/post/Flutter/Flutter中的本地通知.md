---
title: "Flutter中的本地通知"
date: 2021-08-15T12:35:13+08:00
draft: false
tags: ["Flutter"]
categories: ["Flutter"]
---

颤振中的本地通知。

![image-20210815123753856](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210815123753856.png)

![img](https://miro.medium.com/max/1356/1*aiSLboebEqvWdhsaTZCgEQ.jpeg)

在本文中，我们将讨论如何在我们的应用程序中生成本地通知。

很多时候我们需要在应用程序中显示通知，如果执行了任何后台任务，
或者
如果我们需要一个弹出通知来通知他们。

所以让我们来谈谈这个，这次
在颤振中，有最好的插件（库）可用于执行此任务

为了使用这个，
我们按照这个步骤，

1. 打开此链接[flutter_local_notification](https://pub.dev/packages/flutter_local_notifications)，然后添加到您的 pubspec.yaml 文件中。

![img](https://miro.medium.com/max/60/1*n0fle-RFI8J3gC-q39GX2Q.png?q=20)

![img](https://miro.medium.com/max/700/1*n0fle-RFI8J3gC-q39GX2Q.png)

2.为android添加一个通知图标，
在这个路径中添加这个图像，在android文件夹中。

app/src/main/res/drawable/app_icon.png

![img](https://miro.medium.com/max/906/1*oYyMUCa2EpBg2wKsYsHGlQ.png)

像这张图片一样，我在这里添加了**app_icon.png**图片，

\3. Ñ OW它的时间编码，

3.1) 首先在你的 init 方法中添加这个。

```dart
@override
initState() {
  super.initState();
  var initializationSettingsAndroid =
  new AndroidInitializationSettings('app_icon');
  var initializationSettingsIOS = new IOSInitializationSettings();
  var initializationSettings = new InitializationSettings(android: initializationSettingsAndroid, iOS: initializationSettingsIOS);
  flutterLocalNotificationsPlugin = new FlutterLocalNotificationsPlugin();
  flutterLocalNotificationsPlugin.initialize(initializationSettings,
      onSelectNotification: onSelectNotification);
}
```

3.2）这是您的通知显示方法。

```dart
Future _showNotification() async {
  var androidPlatformChannelSpecifics = new AndroidNotificationDetails(
      'your channel id', 'your channel name', 'your channel description',
      importance: Importance.max, priority: Priority.high);
  var iOSPlatformChannelSpecifics = new IOSNotificationDetails();
  var platformChannelSpecifics = new NotificationDetails(
      android: androidPlatformChannelSpecifics, iOS: iOSPlatformChannelSpecifics);
  await flutterLocalNotificationsPlugin.show(
    0,
    'New Notification',
    'Flutter is awesome',
    platformChannelSpecifics,
    payload: 'This is notification detail Text...',
  );
}
```

3.3）这是您的通知对话框，在通知点击时打开，

```dart
Future onSelectNotification(String payload) async {
  showDialog(
    context: context,
    builder: (_) {
      return new AlertDialog(
        title: Text("Your Notification Detail"),
        content: Text("Payload : $payload"),
      );
    },
  );
}
```

4）现在完整的代码在这里（示例），

```dart
import 'dart:async';
import 'package:flutter/material.dart';
import 'package:flutter_local_notifications/flutter_local_notifications.dart';

void main() {
  runApp(
    new MaterialApp(home: new MyApp()),
  );
}

class MyApp extends StatefulWidget {
  @override
  _MyAppState createState() => new _MyAppState();
}

class _MyAppState extends State<MyApp> {
  FlutterLocalNotificationsPlugin flutterLocalNotificationsPlugin;

  @override
  initState() {
    super.initState();
    var initializationSettingsAndroid =
    new AndroidInitializationSettings('app_icon');
    var initializationSettingsIOS = new IOSInitializationSettings();
    var initializationSettings = new InitializationSettings(android: initializationSettingsAndroid, iOS: initializationSettingsIOS);
    flutterLocalNotificationsPlugin = new FlutterLocalNotificationsPlugin();
    flutterLocalNotificationsPlugin.initialize(initializationSettings,
        onSelectNotification: onSelectNotification);
  }
  @override
  Widget build(BuildContext context) {
    return new MaterialApp(
      home: new Scaffold(
        appBar: new AppBar(
          title: new Text('Plugin example app'),
        ),
        body: new Center(
          child: new Column(
            mainAxisAlignment: MainAxisAlignment.center,
            mainAxisSize: MainAxisSize.max,
            children: <Widget>[
              new RaisedButton(
                onPressed: _showNotification,
                child: new Text('Show Notification'),
              ),
            ],
          ),
        ),
      ),
    );
  }
  Future _showNotification() async {
    var androidPlatformChannelSpecifics = new AndroidNotificationDetails(
        'your channel id', 'your channel name', 'your channel description',
        importance: Importance.max, priority: Priority.high);
    var iOSPlatformChannelSpecifics = new IOSNotificationDetails();
    var platformChannelSpecifics = new NotificationDetails(
        android: androidPlatformChannelSpecifics, iOS: iOSPlatformChannelSpecifics);
    await flutterLocalNotificationsPlugin.show(
      0,
      'New Notification',
      'Flutter is awesome',
      platformChannelSpecifics,
      payload: 'This is notification detail Text...',
    );
  }

  Future onSelectNotification(String payload) async {
    showDialog(
      context: context,
      builder: (_) {
        return new AlertDialog(
          title: Text("Your Notification Detail"),
          content: Text("Payload : $payload"),
        );
      },
    );
  }
}
```

现在，我只介绍一个演示示例。

