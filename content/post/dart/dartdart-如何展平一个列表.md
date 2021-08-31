---
title: "Dart如何展平一个列表"
date: 2021-08-31T11:29:38+08:00
draft: true
---

****



如何轻松地展平 `List`在 Dart ？

例如:



```dart
var a = [[1, 2, 3], ['a', 'b', 'c'], [true, false, true]];
var b = [1, 2, 3, 'a', 'b', 'c', true, false, true];
```


怎么转 `a`进入 `b` ，即变成单个 `List`包含所有这些值？



**最佳答案**

我所知道的最简单的方法是使用 `Iterable.expand()`带有身份函数。 `expand()`获取 Iterable 的每个元素，对其执行一个函数，返回一个可迭代对象(“扩展”部分)，然后连接结果。在其他语言中，它可能被称为 flatMap。

因此，通过使用标识函数， expand 只会连接项目。如果你真的想要一个列表，那么使用 `toList()` .



```dart
var a = [[1, 2, 3], ['a', 'b', 'c'], [true, false, true]];
var flat = a.expand((i) => i).toList();
```
