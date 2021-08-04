---
title: "《Flutter与原生通讯01》"
subtitle: ""
date: 2021-05-19T00:19:22+08:00
lastmod: 2021-05-19T00:19:22+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["flutter","原生通讯"]
categories: ["flutter","原生通讯"]

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
comment:
  enable: true
mapbox:
  accessToken: ""
---



> 本文主要介绍Flutter Plugin原理以及其开发和发布过程。

## 前言

Flutter优势主要体现在UI上--高性能且跨平台表现一致。但是针对平台(Android、IOS)的实现，如：获取电量、判断WiFi使用、调起WebView加载网页等，得调用特定平台的API包。Flutter Plugin就是为调用平台API而生。

下文中所提及到的"平台"指的是Android、IOS两端。

<!--more-->

## 介绍

### Flutter Plugin

包含针对Android（Java或Kotlin代码）或iOS（Objective-C或Swift代码）的原生实现，通过`Platform Channels`与FLutter(dart)层通讯并暴露API。



![img](https://pic1.zhimg.com/80/v2-235250ea4c16af31ed27757cf1656f18_720w.jpg)



### Platform Channels

- 允许Flutter UI和平台之间传递消息。
- Platform Channel中的消息和响应是异步传递的，以确保用户界面保持响应。
- 一个Flutter应用可以存在多个channel，使用`name`作为区分。
- Platform Channel并非是`线程安全`，故平台跟Flutter Engine的所有交互必须在平台的`主线程`中执行的。
- Flutter默认的消息编解码器是[StandardMessageCodec class](https://link.zhihu.com/?target=https%3A//api.flutter.dev/flutter/services/StandardMessageCodec-class.html)，现支持平台数据类型如下：

![img](https://pic3.zhimg.com/80/v2-4d9898367e488490d0f4d106c7883b2a_720w.jpg)



## 创建Flutter Plugin

接下来介绍使用Android Studio创建Flutter Plugin。使用Visual Studio Code创建的过程也是大同小异，机智的你一定能举一反三，在这里就不一一细说。

### 第一步：选择创建一个Flutter project



![img](https://pic1.zhimg.com/80/v2-5c88bc27b9031b293ced4b8f558b1520_720w.jpg)



### 第二步：选择Flutter Project的类型为Flutter Plugin

- Flutter Application 是创建一个纯flutter项目工程
- Flutter Plugin 是创建一个可以暴露平台API的插件工程
- Flutter Package 是创建一个纯Dart的组件包。
- Fultter Module 是创建一个Flutter Module，用于被引入现有的原生App。

![img](https://pic1.zhimg.com/80/v2-b773e4718ba6f7dfcf16ad4e400e0d7c_720w.jpg)



### 第三步：填写Project name等工程信息，完成Flutter Plugin创建。

工程创建完成后的目录结构如下： + lib/flutter_plugin_eg.dart 是插件包dart API实现 + android/src/main/ 是插件包API Android的实现 + ios/Classes/ 是插件包API IOS的实现 + example 是基于依赖当前插件的纯flutter示例工程，一般用作展示API调用。

注：从 Flutter 1.9 开始，iOS 新项目默认使用 Swift 语言，而非 Objective-C；Android 新项目则默认使用 Kotlin，而非 Java。如有需要，是可随时切换回之前的 Objective-C 或 Java。

![img](https://pic2.zhimg.com/80/v2-7aed24a9b311c4bf8d62d14ed771d2ed_720w.jpg)



### 使用Android模拟器运行example工程

我们先看看示例工程在Android模拟器上的运行效果：

进入example工程目录，运行lib/main.dart。

APP在Android模拟器运行后，可以看到屏幕出现了"Running on: Android 9"。



![img](https://pic4.zhimg.com/80/v2-e4396d3d9ef0e9f9c434e0b624320503_720w.jpg)



接下来我们通过代码看看flutter是怎么显示出当前平台的系统版本。

### 示例工程 example/lib/main.dart

关键代码：

```text
...
import 'package:flutter_plugin_eg/flutter_plugin_eg.dart';
...
platformVersion = await FlutterPluginEg.platformVersion;
...
setState(() {
  _platformVersion = platformVersion;
});
...
body: Center(
  child: Text('Running on: $_platformVersion\n'),
)
```

示例工程中的lib/main.dart引入了我们刚刚创建Flutter Plugin中dart API实现`flutter_plugin_eg.dart`。

随后使用异步的方式调用`flutter_plugin_eg.dart` 的`FlutterPluginEg.platformVersion`并把返回值赋值给`platformVersion`，随后通过`setState`方法把`platformVersion`的值赋值给当前状态组件的`_platformVersion`，触发UI重渲把`_platformVersion`的值"Android 9"显示出来。

### Flutter Plugin中dart API实现 lib/flutter_plugin_eg.dart

全量代码：

```text
import 'dart:async';

import 'package:flutter/services.dart';

class FlutterPluginEg {
  static const MethodChannel _channel =
      const MethodChannel('flutter_plugin_eg');

  static Future<String> get platformVersion async {
    final String version = await _channel.invokeMethod('getPlatformVersion');
    return version;
  }
}
```

- `service.dart`暴露与平台通讯的API，如:`MethodChannel`Platform Channel的一种类型
- `_channel`是FlutterPluginEg类的属性，是一个实例化的`MethodChannel`,`name`为"flutter_plugin_eg"
- `platformVersion`是FlutterPluginEg类的静态`可计算属性`，会异步返还一个String。
- `platformVersion`中，调用`_channel`的`invokeMethod`方法，入参"getPlatformVersion"为调用平台约定的方法名。然后把`invokeMethod`的异步结果赋值给`String version`作为`platformVersion`的返回值。

### Flutter Plugin中Android实现

android/src/main/kotlin/.../FlutterPluginEgPlugin.kt 全量代码：

```text
package com.yy.flutter_plugin_eg

import io.flutter.plugin.common.MethodCall
import io.flutter.plugin.common.MethodChannel
import io.flutter.plugin.common.MethodChannel.MethodCallHandler
import io.flutter.plugin.common.MethodChannel.Result
import io.flutter.plugin.common.PluginRegistry.Registrar

class FlutterPluginEgPlugin: MethodCallHandler {
  companion object {
    @JvmStatic
    fun registerWith(registrar: Registrar) {
      val channel = MethodChannel(registrar.messenger(), "flutter_plugin_eg")
      channel.setMethodCallHandler(FlutterPluginEgPlugin())
    }
  }

  override fun onMethodCall(call: MethodCall, result: Result) {
    if (call.method == "getPlatformVersion") {
      result.success("Android ${android.os.Build.VERSION.RELEASE}")
    } else {
      result.notImplemented()
    }
  }
}
```

> 再次强调：一个Flutter应用是可以有多个`channel`，并而每个`channel`都可以有多个`method`，所以需要重点了解平台的代码是通过怎么样去对接channel name 与method name。从上文.kt源码可以看到:

- 注册MethodChannel约定通道名"flutter_plugin_eg"，并开始监听通道消息。

```text
companion object {
    @JvmStatic
    fun registerWith(registrar: Registrar) {
      val channel = MethodChannel(registrar.messenger(), "flutter_plugin_eg")
      channel.setMethodCallHandler(FlutterPluginEgPlugin())
    }
  }
```

- 实现onMethodCall方法，判断方法名"getPlatformVersion"，返回`Android ${android.os.Build.VERSION.RELEASE}`

```text
override fun onMethodCall(call: MethodCall, result: Result) {
    if (call.method == "getPlatformVersion") {
      result.success("Android ${android.os.Build.VERSION.RELEASE}")
    } else {
      result.notImplemented()
    }
  }
```

当channel name 和 method name 约定后，Flutter Plugin就可以在dart中方便调用平台的实现，并暴露API供Flutter项目使用。

### Flutter Plugin中IOS实现

ios/Classes/SwiftFlutterPluginEgPlugin.swift

```text
import Flutter
import UIKit

public class SwiftFlutterPluginEgPlugin: NSObject, FlutterPlugin {
  public static func register(with registrar: FlutterPluginRegistrar) {
    let channel = FlutterMethodChannel(name: "flutter_plugin_eg", binaryMessenger: registrar.messenger())
    let instance = SwiftFlutterPluginEgPlugin()
    registrar.addMethodCallDelegate(instance, channel: channel)
  }

  public func handle(_ call: FlutterMethodCall, result: @escaping FlutterResult) {
    result("iOS " + UIDevice.current.systemVersion)
  }
}
```

可以看出，IOS与Android实现思路雷同：通过"flutter_plugin_eg"注册`FlutterMethodChannel`并开始监听。

> 但惊奇发现Flutter Plugin中swift默认生成的代码居然没FlutterMethodCall方法名判断，在swift断点看是能获取到"getPlatformVersion"这个方法名的。

![img](https://pic4.zhimg.com/80/v2-52dda6ae7529c22381fac4ba90194cdf_720w.jpg)



IOS模拟器上运行效果：

![img](https://pic3.zhimg.com/80/v2-c7ed6b618cc76f44483d2a868d50ef6e_720w.jpg)



## 发布Flutter Plugin

轮子造好后，可以发布到[pub.dev](https://link.zhihu.com/?target=https%3A//pub.dev/)，以下内容纯属搬砖。

### 补充文档

在Flutter Plugin根目录添加 + README.md:介绍包的文件 + CHANGELOG.md 记录每个版本中的更改 + LICENSE 包含软件包许可条款的文件 + [编写API文档](https://link.zhihu.com/?target=https%3A//dart.dev/guides/language/effective-dart/documentation)

### 发布package

一旦你实现了一个包，你可以在[Pub](https://link.zhihu.com/?target=https%3A//pub.dev/)上发布它 ，这样其他开发人员就可以轻松使用它

在发布之前，检查pubspec.yaml、README.md以及CHANGELOG.md文件，以确保其内容的完整性和正确性。

然后, 运行 dry-run 命令以查看是否都准备OK了:

```text
flutter packages pub publish --dry-run
```

最后, 运行发布命令:

```text
flutter packages pub publish
```

有关发布的详细信息，请参阅[Pub publishing docs](https://link.zhihu.com/?target=https%3A//dart.dev/tools/pub/publishing)

## 思考

开发一个功能完备的APP，说到底还是需要有原生平台开发的能力，就如Flutter Plugin需要开发原生平台部分。相信有很多Web前端童鞋也开始接触Flutter，但是如果不去深入了解原生开发，那么也只能停留在UI层上。