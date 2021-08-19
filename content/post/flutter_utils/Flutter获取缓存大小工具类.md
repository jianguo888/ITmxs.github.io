---
title: "Flutter获取缓存大小工具类"
date: 2021-08-18T12:41:07+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---



```dart

import 'package:path_provider/path_provider.dart';
import 'dart:io';
import 'package:fluttertoast/fluttertoast.dart';

String cacheSizeStr;
void ClearCache() async {
  Directory tempDir = await getTemporaryDirectory();
  //删除缓存目录
  await delDir(tempDir);
  await LoadCache();
  Fluttertoast.showToast(msg: '清除缓存成功');
}

///加载缓存
Future<Null> LoadCache() async {
  Directory tempDir = await getTemporaryDirectory();
  double value = await _getTotalSizeOfFilesInDir(tempDir);
  /*tempDir.list(followLinks: false,recursive: true).listen((file){
          //打印每个缓存文件的路径
        print(file.path);
      });*/
  // print('临时目录大小: ' + value.toString());

  cacheSizeStr = _renderSize(value); // _cacheSizeStr用来存储大小的值
}

//计算缓存大小
Future<double> _getTotalSizeOfFilesInDir(final FileSystemEntity file) async {
  if (file is File) {
    int length = await file.length();
    return double.parse(length.toString());
  }
  if (file is Directory) {
    final List<FileSystemEntity> children = file.listSync();
    double total = 0;
    if (children != null)
      for (final FileSystemEntity child in children)
        total += await _getTotalSizeOfFilesInDir(child);
    return total;
  }
  return 0;
}

//格式化缓存大小
_renderSize(double value) {
  if (null == value) {
    return 0;
  }
  List<String> unitArr = List()..add('B')..add('K')..add('M')..add('G');
  int index = 0;
  while (value > 1024) {
    index++;
    value = value / 1024;
  }
  String size = value.toStringAsFixed(2);
  return size + unitArr[index];
}

///递归方式删除目录
Future<Null> delDir(FileSystemEntity file) async {
  if (file is Directory) {
    final List<FileSystemEntity> children = file.listSync();
    for (final FileSystemEntity child in children) {
      await delDir(child);
    }
  }
  await file.delete();
}

```

