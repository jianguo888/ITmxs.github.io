---
title: "Flutter返回桌面android工具类(原生通讯)"
date: 2021-08-18T11:29:31+08:00
draft: false
tags: ["Flutter"]
categories: ["Flutter"]
---





```dart
import 'package:flutter/services.dart';
import 'package:flutter/material.dart';

class AndroidBackTop {
//初始化通信管道-设置退出到手机桌面
// 通讯名称,回到手机桌面
  static const String CHANNEL = "android/back/desktop";
  //返回手机桌面事件
  static const String eventBackDesktop = "backDesktop";
//设置回退到手机桌面
  static Future<bool> backDeskTop() async {
    final platform = MethodChannel(CHANNEL);
//通知安卓返回,到手机桌面
    try {
      final bool out = await platform.invokeMethod(eventBackDesktop);
      if (out) print('返回到桌面');
    } on PlatformException catch (e) {
      print("通信失败(设置回退到安卓手机桌面:设置失败)");
      print(e.toString());
    }
    return Future.value(false);
  }
}
```

