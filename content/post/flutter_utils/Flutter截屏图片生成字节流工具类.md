---
title: "Flutter截屏图片生成字节流工具类"
date: 2021-08-18T11:02:36+08:00
draft: true
---





```dart
import 'dart:io';
import 'dart:typed_data';
import 'dart:ui' as ui;
import 'package:flutter/rendering.dart';
import 'package:flutter/services.dart';
import 'package:flutter/widgets.dart';

class QSCommon {
  /// 截屏图片生成图片流ByteData
  static Future<ByteData> capturePngToByteData(GlobalKey key) async {
    try {
      RenderRepaintBoundary boundary = key.currentContext.findRenderObject();
      double dpr = ui.window.devicePixelRatio; // 获取当前设备的像素比
      ui.Image image = await boundary.toImage(pixelRatio: dpr);
      ByteData _byteData =
          await image.toByteData(format: ui.ImageByteFormat.png);
      return _byteData;
    } catch (e) {
      debugPrint(e);
    }
    return null;
  }
}

```



使用

```
   ByteData byteData = await QSCommon.capturePngToByteData(key);
```

