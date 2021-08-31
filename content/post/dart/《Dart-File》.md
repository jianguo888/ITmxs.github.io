---
title: "《Dart_File》"
subtitle: ""
date: 2021-06-24T21:41:33+08:00
lastmod: 2021-06-24T21:41:33+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["flutter"]
categories: ["flutter"]

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
comment: true
mapbox:
  accessToken: ""
---



> 本文主要简单了解 Dart File 类的用法。

<!--more-->

# Dart File 类

原文见[这里](https://api.dartlang.org/stable/2.4.0/dart-io/File-class.html)

File 表示文件系统中某个文件的引用。

File 实例是个对象，它持有 [path](https://api.dartlang.org/stable/2.4.0/dart-io/File/path.html) 并对其进行操作。可以使用 [parent](https://api.dartlang.org/stable/2.4.0/dart-io/FileSystemEntity/parent.html) getter 获取父目录，`parent` 属性继承自 [FileSystemEntity](https://api.dartlang.org/stable/2.4.0/dart-io/FileSystemEntity-class.html)。

使用 pathname 创建新的 File 对象来访问文件系统上的文件。

```
var myFile = new File('file.txt');
```

File 类包含操作文件及其内容的方法。使用这些方法，可以打开和关闭文件，读写文件，创建和删除文件，以及检查文件是否存在。

读写文件时，可以使用 stream (通过 [openRead](https://api.dartlang.org/stable/2.4.0/dart-io/File/openRead.html))，随机访问操作 (通过 [open](https://api.dartlang.org/stable/2.4.0/dart-io/File/open.html))，或者类似 [readAsString](https://api.dartlang.org/stable/2.4.0/dart-io/File/readAsString.html) 这样的便捷方法。

File 类中的方法大部分都有同步和异步两种形式，比如 [readAsString](https://api.dartlang.org/stable/2.4.0/dart-io/File/readAsString.html) 和 [readAsStringSync](https://api.dartlang.org/stable/2.4.0/dart-io/File/readAsStringSync.html)。除非有特别的理由，通常应当使用异步方法以避免阻塞程序。

如果构造 File 对象的 [path](https://api.dartlang.org/stable/2.4.0/dart-io/File/path.html) 是一个符号链接，而非文件，则 File 类的方法会操作链接指向的最终目标文件。不过，[delete](https://api.dartlang.org/stable/2.4.0/dart-io/FileSystemEntity/delete.html) 和 [deleteSync](https://api.dartlang.org/stable/2.4.0/dart-io/FileSystemEntity/deleteSync.html) 方法除外，这两个方法是对符号链接进行操作。

# 读文件

下面示例代码使用异步的 [readAsString](https://api.dartlang.org/stable/2.4.0/dart-io/File/readAsString.html) 方法读取文件，它将整个文件内容视为一个字符串：

```
import 'dart:async';
import 'dart:io';

void main() {
  new File('file.txt').readAsString().then((String contents) {
    print(contents);
  });
}
```

更灵活更有用的方法是使用 [Stream](https://api.dartlang.org/stable/2.4.0/dart-async/Stream-class.html) 方式读文件。调用 [openRead](https://api.dartlang.org/stable/2.4.0/dart-io/File/openRead.html) 方法打开文件，该方法返回 stream，这个 stream 以字节块的方式返回文件数据。可以监听 stream 来获取数据并进行必要的处理。可以继续使用不同的 transformer 操作数据来得到想要的数据格式。

可以使用 stream 方式来读取大文件，并提供 transformer 来操作数据。

```
import 'dart:io';
import 'dart:convert';
import 'dart:async';

main() {
  final file = new File('file.txt');
  Stream<List<int>> inputStream = file.openRead();

  inputStream
    .transform(utf8.decoder)       // Decode bytes to UTF-8.
    .transform(new LineSplitter()) // Convert stream to individual lines.
    .listen((String line) {        // Process results.
        print('$line: ${line.length} bytes');
      },
      onDone: () { print('File is now closed.'); },
      onError: (e) { print(e.toString()); });
}
```

# 写文件

使用 [writeAsString](https://api.dartlang.org/stable/2.4.0/dart-io/File/writeAsString.html) 方法写文件。

```
import 'dart:io';

void main() {
  final filename = 'file.txt';
  new File(filename).writeAsString('some content')
    .then((File file) {
      // Do something with the file.
    });
}
```

也可以使用 [Stream](https://api.dartlang.org/stable/2.4.0/dart-async/Stream-class.html) 来写入文件。调用 [openWrite](https://api.dartlang.org/stable/2.4.0/dart-io/File/openWrite.html) 方法打开文件，返回结果是 [IOSink](https://api.dartlang.org/stable/2.4.0/dart-io/IOSink-class.html)，可以向 IOSink 写入数据。记得操作完成后调用 [IOSink.close](https://api.dartlang.org/stable/2.4.0/dart-io/IOSink/close.html) 关闭 sink。

```
import 'dart:io';

void main() {
  var file = new File('file.txt');
  var sink = file.openWrite();
  sink.write('FILE ACCESSED ${new DateTime.now()}\n');

  // Close the IOSink to free system resources.
  sink.close();
}
```

# 使用 Future

为避免意外阻塞程序，File 类的一些方法使用 [Future](https://api.dartlang.org/stable/2.4.0/dart-async/Future-class.html) 作为返回值。比如，[length](https://api.dartlang.org/stable/2.4.0/dart-io/File/length.html) 方法用于获取文件长度，返回的是 Future。调用 `then` 方法注册回调函数，获取到文件长度后会回调这个函数。

```
import 'dart:io';

main() {
  final file = new File('file.txt');

  file.length().then((len) {
    print(len);
  });
}
```

除了 `length()` 外，其他几个方法也返回 Future，包括：[exists](https://api.dartlang.org/stable/2.4.0/dart-io/FileSystemEntity/exists.html)，[lastModified](https://api.dartlang.org/stable/2.4.0/dart-io/File/lastModified.html)，[stat](https://api.dartlang.org/stable/2.4.0/dart-io/FileSystemEntity/stat.html) 等等。

# 其他资源

- [Dart by Example](https://www.dartlang.org/dart-by-example/#files-directories-and-symlinks) provides additional task-oriented code samples that show how to use various API from the Directory class and the related File class.
- I[I/O for Command-Line Apps](https://www.dartlang.org/docs/dart-up-and-running/ch03.html#dartio---io-for-command-line-apps) a section from A Tour of the Dart Libraries covers files and directories.
- [Write Command-Line Apps](https://www.dartlang.org/docs/tutorials/cmdline/), a tutorial about writing command-line apps, includes information about files and directories.