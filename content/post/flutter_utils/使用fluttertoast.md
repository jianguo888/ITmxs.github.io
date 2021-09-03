---
title: "使用fluttertoast"
date: 2021-08-19T20:06:29+08:00
draft: false
tags: ["Flutter"]
categories: ["Flutter"]
---

https://pub.flutter-io.cn/packages/flutter_sliding_up_panel

## 前言

Flutter似乎不支持直接调用安卓的Toast，只能用Flutter自己的方式去实现Toast弹窗。（那岂不是iOS也能用，美滋滋）

## 导包

进入`pubspec.yaml`这个文件，找到这一段代码

```yaml
dependencies:
  #在这下面添加依赖库，注意库的名字要跟flutter对齐，不对齐会报错的
  flutter:
    sdk: flutter

```

在下面添加`fluttertoast: `，注意要和`flutter`对齐，对不齐会导入失败的。

```yaml
dependencies:
  #在这下面添加依赖库，注意库的名字要跟flutter对齐，不对齐会报错的
  flutter:
    sdk: flutter
  fluttertoast: 
```

## 工具类

Toast这东西几乎是到处都会用到，写一个Toast工具类来便于调用，写法很简单就不多赘述了，直接抄吧。

```dart
import 'package:flutter/material.dart';
import 'package:fluttertoast/fluttertoast.dart';

///lib/utils/toast_utils.dart
///Toast工具类
class ToastUtils {
  static void showToast(String message) {
    // 根据消息长度决定自动消失时间
    double multiplier = 0.5;
    double flag = message.length * 0.06 + 0.5;
    //计算显示时间
    int timeInSecForIos = (multiplier * flag).round();
    //如果已显示 先取消已有的
    Fluttertoast.cancel();
    //显示Toast
    Fluttertoast.showToast(
      backgroundColor: Colors.black54,
      msg: message,
      //显示的位置
      gravity: ToastGravity.CENTER,
      //只针对iOS生效的消失时间
      timeInSecForIosWeb: timeInSecForIos,
    );
  }
}

```

## 弹出Toast

简直不要太简单，调用之后传入个消息内容就完事了，如果要改颜色和时间也传入就ok

```dart
void _clickLogin() {
    if (_user.toString().isEmpty) {
      ToastUtils.shotToast("请输入手机号");//弹出Toast
      return;
    }
    if (_password.toString().isEmpty) {
      ToastUtils.shotToast("请输入密码");//弹出Toast
      return;
    }
    _doLogin();
  }
```

