---
title: "Flutter本地图片缓存工具类"
date: 2021-08-18T11:11:47+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---





```dart
import 'dart:convert';
import 'dart:io';
import 'dart:typed_data';

import 'package:dio/dio.dart';
import 'package:flutter/cupertino.dart';
import 'package:path_provider/path_provider.dart';
import 'package:convert/convert.dart';
import 'package:crypto/crypto.dart';

class LocalImageCache {
  static LocalImageCache instance = LocalImageCache();
  String _tmepPath = "";
  void init() async {
    var tempDir = await getTemporaryDirectory();
    _tmepPath = tempDir.path;
  }

  String getImage(String url) {
    var file = File(_tmepPath + "/" + generate_MD5(url));

    if (file.existsSync()) {
      return file.path;
    }
    return null;
  }

//直接保存图片到本地临时目录
  Future<String> save(Uint8List bytes, String imageName) async {
    var tempDir = await getTemporaryDirectory();
    var file = File(tempDir.path + "/" + generate_MD5(imageName));
    if (file.existsSync()) {
      file.deleteSync();
    }
    file.writeAsBytesSync(bytes);
    return file.path;
  }

  Future<String> download(BuildContext context, String url) async {
    try {
      var dio = await Dio()
          .get(url, options: Options(responseType: ResponseType.bytes));
      var image = Uint8List.fromList(dio.data);
      return save(image, url);
    } catch (ex) {
      print("image download error:${ex.toString()}");
      return null;
    }
  }

  Future<String> getAndDownload(BuildContext context, String url) async {
    var image = getImage(url);
    if (image == null) {
      return await download(context, url);
    }
    return image;
  }

  String generate_MD5(String data) {
    var content = new Utf8Encoder().convert(data);
    var digest = md5.convert(content);
    // 这里其实就是 digest.toString()
    return hex.encode(digest.bytes);
  }
}

```



```
 var path = await LocalImageCache.instance
          .save(_pics[i], "act_pub_draft_pic" + i.toString());
```

