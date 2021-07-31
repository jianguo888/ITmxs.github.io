---
title: "《Dart之笨办法学Dart Dartdoc"
subtitle: ""
date: 2021-07-31T21:00:31+08:00
lastmod: 2021-07-31T21:00:31+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["dart"]
categories: ["dart"]

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



> Flutter SDK 的 [dartdoc.dart](https://github.com/flutter/flutter/blob/master/dev/tools/dartdoc.dart) 脚本不仅可以演示了 `dartdoc` 各参数的用法，它也是学习 Dart 编程语言的绝好参考资料。

<!--more-->





Dart SDK 中的 `dartdoc` 命令用于从 Dart 源码生成文档。使用过程中遇到问题，一番搜索后发现 Flutter SDK 的 `dartdoc.dart` 脚本是对该命令的封装。这个脚本用于生成 Flutter 文档。我发现从 `dartdoc.dart` 中不仅能学习到 `dartdoc` 各参数的用法，它还是学习 Dart 编程语言的绝好参考资料。学习并记录一下。(看似挺笨的学习方法，但感觉有效)

一方面是 Dart 语言相关的知识：

- 第三方简单用法
  - argParser
  - path
  - process
- 文件操作
  - 文件生成和读写
  - 切换及遍历目录
- 进程操作
- Dart 语言特性
  - Synchronous generator
  - 扩展操作符
  - 字符串操作
- 正则表达式

另一方面是 Dart dartdoc 的用法。

dartdoc.dart 脚本见 [Github](https://github.com/flutter/flutter/blob/master/dev/tools/dartdoc.dart)。另外注意区分 dartdoc.dart 和 Dart `dartdoc`，前者是 Flutter SDK 中用于生成文档的脚本，是对后者的封装；后者则是 Dart SDK 的内置命令。

# Dart 语言

`dartdoc.dart` 函数了以下内容。

## args 库

首先要引入 [args库](https://pub.dev/packages/args)，在 `pubspec.yaml` 中添加如下依赖：

```
dependencies:
  args: 1.5.2
```

arg 库的基本用法包括：

- 创建 ArgParser - `ArgParser()`

- 添加 flag - `ArgParser.addFlag()`

- 生成 ArgResults - `ArgParser.parse()`

- 访问

   

  ```
  ArgResults
  ```

  - 显示当前程序的用法 - `ArgResults.usage`
  - 访问传给当前程序的参数 - `[]`

示例代码如下：

```
ArgParser _createArgsParser() {
  // 1 - 创建 ArgParser
  final ArgParser parser = ArgParser();
  parser.addFlag('help', abbr: 'h', negatable: false,
      help: 'Show command help.');
  // 2 - 添加 flag
  parser.addFlag('verbose', negatable: true, defaultsTo: true,
      help: 'Whether to report all error messages (on) or attempt to '
          'filter out some known false positives (off).  Shut this off '
          'locally if you want to address Flutter-specific issues.');
  ...
  return parser;
}

Future<void> main(List<String> arguments) async {
  final ArgParser argParser = _createArgsParser();
  // 3 - 生成 ArgResults
  final ArgResults args = argParser.parse(arguments);
  if (args['help']) {
    print ('Usage:');
    // 4 - 访问 `ArgResults`
    print (argParser.usage);
    exit(0);
  }
}
```

Flag 支持的选项包括：

- `negatable` - 是否可忽略
- `abbr` - 缩写命令，比如 `--help` 的短命令是 `-h`
- `defaultsTo` - 缺省值

## path 库

首先要引入 [path库](https://pub.dev/packages/path)，在 `pubspec.yaml` 中添加如下依赖：

```
dependencies:
  path: 1.6.4
```

Dart 中使用 `Directory` 类可获取当前目录，通过 path 库可获取到目录名和文件名。示例代码如下：

```
// 获取当前目录
Directory.current.path; // -> /Users/cm/wd/flutter_web/lib/learn_dartdoc

// 切换目录
if (path.basename(Directory.current.path) == 'tools') {
  Directory.current = Directory.current.parent.parent;
}

// 获取当前目录名
path.basename('path/to/foo.dart'); // -> 'foo.dart'
path.basename('path/to');          // -> 'to'
path.basename('path/to/'); // -> 'to'
```

## process 库

`dartdoc.dart` 脚本中有这样一段代码：

```
final ProcessResult flutter = Process.runSync('flutter', <String>[]);
final File versionFile = File('version');
if (flutter.exitCode != 0 || !versionFile.existsSync())
  throw Exception('Failed to determine Flutter version.');
```

这段代码的功能包括：

- 使用 `Process.runSync()` 启动 `flutter` 命令并检查是否运行成功
- 使用 `File.existsSync()` 检查 `<FLUTTER DIR>/version` 文件是否存在

引入 [path库](https://pub.dev/packages/process)，在 `pubspec.yaml` 中添加如下依赖：

```
dependencies:
  process: 3.0.12
```

[process 库](https://pub.dev/packages/process) 的 `ProcessWrapper` 是对 Dart `Process` 的包装，以提供更方便的方法。其用法如下：

```
// Run pub.
ProcessWrapper process = ProcessWrapper(await Process.start(
  pubExecutable,
  <String>['get'],
  workingDirectory: kDocsRoot,
  environment: pubEnvironment,
));
printStream(process.stdout, prefix: 'pub:stdout: ');
printStream(process.stderr, prefix: 'pub:stderr: ');
final int code = await process.done;
```

## 文件操作

`dartdoc.dart` 脚本中用到几种基本的文件操作，

- 写文件
- 读文件
- 创建目录
- 遍历目录

`File.writeAsStringSync(String)` 方法用于将字符串写入文件。用法如下：

```
// Create the pubspec.yaml file.
final StringBuffer buf = StringBuffer();
buf.writeln('name: Flutter');
buf.writeln('homepage: https://flutter.dev');
...
File('$kDocsRoot/pubspec.yaml').writeAsStringSync(buf.toString());
```

`Directory.createSync()` 方法用于创建目录。用法如下：

```
// Create the library file.
final Directory libDir = Directory('$kDocsRoot/lib');
libDir.createSync();
```

`dartdoc.dart` 包含的一个功能是遍历 Flutter SDK `packages` 目录来找到所有的库路径：

```
/// Returns import or on-disk paths for all libraries in the Flutter SDK.
Iterable<String> libraryRefs() sync* {
  for (Directory dir in findPackages()) {
    final String dirName = path.basename(dir.path);
    for (FileSystemEntity file in Directory('${dir.path}/lib').listSync()) {
      if (file is File && file.path.endsWith('.dart')) {
        yield '$dirName/${path.basename(file.path)}';
      }
    }
  }

  // Add a fake package for platform integration APIs.
  yield 'platform_integration/android.dart';
  yield 'platform_integration/ios.dart';
}

/// Finds all packages in the Flutter SDK
List<FileSystemEntity> findPackages() {
  return Directory('<FLUTTER SDK>/packages')
    .listSync()
    .where((FileSystemEntity entity) {
      return false;
    })
    .cast<Directory>()
    .toList();
}
```

从这段代码可以学到两个知识点：

- 首先，注意 `libraryRefs()` 方法中用到的 `sync*` 和 `yield` 的用法。实际上这里返回的 `Iterable` 是 [synchronous generator](https://dart.dev/guides/language/language-tour#generators)
- 其次，`Directory.listSync()` 返回当前目录中的所有文件和目录。该方法的参数 `recursive` 用于支持递归遍历

## 正则式

`dartdoc.dart` 中用到了正则式。限于篇幅，这里不展开。

```
printStream(process.stdout, prefix: args['json'] ? '' : 'dartdoc:stdout: ',
  filter: args['verbose'] ? const <Pattern>[] : <Pattern>[
    RegExp(r'^generating docs for library '), // unnecessary verbosity
    RegExp(r'^pars'), // unnecessary verbosity
  ],
);
printStream(process.stderr, prefix: args['json'] ? '' : 'dartdoc:stderr: ',
  filter: args['verbose'] ? const <Pattern>[] : <Pattern>[
    RegExp(r'^ warning: .+: \(.+/\.pub-cache/hosted/pub.dartlang.org/.+\)'), // packages outside our control
  ],
);
```

## 其他

这里来看一些小的语言特性。小则小，用起来确实方便。

一，可以将 `if` 表达式用于 List 字面量的创建。

```
final List<String> dartdocBaseArgs = <String>[
  'global',
  'run',
  if (args['checked']) '-c',
  'dartdoc',
];
```

二，Dart 提供 `...` 扩展操作符。用法如下：

```
final List<String> dartdocBaseArgs = <String>[
  'global',
  'run',
  if (args['checked']) '-c',
  'dartdoc',
];

// Verify which version of dartdoc we're using.
final ProcessResult result = Process.runSync(
  pubExecutable,
  <String>[...dartdocBaseArgs, '--version'],
  workingDirectory: kDocsRoot,
  environment: pubEnvironment,
);
```

# [Dart dartdoc 命令](https://github.com/dart-lang/dartdoc#dartdoc)

> Use dartdoc to generate HTML documentaton for your Dart package.

Dart dartdoc 是 Dart SDK 中用于为 Dart package 生成 HTML 文档的命令行工具。

要正确使用这个工具，建议先简单了解以下内容：

- [Dart package 的组织方式](https://dart.dev/guides/libraries/create-library-packages)
- [Dart 注释文档最佳实践](https://dart.dev/guides/language/effective-dart/documentation#doc-comments)

最简单的 Dart package 目录结构如下：

![img](https://www.sunmoonblog.com/images/15749233895070.jpg)

- pubspec file
- lib directory
  - lib 目录下可以创建任意目录结构
  - lib/src 目录下的代码是私有的，通常是具体实现
  - lib 目录下的代码是公开的
  - 可以通过 lib 目录下的文件导出 lib/src 为公开代码

更完整的代码结构可以参考 [shelf](https://github.com/dart-lang/shelf)。

## 使用

Dart SDK 自带 dartdoc，不用另外安装。

- 生成文档 - 在根目录下运行 `dartdoc`
- 查看文档
  - `dhttpd --path doc/api` 启动服务
  - `http://localhost:8080` 查看

## 参数

Dart dartdoc 命令支持相当多的参数。以 Flutter 为例，生成文档时的参数如下：

```
dartdoc
    --allow-tools
    --no-validate-links
    --link-to-source-excludes ../../bin/cache
    --link-to-source-root ../.. 
    --link-to-source-uri-template https://github.com/flutter/flutter/blob/master/%f%#L%l%
    --inject-html
    --header styles.html
    --header analytics.html 
    --header survey.html
    --header snippets.html
    --header opensearch.html
    --footer-text lib/footer.html
    --allow-warnings-in-packages Flutter,flutter,platform_integration,flutter_test,...
    --exclude package:Flutter/temp_doc.dart,package:http/browser_client.dart,...
    --favicon=favicon.ico
    --package-order flutter,Dart,...
    --auto-include-dependencies)
```

实际使用时可以参考 [dart-lang/dartdoc: API documentation tool for Dart.](https://github.com/dart-lang/dartdoc#dartdoc)。

## dartdoc_options.yaml

可以通过在根目录下创建 `dartdoc_options.yaml` 来控制调整生成的文档。

```
dartdoc:
  exclude: []
```

详细参数见 [Github](https://github.com/dart-lang/dartdoc#advanced-features)

## 简单例子

这里用一个简单的例子演示 dartdoc 的用法。代码结构和 `dartdoc_options.yaml` 分别如下：

![-w885](https://www.sunmoonblog.com/images/15749244487548.jpg)

我们在 `exclude` 中排除了 `hello.dart` 和 `bar.dart` 两个文件，而 `lib/src` 下的代码为私有的，所以最终仅为 `hello2.dart` 生成了文档。

![-w773](https://www.sunmoonblog.com/images/15749246280161.jpg)

浏览器中看到了文档是这样的：

![-w723](https://www.sunmoonblog.com/images/15749249251449.jpg)