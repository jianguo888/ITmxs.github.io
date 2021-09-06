---
title: "在Flutter中离线存储数据的4种方法"
date: 2021-09-06T16:59:42+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter","dart"]
categories: ["Flutter","dart"]
---

# 在 Flutter 中离线存储数据的 4 种方法

在很多情况下，您需要在用户设备上本地存储数据，而不是使用远程云服务器或 API，例如，您可能需要在应用程序启动时保留数据，或者从互联网下载语言词典并保存以备后用离线使用。本文将向您介绍 4 种解决方案来做到这一点。

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/image/Flutter-store-data.jpg)



## 使用文本/CSV/JSON 文件

用于保存数据的最常见文件类型是 TXT、CSV 和 JSON。您可以在您的应用程序中使用其中的一个或一些。放置数据文件的位置是设备的**Documents**目录。在 Android 上，它是**AppData**目录，在 iOS 上，它是**NSDocumentDirectory**。

为了正确获取路径，我们可以使用包名[path_provider](https://pub.dev/packages/path_provider)。

示例代码：

```dart
Future<String> get _getLocalPath async {
  final dir = await getApplicationDocumentsDirectory();
  return dir.path;
}
```

退出应用程序、重新启动或关闭设备不会丢失数据。只有在删除相应的应用程序时才会删除数据。

## SQLite

SQLite 是一个 C 语言库，它实现了一个小型、快速、自包含、高可靠性、功能齐全的 SQL 数据库引擎。它内置于所有手机中，并捆绑在人们每天使用的无数其他应用程序中。

SQLite 查询可能会让新手感到困惑，但使用 SQLite 将帮助您轻松地对大量数据执行 INSERT、READ、UPDATE 和 DELETE 操作。

在 Flutter 中，您可以使用[sqflite](https://pub.dev/packages/sqflite)插件（名称包含“f”字符）与 SQLite 进行交互。以下是 UPDATE 查询的示例：

```dart
int count = await database.rawUpdate(
    'UPDATE Test SET name = ?, value = ? WHERE name = ?',
    ['updated name', '9876', 'some name']);
```

## 蜂巢数据库



如果您之前使用过 MongoDB，那么您可能会喜欢使用[HIVE 数据库](https://pub.dev/packages/hive)。这是一个用纯 Dart 编写的快速、轻量级、NoSQL、键值数据库。你可以像map一样工作：

```dart
var box = Hive.box('myBox');
box.put('name', 'David');
var name = box.get('name');
print('Name: $name');
```

一般来说，使用 Hive 数据库比使用 SQLite 更简单。

## SharedPreferences存储

[SharedPreferences](https://pub.dev/packages/shared_preferences)是一个流行的插件，它为简单数据（iOS 和 macOS 上的 NSUserDefaults，Android 上的 SharedPreferences）提供特定于平台的持久存储。数据可能会异步持久化到磁盘，并**不能保证**写入返回后会持久化到磁盘，因此该插件不得用于存储关键数据。

示例用法：

```dart
SharedPreferences prefs = await SharedPreferences.getInstance();

// Reading data
String name = prefs.getString('name');
   
// Updating
await prefs.setString('name', 'Some Value');
```

## 总结

本文介绍了当今 Flutter 应用中最流行的离线数据存储方式。

- [Flutter：如何读写文本文件](https://www.kindacode.com/article/flutter-how-to-read-and-write-text-files/)
- [Flutter：从 CSV 文件加载和显示内容](https://www.kindacode.com/article/flutter-load-and-display-content-from-csv-files/)
- [如何使用共享首选项在 Flutter 中本地保存数据](https://www.kindacode.com/article/how-to-locally-save-data-in-flutter/)
- [如何在 Flutter 中编码/解码 JSON](https://www.kindacode.com/article/how-to-encode-decode-json-in-flutter/)
