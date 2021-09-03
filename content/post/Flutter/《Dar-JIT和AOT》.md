---
title: "《DarJIT和AOT》"
subtitle: ""
date: 2021-06-24T21:41:33+08:00
lastmod: 2021-06-24T21:41:33+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter"]
categories: ["Flutter"]

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



> 本文主要简单聊聊 Dart 的 JIT 和 AOT 模式

<!--more-->

Dart 官方有如下介绍：

> Dart Native includes both a Dart VM with JIT (just-in-time) compilation and an AOT (ahead-of-time) compiler for producing machine code

![-w725](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2019/11/13/15734544331781.jpg)

> Dart Native enables running Dart code compiled to native ARM or X64 machine code for mobile, desktop, and server apps.

简单来说，Dart Native 同时支持 JIT 和 AOT。Dart 这种设计好处如下：

- Dart VM JIT 便于轻量快速开发，它既支持纯解释执行，也可对代码进行运行时优化
- Dart AOT 编译器将代码编译成原生的 ARM 或 X64 代码，提升发布的代码运行效率

# 命令行工具

Dart SDK 分别为 JIT 和 AOT 提供不同的工具。

## dart VM tool

[dart | Dart](https://dart.dev/tools/dart-vm)。

> You can use the dart tool (bin/dart) to run Dart command-line apps such as server-side scripts, programs, and servers.

`dart` 命令支持如下的常用选项：

- `--enable-asserts` - 开启断言
- `--version` - 查看 Dart VM 版本

另外它还支持 Observatory 选项，用于分析和调试 Dart 程序。常用选项包括：

- `--enable-vm-service` - 在本地 8181 端口开启 Observatory 服务
- `--observe` - 相当于同时指定 `--enable-vm-service` 和 `--pause-isolates-on-exit`

![-w794](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2019/11/13/15734559193856.jpg)

(小技巧：如果你是使用的是 iTerm，按住 Command 键后将光标放在)

关于 Observatory 的更多用法见 [Observatory](https://dart-lang.github.io/observatory/)。

## [dart2native](https://dart.dev/tools/dart2native)

> Releases before Dart 2.6 contained a tool named dart2aot that produced AOT snapshots. The dart2native command replaces dart2aot and has a superset of the dart2aot functionality.

> Dart 2.6 is now available, with support for compiling to self-contained, native executables.

Dart 语言本身还在快速演化中。所以要注意以下变化：

- 在 Dart 2.6 以及之前版本，`dart2aot` 用于生成 AOT 镜像
- Dart 2.6 之后，`dart2native` 命令取代了 `dart2aot` (前者是后者的超集)。

Dart 2.6 近期刚刚发布，这个版本开始才支持将 Dart 代码编译成自包含、独立的原生可执行程序。(当然，这只是形式上的改进，并不是 Dart 中的新技术，因为相关技术很早用于 Flutter 平台了)。

使用 `dart2native` 命令将对 Dart 程序进行 AOT (ahead-of-time) 编译，生成原生 ARM 或 X64 机器码。

`dart2native` 命令的产物分成两种。一种是 **独立的可执行程序** (缺省是这种)，它是原生的机器码，由 Dart 代码及其依赖编译而来，再加上一个很小用于类型检查和垃圾回收的 Dart 运行时。

`dart2native` 命令用法如下：

```
dart2native bin/main.dart -o bin/my_app
cp bin/my_app .
./my_app
```

另一种是 **AOT snapshot**，它不包含 Dart 运行时，所以需要使用 `dartaotruntime` 来运行。

## [dartaotruntime](https://dart.dev/tools/dartaotruntime)

`dartaotruntime` 命令用法如下：

```
dart2native bin/main.dart -k aot
dartaotruntime bin/main.aot
```

Flutter 采用 AOT snapshot 这种形式。讲 Flutter 架构时会看到这张图，

![img](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2019/11/14/15737038527917.jpg)

图中 Flutter Engine 层中的 Dart 即 Dart 运行时，也正是这里提到的 `dartaotruntime`。

不过 Flutter 自带的 Dart SDK 中似乎找不到 `dart2aot` 或 `dart2native` 命令，以下两个 issue 提到了这个问题。

- [dart2aot does not exist on the Dart SDK · Issue #32633 · flutter/flutter](https://github.com/flutter/flutter/issues/32633)
- [The ‘dart2aot’ tool is missing from the Dart SDK debian package · Issue #36892 · dart-lang/sdk](https://github.com/dart-lang/sdk/issues/36892)

[Announcing Dart 2.6 with dart2native: Compile Dart to self-contained, native executables](https://medium.com/dartlang/dart2native-a76c815e6baf) 对 Flutter 中不包含 `dart2aot` 或 `dart2native` 有如下解释：

> If you’re getting the Dart SDK via Flutter, note that current Flutter builds have incomplete dart2native support. Until Dart 2.6 reaches Flutter stable, we recommend you install the Dart 2.6 SDK from dart.dev/get-dart.

可以从 [Dart 官网](https://dart.dev/tools/sdk/archive) 下载安装 Dart SDK，它包含 `dart2native` 命令。

![Dart SDK 中的 dart2native 命令](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2019/11/13/15735268131869.jpg)

# 性能测试

写一个简单的 Dart 程序分别测试下 JIT 和 AOT 模式的性能。

```
// demo.dart
main(List<String> args) {
  double area;
  for (int i = 0; i < 50000000; i++) {
    area = i * 3.14 * 3.14;
  }
}
```

![-w779](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2019/11/13/15735258635004.jpg)

从测试数据可以看到，AOT 下的执行效率确实比 JIT 高多了。

Update: 2019-11-23 16:48

[这篇文章](https://renato.athaydes.com/posts/interesting-dart-features.html#quick-dart-overview) 中 “Dart is as slow as Python” 这一节提到了 Dart 的性能问题，这里可以找到很多参考数据

# 参考

- [Platforms | Dart](https://dart.dev/platforms#dart-native-vm-jit-and-aot)
- [Announcing Dart 2.6 with dart2native: Compile Dart to self-contained, native executables](https://medium.com/dartlang/dart2native-a76c815e6baf)