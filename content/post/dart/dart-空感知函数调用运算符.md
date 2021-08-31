---
title: "Dart 空感知函数调用运算符"
date: 2021-08-31T09:04:24+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["flutter","dart"]
categories: ["flutter","dart"]
---

以同样的方式我们可以有



```dart
nullableClassInstance?.method(blah)
```


有没有办法



```dart
nullableFunctionInstance?(blah)
```


换句话说，是否有一个运算符来检查函数实例是否不是 `null` ，如果是这样，请在一行中调用该函数？



**最佳答案**

使用 [`call` method](https://api.dartlang.org/stable/2.5.2/dart-core/Function-class.html) ，您可以通过以下方式实现您想要的:



```dart
nullableFunctionInstance?.call(blah)
```


还有 [`apply` method](https://api.dartlang.org/stable/2.5.2/dart-core/Function/apply.html)如果你想传递参数。
