---
title: "Flutter获取图片大小工具类"
date: 2021-08-18T11:14:11+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---



```dart
import 'dart:async';
import 'dart:io';
import 'dart:typed_data';
import 'dart:ui' as ui;

import 'package:flutter/cupertino.dart';
import 'package:flutter/services.dart';

class ImageSize {
  static Future<ui.Image> loadImage(String url) async {
    AssetBundle _bundle = rootBundle;
    ImageStream stream =
        new AssetImage(url, bundle: _bundle).resolve(ImageConfiguration.empty);
    Completer<ui.Image> completer = new Completer<ui.Image>();
    ImageStreamListener listener;
    listener = new ImageStreamListener((ImageInfo frame, bool synchronousCall) {
      final ui.Image image = frame.image;
      completer.complete(image);
      stream.removeListener(listener);
    });
    stream.addListener(listener);
    return completer.future;
  }

  static Future<ui.Image> loadImageFromFile(String url) async {
    AssetBundle _bundle = rootBundle;
    ImageStream stream = new FileImage(
      File.fromUri(Uri.parse(url)),
    ).resolve(ImageConfiguration.empty);
    Completer<ui.Image> completer = new Completer<ui.Image>();
    ImageStreamListener listener;
    listener = new ImageStreamListener((ImageInfo frame, bool synchronousCall) {
      final ui.Image image = frame.image;
      completer.complete(image);
      stream.removeListener(listener);
    });
    stream.addListener(listener);
    return completer.future;
  }

  static Future<Size> loadSizeFromFile(String url) async {
    ui.Image image = await loadImageFromFile(url);
    return Size(image.width.toDouble(), image.height.toDouble());
  }

  // static Size sizeFromMemory(Uint8List bytes) {
  //   var image = Image.memory(bytes);
  //   return Size(image.width, image.height);
  // }
}

```



```dart
import 'dart:typed_data';



class MemoryInput extends ImageInput {
  final Uint8List bytes;
  const MemoryInput(this.bytes);

  factory MemoryInput.byteBuffer(ByteBuffer buffer) {
    return MemoryInput(buffer.asUint8List());
  }

  @override
  List<int> getRange(int start, int end) {
    return bytes.sublist(start, end);
  }

  @override
  int get length => bytes.length;

  @override
  bool exists() {
    return bytes != null && bytes.isNotEmpty;
  }
}
abstract class ImageInput {
  const ImageInput();

  int get length;

  List<int> getRange(int start, int end);

  bool exists();
}

```



```
  Size size = ImageSizeGetter.getSize(MemoryInput(bytes));
```

