---
title: "Dart如何在扩展方法中反转列表"
date: 2021-08-31T12:54:23+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["flutter","dart"]
categories: ["flutter","dart"]
---

从[here](https://dart.dev/guides/language/extension-methods#implementing-generic-extensions)

```
extension MyFancyList<T> on List<T> {
  List<T> operator -() => reversed.toList();
}
```

但是，如何使用运算符`-`反转列表？

```
void main() {
  var numbers = [1, 2, 3];
  print(numbers-); // Error
}
```



**最佳答案**

你可以这样使用

```
extension MyFancyList<T> on List<T> {
  int get doubleLength => length * 2;
  List<T> operator -() => reversed.toList();
}

void main() {
  print([1,2,3].doubleLength); /* 6 */
  print(-[1,2,3]); /* [3, 2, 1] */
}
```

还是你的情况

```
void main() {
  var numbers = [1, 2, 3];
  print(-numbers); /* [3, 2, 1] */
}
```

最后一个来自文档

```
extension MyFancyList<T> on List<T> {
  List<List<T>> split(int at) => <List<T>>[sublist(0, at), sublist(at)];
}

void main() {
  List<int> numbers = [1, 2, 3, 4, 5];
  print(numbers.split(2)); /* [[1, 2], [3, 4, 5]] */
}
```
