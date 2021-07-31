---
title: "《FutureBuilder与StreamBuilder》"
subtitle: ""
date: 2021-07-31T21:20:54+08:00
lastmod: 2021-07-31T21:20:54+08:00
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



> 本文主要翻译一些 Future 和 FutureBuilder 以及 Stream 和 StreamBuilder 相关的资料。

<!--more-->





# FutureBuilder

FutureBuilder 是一个基于 Future 最后一次结果进行构建的 Widget。

Future 必须在较早的时间点获取，比如 [State.initState](https://api.flutter.dev/flutter/widgets/State/initState.html)、`State.didUpdateConfig` 或 [State.didChangeDependencies](https://api.flutter.dev/flutter/widgets/State/didChangeDependencies.html)。一定不要在 [State.build](https://api.flutter.dev/flutter/widgets/State/build.html) 或 [StatelessWidget.build](https://api.flutter.dev/flutter/widgets/StatelessWidget/build.html) 方法中构建 FutureBuilder 的同时去获取 Future。如果创建 FutureBuilder 的同时也去创建 Future，FutureBuilder 的父节点每次构建时会导致异步任务也重启。

Future 结束时调用 [State.setState](https://api.flutter.dev/flutter/widgets/State/setState.html) 以便让 Widget 重建。Flutter 流水线决定如何调用 [builder](https://api.flutter.dev/flutter/widgets/FutureBuilder/builder.html) 回调，该回调接收一个跟时间无关的、代表 Future 交互过程的 snapshot 序列 (receive a timing-dependent sub-sequence of the snapshots that represent the interaction with the future)

## Builder contract

假设 [initialData](https://api.flutter.dev/flutter/widgets/FutureBuilder/initialData.html) 为 null，对于成功结束的 Future `builder` 被回调两次(`waiting` 和 `done`)或一次(`done`)

- `waiting` - `new AsyncSnapshot<String>.withData(ConnectionState.waiting, null)`
- `done` - `new AsyncSnapshot<String>.withData(ConnectionState.done, 'some data')`

对于出错的 Future `builder` 被回调两次(`waiting` 和 `error`)或一次 (`done`)

- `waiting` - `new AsyncSnapshot<String>.withData(ConnectionState.waiting, null)`
- `error` - `new AsyncSnapshot<String>.withError(ConnectionState.done, 'some error')`

通过 [initialData](https://api.flutter.dev/flutter/widgets/FutureBuilder/initialData.html) 来指定初始的 snapshot (initial snapshot data)。使用这个字段来保证 `builder` 在 Future 结束之前也被调用一次，这时的 snapshot 持有的值即 `initialData` 而非缺省的 `null`。

The data and error fields of the snapshot change only as the connection state field transitions from waiting to done, and they will be retained when changing the FutureBuilder configuration to another future. If the old future has already completed successfully with data as above, changing configuration to a new future results in snapshot pairs of the form:

只在连接状态字段 (the connection state field) 从 `waiting` 变成 `done` 时 snapshot 的 `data` 和 `error` 字段才会变化。FutureBuilder 的 future 由原先切换成新的 future 时 `data` 和 `error` 并不变化。

If the old future has already completed successfully with data as above, changing configuration to a new future results in snapshot pairs of the form:

仅当旧的 future 成功结束时，切换到一个新的 Future 才会导致出现如下变化：

- `new AsyncSnapshot<String>.withData(ConnectionState.none, 'data of first future')`
- `new AsyncSnapshot<String>.withData(ConnectionState.waiting, 'data of second future')`

In general, the latter will be produced only when the new future is non-null, and the former only when the old future is non-null.

A FutureBuilder behaves identically to a StreamBuilder configured with future?.asStream(), except that snapshots with ConnectionState.active may appear for the latter, depending on how the stream is implemented.

# Stream

原文见[这里](https://itnext.io/how-to-use-streams-in-dart-part-1-4503fec0cdd7)。

对多数想要深入了解 Dart (或者有类似概念)的程序员来说 Stream 是个理解起来有挑战的话题，因为想要弄懂这个概念需要尝试一些例子。本文尝试讲清 Dart Stream 的用法，并且在系列文章中构建一些能感知的例子加强理解。

## Dart Stream 用法

原文见[这里](https://itnext.io/how-to-use-streams-in-dart-part-1-4503fec0cdd7)。

对多数想要深入了解 Dart (或者有类似概念)的程序员来说 Stream 是个理解起来有挑战的话题，因为想要弄懂这个概念需要尝试一些例子。本文尝试讲清 Dart Stream 的用法，并且在系列文章中构建一些能感知的例子加强理解。

## Stream 是什么

Dart 官方文档中是这样定义 Stream 的：

> A source of asynchronous data events. A Stream provides a way to receive a sequence of events. Each event is either a data event, also called an element of the stream, or an error event, which is a notification that something has failed. When a stream has emitted all its event, a single “done” event will notify the listener that the end has been reached.
>
> api.dartlang.org

翻译如下(发现跟 [RxJava](https://github.com/ReactiveX/RxJava) 中 [Flowable](http://reactivex.io/RxJava/3.x/javadoc/io/reactivex/Flowable.html) 的概念基本一样的)

> 它是异步数据事件源。Stream 提供接收事件序列的方法。每个事件要么是数据事件，也称为 stream 元素；要么是错误事件，它代表某个失败。当 stream 发射完所有事件后，会使用 “done” 事件通知监听器 stream 已结束。

Stream 指的是数据从 A 流动到的 B 的通道。在这个通道中可以在到达 B 之前对”读入”的数据进行不同的变换。以小块来传输而不是整体传输数据时这个通道非常有用。

Dart 中使用 SDK 提供的工具类来使用 Stream。这些工具类提供方法将数据推到 stream 中，并通知 stream 的监听器捕获数据。

代表 stream 的最通用的类是 `Stream<T>`。但通常不直接使用这个类，而是在 Dart 库的其他类中向外暴露这个类。所以可以将其视为用于跟数据流动通道交互的接口。

## Basic example with StreamController

`StreamController<T>` 包含一个 stream，允许消费者向它发送数据事件、结束事件以及错误事件。可以使用 `streamController.stream` 访问及调用[文档](https://www.sunmoonblog.com/2020/06/04/future-builder-stream-builder/[documentation](https://api.dartlang.org/stable/2.1.1/dart-async/Stream-class.html?source=post_page---------------------------))中定义的方法。

来看个 `StreamController<T>` 类的例子:

```
var streamController = StreamController();
// Accessing the stream and listening for data event
streamController.stream.listen((data) {
  print('Got eem! $data');
});
```

以上代码片断允许监听 stream 通道上输入的数据块。我们对这个数据的响应方式是将其打印到控制台。

我猜接下来的疑问是：*如何触发监听器事件呢？* 答案是：*向 stream 喂数据。* 通过 `EventSink<T>` 的 `add()` 方法向 stream 喂数据。

```
streamController.sink.add('Added this string');
// Result
// Got eem! Added this string
```

stream 上的 `listen()` 方法也能用于捕获错误信息。监听 stream 时会生成一个 `StreamSubscription<T>` 对象。这个对象可用于处理不同的事件，比如数据、数据、结束 (调用 stream 的 `close()` 方法时产生结束事件)。

这是 `listen()` 方法的完整定义：

```
StreamSubscription<T> listen (
  void onData(T event), 
  {
    Function onError,
    void onDone(), // Invoked when the stream is closed
    bool cancelOnError // Kills the stream when an error occurs
  });
```

像这样调用 “error” 和 “done” 事件：

```
streamController.sink.addError('Houston, we have a problem!'); // Got an error! Houston, we have a problem!
streamController.sink.close(); // Mission complete!
```

[Try this on DartPad](https://dartpad.dartlang.org/3baf3a9c229dcfa962878905e478a1a7?source=post_page---------------------------)

## Streams exposed through libraries

尽管 `StreamController<T>` 允许我们更好精细地控制 stream，但内置的 Dart 库其实内部也大量使用 Stream。比如，以下代码创建一个服务器：

```
import 'dart:io';

void main() async {
  var server = await HttpServer.bind('localhost', 8080);
  // HttpServer exposes a Stream<T> interface
  server.listen((HttpRequest request) {
    request.response.write('Hello, World!');
    request.response.close();
  });
}
```

上面代码初始化一个 `HttpServer` 用于创建 web 服务器。这个类暴露 `Stream<T>` 接口，这意味着我们可以监听这个 stream，它包含用户在浏览器访问该 web 服务器时发出的请求。

另一个例子是 web 浏览器也暴露 stream：

```
import 'dart:html';
void main() {
  var button = querySelector('button');
  
  // `onClick` is a Stream<T> instance that receives user click data events
  button.onClick.listen((_) => print('Button clicked!'));
}
```

浏览器中用户交互行为有点击，滚动及输入等等，这些行为作为”数据”事件发射到 stream 中。另外，HTML 元素也暴露 `Stream<T>` 用于处理用户在页面上的交互。

还有很多类使用 Stream。这里想要说的是，你不必直接初始化 `Stream<T>` 对象，而是 SDK 库中的类为你初始化。

## 结论

Stream 提供一种强大的数据块处理方式。由于是以异步的方式操作，所以得到的好处是以非阻塞式的方式来运行代码。建议阅读文章，尤其是 `dart:async` 库，它包含有用于异步编程的 `Streams` 和 `Futures`。

下一篇将介绍如何对 stream 进行变换，并且展示了 stream 用法的常用设计模式。

------

原文见[这里](https://creativebracket.com/how-to-use-streams-in-dart-2/?source=post_page---------------------------)。

是前一篇中我们学习了 stream，以及两种跟 stream 交互的方式，另外还看到了使用 `StreamController<T>` 类型处理 stream 的灵活性，以及 Dart 库暴露 `Stream<T>` 类型供监听和数据处理。

本篇将先看看如何使用 Stream Transformers 对 stream 进行变换。之后学习 stream 用法的常用设计模式。

## Stream Transformer 是什么?

Stream Transformer 允许对 stream 进行数据变换。这些变换被推进 stream，并且被所有监听器接收。

Dart 使用 `StreamTransformer<S,T>` 类进行 stream 变换，它有三种不同的形式：

```
// 1. Creates a transformer based on the provided `onListen` callback
StreamTransformer(
  StreamSubscription<T> onListen(
    Stream<S> stream,
    bool cancelOnError
  )
);
// 2. Creates a transformer based on the provided `bind` callback fn
StreamTransformer.fromBind(
  Stream<T> bind(Stream<S>)
);
// 3. Creates a transformer that will delegate events to the
// provided `handleData`, `handleError` and `handleDone` callback functions
StreamTransformer.fromHandlers({
  void handleData(
    S data,
    EventSink<T> sink
  ),
  void handleError(
    Object error,
    StackTrace stackTrace,
    EventSink<T> sink
  ),
  void handleDone(
    EventSink<T> sink
  ), 
});
```

For this post I will focus on

本文重点介绍 `StreamTransformer.fromHandlers(...)`，因为它最容易使用。以下是一个例子：

```
var streamTransformer = StreamTransformer<num, num>.fromHandlers(
    handleData: (num data, EventSink sink) {
      // The actual transformation we're making
      // here is multiplying $data by 2
      sink.add(data * 2);
    }, 
    handleError: (Object error, StackTrace stacktrace, EventSink sink) {
      sink.addError('Something went wrong: $error');
    }, 
    handleDone: (EventSink sink) => sink.close(),
  );
```

`StreamTransformer.fromHandlers(...)` 命名构造方法接收回调函数，有三个命名参数：

- `handleData`: 这个方法响应 stream 发射的任意数据事件。它的参数是发射事件中的数据，`EventSink<T>` 实例则是当前变换所属的 stream 的成员。`EventSink<T>.add()` 方法用于向 stream 的监听器重传变换后的数据。
- `handleError`: 这个方法响应 stream 发射的任意错误事件。它的参数包含错误信息，stack trace 以及 `EventSink<T>` 实例。该实例的 `addError()` 方法用于向监听器发送自定义的错误信息。
- `handleDone`: 当 stream 中没有更多数据需要处理时运行这个方法。调用 stream 的 `EventSink<T>` 实例的 `close()` 方法时会出现没有数据需要处理的情况。

使用时，将 `streamTransformer` 作为 stream 的 `transform()` 方法参数：

```
var controller = StreamController<num>();
// Call the `transform` method on the controller's stream
// while passing in the stream transformer
var controllerStream = controller.stream.transform(streamTransformer);
// Just print out transformations to the console
controllerStream.listen(print);
// Add data to stream to see transformations in effect
controller.sink.add(1); // 2
controller.sink.add(2); // 4
controller.sink.add(3); // 6
controller.sink.add(4); // 8
controller.sink.add(5); // 10
```

## Single subscription vs Broadcast streams

有两种形式的 stream：单个订阅或者广播。概念上的区别是单个订阅只一个监听器。添加多个监听器抛出异常：

```
controllerStream.listen(print);
controllerStream.listen(print); // Throws the error below:
// ---
// Uncaught exception: Bad state: Stream has already been listened to.
```

但广播 stream 允许定义多个数据监听器。使用广播 stream 修正以上代码：

```
var controller = StreamController<num>.broadcast();
```

再测试一次，结果如下：

```
controllerStream.listen(print);
controllerStream.listen(print)
// --
// 2
// 2
// 4
// 4
// 6
// 6
// 8
// 8
// 10
// 10
```

## 结论

Dart Stream 的优雅之处在于所有平台的 API 以一致的方式来实现，无论是 web，服务器端还是移动端。这一事实展示了 “light bulb moment”，你能跨平台写可重用的逻辑，只要它用的是 Stream API。 (原文：This fact presents a “light bulb moment” where we can write reusable logic for use across the platforms as long as it utilizes the Stream API.)

这种模式可称为 Business Logic Components ( 简称 BLoC)，我们将在第三部分介绍它。

## 延伸阅读

- [Asynchronous Programming: Streams](https://www.dartlang.org/tutorials/language/streams)
- [StreamTransformer Class Documentation](https://api.dartlang.org/stable/2.2.0/dart-async/StreamTransformer-class.html)

------

# 参考

- [FutureBuilder class - widgets library - Dart API](https://api.flutter.dev/flutter/widgets/FutureBuilder-class.html)