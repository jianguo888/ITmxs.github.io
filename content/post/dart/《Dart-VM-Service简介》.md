---
title: "《Dart VM Service简介》"
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



> 本文主要介绍Flutter 开发工具 DevTools

<!--more-->

Flutter 开发工具 DevTools 是不是有些神奇？移动端开发中用到的性能工具通常是原生应用，而 DevTools 却是基于浏览器的。为什么要基于浏览器开发，Dart VM Service Protocol 又是什么？简单了解一下。



Dart DevTools 提供 hot reload、UI 调试和内存分析等各种功能。

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/img/15941055868454.jpg)

# Dart VM Service Protocol

先来看看 Dart VM Service Protocol 是什么。

[Dart VM Service Protocol 3.36](https://github.com/dart-lang/sdk/blob/master/runtime/vm/service/service.md#rpcs-requests-and-responses) 介绍如下：

> Service Protocol 协议用于跟运行中的 Dart VM 通信
>
> 使用 `--observe` 启动 Dart VM 后就能使用 Service Protocol。VM 会启动一个 webserver 并通过 WebSocket 为 protocol request 提供服务。也可发送 HTTP (非WebSocket)请求，但这种情况下无法访问 VM *event*
>
> Service Protocol 使用 [JSON-RPC 2.0](http://www.jsonrpc.org/specification) 来通信

## RPC

通过 [getVersion](https://github.com/dart-lang/sdk/blob/master/runtime/vm/service/service.md#getversion) 这个例子来看看 RPC 请求和响应。

RPC request 是一个发送到 server 的 JSON 对象。

```
{
  "jsonrpc": "2.0", // jsonprc 属性可选
  "method": "getVersion", //
  "params": {}, // params 属性在 Dart 代码中以 named parameters 形式提供
  "id": "1" // id 属性可以是 string, number, null
}
```

RPC response 也是一个 JSON 对象。

这是一个成功的 RPC response：

```
{
  "jsonrpc": "2.0",
  "result": { // 请求成功时返回 result 属性
    "type": "Version", // type 参数可用于判断准确的返回类型，它是 Response 类的子类
    "major": 3,
    "minor": 5
  }
  "id": "1" // id 属性跟对应请求中的 id 匹配
}
```

这是一个失败的 RPC response：

```
{
  "jsonrpc": "2.0",
  "error": { // 请求失败时返回 error 属性，它包括 code, message 和 data
    "code": 103,
    "message": "Stream already subscribed",
    "data": {
      "details": "The stream 'GC' is already subscribed"
    }
  }
  "id": "2"
}
```

[JSON-RPC 规范](https://www.sunmoonblog.com/2020/07/07/dart-vm-service/error codes)的基础上，Service Protocol 定义了一些额外的错误码。具体错误见 Service Protocol 文档，这里不展开。

## Event 和 Binary Event

- 通过使用 [streamListen](https://github.com/dart-lang/sdk/blob/master/runtime/vm/service/service.md#streamlisten) 和 [streamCancel](https://github.com/dart-lang/sdk/blob/master/runtime/vm/service/service.md#streamcancel) RPC，客户端可以收到 VM 中特定 stream 上的事件通通知
- 每个 stream 跟一个 *stream id* 关联
- 每个 stream 跟某种类型的事件关联。比如 *Isolate* stream 提供 isolate 相关的创建和销毁等事件
- 客户端通过 WebSocket 异步接收 stream event
- 某些事件跟二进制数据关联，通过 WebSocket binary frame 而不是 text frame 发送这些事件

```
{
  "json-rpc": "2.0",
  "method": "streamNotify",
  "params": { // 参数中包括 streamId 和 event 属性
    "streamId": "Isolate",
    "event": {
      "type": "Event",
      "kind": "IsolateExit",
      "isolate": {
        "type": "@Isolate",
        "id": "isolates/33",
        "number": "51048743613",
        "name": "worker-isolate"
      }
    }
  }
}
```

## Types

Service Protocol 中的每个响应或事件均有 `type` 属性，它是 [Response](https://github.com/dart-lang/sdk/blob/master/runtime/vm/service/service.md#response) 子类。客户端可以通过 `type` 属性判断每种响应或事件的准确类型。

- 以 `@` 开头的 type 表示当前响应是一个 `reference`
- 不以 `@` 开头的 type 表示当前响应是一个 `object`

`reference` 可提供的信息是 `object` 的子集

# Dart VM Service Protocol Extension

[Dart VM Service Protocol Extension](https://github.com/dart-lang/sdk/blob/master/runtime/vm/service/service_extension.md) 与 VM 通信的方式跟 Service Protocol 类似跟类似，但它们并不是核心 [Dart VM Service Protocol](https://github.com/dart-lang/sdk/blob/master/runtime/vm/service/service.md) 的一部分，仅允许通过某些核心库访问。

通过添加前缀名来调用对应的 RPC 接口，类似 “ext.dart.libraryName”。比如，调用 `ext.dart.io.getSocketProfile` 来执行 dart:io 暴露的 `getSocketProfile` RPC 接口。

# 总结

简单了解 Dart VM Service Protocol 和 Dart VM Service Protocol Extension 后，我们有以下认识：

- Service Protocol 和 Service Protocol Extension 是一套 RPC-JSON 协议
- Service Protocol 兼容性好，可以很容易地集成到现有 IDE
- VM 会启动一个 webserver 并通过 WebSocket 为客户端提供服务

为什么要基于浏览器来开发 DevTools？有以下几个原因

- 浏览器天生支持 WebSocket，所以基于浏览器开发 DevTools 是水到渠成
- 基于浏览器开发 DevTools，可兼容多平台
- 当前的 DevTools 是使用 Flutter 框架开发的 (没错，Flutter。以前是纯 Dart 开发)，部署成 Web 应用很方便

# 参考

- [sdk/service.md at master · dart-lang/sdk](https://github.com/dart-lang/sdk/blob/master/runtime/vm/service/service.md#rpcs-requests-and-responses)
- [sdk/service_extension.md at master · dart-lang/sdk](https://github.com/dart-lang/sdk/blob/master/runtime/vm/service/service_extension.md)
- [我们用 Flutter 写了一套全新的 Flutter 开发者工具](