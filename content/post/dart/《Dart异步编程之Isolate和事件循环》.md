---
title: "《Dart异步编程之Isolate和事件循环》"
subtitle: ""
date: 2021-07-31T20:56:14+08:00
lastmod: 2021-07-31T20:56:14+08:00
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



> 本文主要介绍

<!--more-->

Dart 异步编程之 Isolate 和事件循环。编译自 [Dart asynchronous programming: Isolates and event loops](https://medium.com/dartlang/dart-asynchronous-programming-isolates-and-event-loops-bffc3e296a6a)。



尽管 Dart 是个单线程任务，但它提供 Future、Stream、后台任务以及其他特性用于编写现代异步程序以及响应式程序(Flutter)。本文讲的是 Dart 后台任务的基础：Isolate 和事件循环。

如果你倾向于通过视频学习，可以在以下视频中找到本文讲解的内容。这个视频是 the Flutter in Focus 系列的一部分《Dart 中的异步编程》：

Still here? Let’s talk about isolates.

我们先从 Isolate 开始。

# Isolates

------

译者补充：

> 大多数应用程序中，线程的数量都不止一个。多个线程可以互不干扰地并发执行，并共享进程的全局变量和堆的数据。
> 线程的访问非常自由，它可以访问进程内存里的所有数据，甚至包括其他线程的堆栈
> 《程序员的自我修养》

------

所有的 Dart 代码都运行在 Isolate 中。Isolate 有自己私有的内存空间和一个基于事件循环的线程。





![Two isolates, each with its own memory and thread of execution.](https://luckly007.oss-cn-beijing.aliyuncs.com/img/15747423247738.jpg)



在其他很多语言中，比如 C++，多个线程可运行任何代码并共享内存。但 Dart 中每个线程在其自己的 Isolate 中，有自己的内存，线程只处理事件(后面很快会详细展开)。

大部分 Dart 应用在一个 Isolate 中运行全部代码，也可以根据需要创建更多 Isolate。如果某个操作计算量如此之大以至于它在主 Isolate 运行中会导致掉帧，可以使用 [Isolate.spawn()](https://api.dartlang.org/stable/dart-isolate/Isolate/spawn.html) 或[Flutter’s compute() function](https://flutter.dev/docs/cookbook/networking/background-parsing#4-move-this-work-to-a-separate-isolate) 方法。这些方法都会创建独立的 Isolate 来做密集计算，让主 Isolate 专注重建和渲染 Widget 树。

![Two isolates, each with its own memory and thread of execution.](https://luckly007.oss-cn-beijing.aliyuncs.com/img/15747512192242.jpg)

新创建的 Isolate 有自己的事件循环和内存，原先的 Isolate (即创建新 Isolate 的那个 Isolate) 不能访问这些内存。这种机制正是 Isolate 名字的来源：内存块之间彼此**隔离**。

事实上，Isolate 之间能协作的唯一方式是消息传递。一个 Isolate 可以向另一个 Isolate 发送消息，接收方在其事件循环处理收到的消息。

![Isolates can send messages to other isolates.](https://luckly007.oss-cn-beijing.aliyuncs.com/img/15747423247738.jpg)

缺少共享内存听起来过于严格，尤其是你之前用过 Java 或 C++，但它给 Dart 开发者带来一些重要的好处。

比如，Isolate 中内存分配和垃圾回收不需要锁定。Isolate 中只有一个线程，如果它不是很忙的话，内存并不会快速变化，所以不必锁定。这非常适合 Flutter 应用，它时常要迅速地构建和销毁 Widget 树。

# Event loops

现在你已经了解 Isolate 了，再来看看事件循环是如何让异步代码变成可能的吧。

想像一下应用沿着时间线的运行过程。应用启动，应用停止，之间发生一串串事件：磁盘 IO，用户手势以及类似一些事件。

应用无法预测事件何时发生、以何种顺序发生，它必须在单个线程中处理所有事件并且保证不阻塞。所以应用会运行一个事件循环。它从事件队列中取出最老的事件进行处理，然后再取下一个事件，依次进行，直到事件队列为空。

应用一直在运行：你点击屏幕、下载数据、触发定时器。事件循环一直在运行，每次处理一个事件。

![The event loop processes one event at a time.](https://luckly007.oss-cn-beijing.aliyuncs.com/img/15747423247738.jpg)

事件循环空闲时，线程会暂停并循环下一个事件。这时可能触发垃圾回收器等等。Dart 为异步编程提供的所有高级 API 和语言特性，如 Future、Stream、`async/await`，都是基于和围绕这个基本的循环。

比如，某个按钮用于发起网络请求，就像这样：

```
RaisedButton(
  child: Text('Click me'),
  onPressed: () {
    final myFuture = http.get('https://example.com');
    myFuture.then((response) {
      if (response.statusCode == 200) {
        print('Success!');
      }
    });
  },
)
```

你运行应用时，Flutter 构建按钮并显示到屏幕，之后应用开始等待。

应用的事件循环处于空闲，等待下一个事件。当按钮等待点击时，跟按钮不相关的事件可能发生并进入到事件队列被处理。当点击事件发生时，最终会进入队列。

点击事件被取到，等待处理。Flutter 看到这个事件，它的渲染系统说 “事件坐标跟 RaisedButton 匹配”，所以 Flutter 执行 `onPressed` 函数。这个函数会发起网络请求(返回一个 Future)并使用 `then()` 方法注册 completion handler。

整个过程就是这样的。事件循环处理完点击事件后将其抛弃。

`onPressed` 是 [RaisedButton](https://api.flutter.dev/flutter/material/RaisedButton-class.html) 的一个属性，而网络事件为 Future 添加了一个回调，但两者都是在相同的基本操作。它们都是在告诉 Flutter，”你好，一会儿将发生某个事件，你记得执行该事件的代码。”

`onPressed` 在等待点击，而 Future 在等待网络数据，从 Dart 的视角，这些都是队列中的事件。

这也正是 Dart 中异步代码的工作方式。Future、Steam、以及 `async/await`，这些 API 都是你告诉 Dart 事件循环执行代码的一种方式。

如果再来回头看刚才的例子，你可以准确地看到它是如何为特定的事件被分解成一小块一小块的。

1. 初始的 UI 构建事件
2. 点击事件
3. 网络响应事件

```
RaisedButton( // (1)
  child: Text('Click me'),
  onPressed: () { // (2)
    final myFuture = http.get('https://example.com');
    myFuture.then((response) { // (3)
      if (response.statusCode == 200) {
        print('Success!');
      }
    });
  },
)
```

你习惯异步代码之后，到处都可以看到这些模式。理解事件循环对你跟高级 API 打交道时同样有帮助。

# 总结

我们简单地了解了 Dart 中的 Isolate、事件循环以及异步编程基础。

如果你想了解更多，请看这个关于异步编程的视频。它讨论的是 Future API，这个 API 用于简化异步程序代码。

\