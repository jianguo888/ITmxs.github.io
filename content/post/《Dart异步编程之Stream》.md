---
title: "《Dart异步编程之Stream》"
subtitle: ""
date: 2021-07-31T18:38:57+08:00
lastmod: 2021-07-31T18:38:57+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["flutter","dart"]
categories: ["flutter","dart"]

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



> 本文主要翻译 Dart Stream 文档，了解 Stream 的用法。

<!--more-->

原文见[这里](https://dart.dev/tutorials/language/streams)。

重点：

- Steams 提供异步的数据序列
- 数据序列可以用户生成的数据或者从文件读取的数据
- 可以使用 **await for** 或 Stream API 的 `listen()` 方法来处理 stream
- Streams 提供错误处理机制
- Streams 分为两类：单一的 subscription (single subscription) 或广播 (broadcast)

Future 和 Stream 类用于 Dart 异步编程。

Future 表示不能立即结束的计算。普通函数返回计算结果，而异步函数返回 Future，它包含最终的计算结果。Future 会在计算完成时告知结果。

Stream 是异步事件序列。它有点像一个异步的 Iterable，不同之处在于，你不是自己获取下一个事件，而是由 Stream 在下一个事件准备好后通知你。

# 接收 stream 事件

可以通过多种方式创建 streams，这个话题后面文章再讨论。无论哪种方式创建 stream，它们的用法都相同：用于 stream 事件上进行异步 for 循环迭代 (即 **await for**)，就像 Iterable 中进行普通 **for 循环** 迭代一样。比如，

```
Future<int> sumStream(Stream<int> stream) async {
  var sum = 0;
  await for (var value in stream) {
    sum += value;
  }
  return sum;
}
```

这段代码只是接收整数事件 stream 的每个事件，然后返回它们的和 (一个 Future)。循环体结束后，函数暂停并等待下一个事件到来，直到 stream 结束。

使用 **await for** 循环时要求函数带 `async` 关键字。

# 错误事件

Streams 在没有更多事件时结束。使用 **await for** 循环读取事件时，循环在 stream 结束时停止。

某些情况下，stream 结束前出现错误，比如，从远程服务器获取文件时网络断开，或者创建事件的代码有 bug。

Streams 以发送数据事件的相同方式发送错误事件。大多数 streams 会在第一次错误时中止，但也有些 streams 可能多次发送错误事件，还有些 streams 在错误事件后继续发送数据事件。这个文档中我们只讨论最多发送一次错误事件的情况。

使用 **await for** 读取 stream 时，循环语句抛出错误同时结束循环。可以使用 **try-catch** 来捕获错误。

# 使用 stream

`Stream` 类有许多工具方法用于在 stream 上执行常用操作，跟 `Iterable` 的方法类似。比如，可以使用 Stream API 的 `lastWhere()` 方法查找 stream 中最后一个正整数：

```dart
Future<int> lastPositive(Stream<int> stream) =>
    stream.lastWhere((x) => x >= 0);
```

# 两种 Stream

有两种 stream。

## Single subscription streams

最常用的 stream 包含整个事件中的一个小序列。事件必须以正确的顺序分发。读取文件或接收网络响应时得到的正是这种 stream。

## Broadcast streams

另一种 stream 专门用于每次只能处理一条消息的场景。比如，用于浏览器中的鼠标事件。

可以在任何时候监听这个 stream，监听后会收到发送出来的事件。同一时刻可以监听多个 listener，取消前一个订阅后还能再次监听。

# 处理 stream

`Stream<T>` 上的以下方法用于处理 stream 并且返回结果：

```
Future<T> get first;
Future<bool> get isEmpty;
Future<T> get last;
Future<int> get length;
Future<T> get single;
Future<bool> any(bool Function(T element) test);
Future<bool> contains(Object needle);
Future<E> drain<E>([E futureValue]);
Future<T> elementAt(int index);
Future<bool> every(bool Function(T element) test);
Future<T> firstWhere(bool Function(T element) test, {T Function() orElse});
Future<S> fold<S>(S initialValue, S Function(S previous, T element) combine);
Future forEach(void Function(T element) action);
Future<String> join([String separator = ""]);
Future<T> lastWhere(bool Function(T element) test, {T Function() orElse});
Future pipe(StreamConsumer<T> streamConsumer);
Future<T> reduce(T Function(T previous, T element) combine);
Future<T> singleWhere(bool Function(T element) test, {T Function() orElse});
Future<List<T>> toList();
Future<Set<T>> toSet();
```

除了 `drain()` 和 `pipe()`，其他方法都能在 Iterable 中找到相应的方法。这些方法很容易写，在 async 方法使用 **await for** 循环。比如，以下是一些方法的实现：

```
Future<bool> contains(Object needle) async {
  await for (var event in this) {
    if (event == needle) return true;
  }
  return false;
}

Future forEach(void Function(T element) action) async {
  await for (var event in this) {
    action(event);
  }
}

Future<List<T>> toList() async {
  final result = <T>[];
  await this.forEach(result.add);
  return result;
}

Future<String> join([String separator = ""]) async =>
    (await this.toList()).join(separator);
```

(注意，以上只是精简后的代码，由于历史原因实际实现可能更复杂。)

# 修改 stream

Stream 类的以下方法基于原先的 stream 返回一个新的 stream。Each one waits until someone listens on the new stream before listening on the original.

```
Stream<R> cast<R>();
Stream<S> expand<S>(Iterable<S> Function(T element) convert);
Stream<S> map<S>(S Function(T event) convert);
Stream<R> retype<R>();
Stream<T> skip(int count);
Stream<T> skipWhile(bool Function(T element) test);
Stream<T> take(int count);
Stream<T> takeWhile(bool Function(T element) test);
Stream<T> where(bool Function(T event) test);
```

相应地，Iterable 中也有类似的方法用于将一个 iterable 转换成另外一个 iterable。这些方法很容易使用带 **await for** 循环的 async 方法编写。

```
Stream<E> asyncExpand<E>(Stream<E> Function(T event) convert);
Stream<E> asyncMap<E>(FutureOr<E> Function(T event) convert);
Stream<T> distinct([bool Function(T previous, T next) equals]);
```

`asyncExpand()` 和 `asyncMap()` 函数跟 `expand()` 和 `map()` 类似，但允许其函数参数为异步函数。

```
Stream<T> handleError(Function onError, {bool test(error)});
Stream<T> timeout(Duration timeLimit,
    {void Function(EventSink<T> sink) onTimeout});
Stream<S> transform<S>(StreamTransformer<T, S> streamTransformer);
```

最后的三个方法更特别。它们用于处理 **await for** 循环不能处理的错误，循环中首次出现的错误会导致循环结束，对 stream 的订阅也被结束。没法从这种情况恢复。可以使用 `handleError()` 在错误事件到达 **await for** 循环之前从 stream 中移除错误。(原文：You can use handleError() to remove errors from a stream before using it in an await for loop.)

## transform() function

`transform()` 函数不仅仅用于错误处理，还能用于对 stream 进行更通用的 “map”。通常的 map 要求为每个收到的事件得到一个值。但是在 I/O 操作中，常常出现几个输入事件生成一个输出事件的情形。`StreamTransformer` 用于处理这种情况。比如，`Utf8Decoder` 等解码器也是 transformer。transformer 只需要实现 `bind()` 方法。

```
Stream<S> mapLogErrors<S, T>(
  Stream<T> stream,
  S Function(T event) convert,
) async* {
  var streamWithoutErrors = stream.handleError((e) => log(e));
  await for (var event in streamWithoutErrors) {
    yield convert(event);
  }
}
```

## Reading and decoding a file

以下代码读文件并且对 stream 运用两个 transform。首先从 UTF8 转换数据，然后再使用 `LineSplitter`。最后打印所有行，除了以 `#` 开头的行。

```
import 'dart:convert';
import 'dart:io';

Future<void> main(List<String> args) async {
  var file = File(args[0]);
  var lines = utf8.decoder
      .bind(file.openRead())
      .transform(LineSplitter());
  await for (var line in lines) {
    if (!line.startsWith('#')) print(line);
  }
}
```

# The listen() method

最后要介绍的是 Stream 中的 `listen()` 方法。它是一个低级方法，所有的其他 stream 方法都是某种意义上的 `listen()` 方法。

```
StreamSubscription<T> listen(void Function(T event) onData,
    {Function onError, void Function() onDone, bool cancelOnError});
```

要创建新的 `Stream` 类型，只需要继承 `Stream` 类并且实现 `listen()` 方法。`Stream` 上的其他方法都会调用 `listen()` 方法。

`listen()` 方法允许监听 stream。直接被监听，否则 stream 就是静态的。开始监听后，将返回 `StreamSubscription` 对象，它代表活动的、可以产生事件的流。这跟 `Iterable` 类似，它只是对象集合，itertor 才会真正进行迭代。

stream subscription 允许暂停以及恢复，或者完全取消。还能设置回调，当产生数据事件、错误事件或者 stream 被关闭时都会收到回调。

# 其他资源

阅读以下 stream 用法及 Dart 异步编程文档，了解更多内容。

- [Creating Streams in Dart](https://dart.dev/articles/libraries/creating-streams), an article about creating your own streams
- [Futures and Error Handling](https://dart.dev/guides/libraries/futures-error-handling), an article that explains how to handle errors using the Future API
- [Asynchrony support](https://dart.dev/guides/language/language-tour#asynchrony-support), a section in the [language tour](https://dart.dev/guides/language/language-tour)
- [Stream API reference](https://api.dart.dev/stable/dart-async/Stream-class.html)