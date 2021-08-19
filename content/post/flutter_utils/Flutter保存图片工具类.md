---
title: "Flutter保存图片工具类"
date: 2021-08-18T10:59:08+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---







```dart
 SaveImageUtils.save(
                      url: widget.photoList[_controller.page.toInt()]
                          .toString())
```

```dart
import 'dart:io';

import 'package:dio/dio.dart';
import 'package:fluttertoast/fluttertoast.dart';
import 'package:image_gallery_saver/image_gallery_saver.dart';
import 'package:path_provider/path_provider.dart';
import 'package:permission_handler/permission_handler.dart';

class SaveImageUtils {
  static void save({String path, String url, String ext}) async {
    bool isFile = true;
    if (path != null) {
      if (ext == null) {
        ext = path.substring(path.lastIndexOf("."));
      }
    } else if (url != null) {
      if (ext == null) {
        ext = url.substring(url.lastIndexOf("."));
      }
      var tempDir = await getTemporaryDirectory();
      path = tempDir.path + "/${DateTime.now()}.$ext";
      await Dio().download(url, path);
    }
    switch (ext.toLowerCase()) {
      case "jpg":
      case "png":
      case "jpeg":
      case "bmp":
        isFile = false;
        break;
    }
    if (Platform.isIOS) {
      var status = await Permission.photos.status;
      if (status.isUndetermined) {
        Map<Permission, PermissionStatus> statuses = await [
          Permission.photos,
        ].request();
        save(path: path, url: url);
      }
      if (status.isGranted) {
        var res = await ImageGallerySaver.saveFile(path);
        print("图片保存结果$res");
        if (res["isSuccess"]) {
          Fluttertoast.showToast(msg: "成功保存");
        }
      }
      if (status.isDenied) {
        print("IOS拒绝");
      }
    } else if (Platform.isAndroid) {
      var status = await Permission.storage.status;
      if (status.isUndetermined) {
        Map<Permission, PermissionStatus> statuses = await [
          Permission.storage,
        ].request();
        save(path: path, url: url);
      }
      if (status.isGranted) {
        var res = await ImageGallerySaver.saveFile(path);
        print("图片保存结果$res");
        if (res["isSuccess"]) {
          Fluttertoast.showToast(msg: "成功保存");
        }
      }
      if (status.isDenied) {
        print("Android拒绝");
      }
    }
  }
}

```

