---
title: "在Dart和Flutter中使用级联表示法"
date: 2021-09-07T11:00:45+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter","dart"]
categories: ["Flutter","dart"]
---

这篇短文将带您了解一些在 Dart 中使用级联表示法 (.., ?...) 的示例。



## 重点是什么？

在 Dart 中，级联让你可以对同一个对象执行一系列操作。换句话说，级联允许您在不需要临时变量的情况下执行一系列方法。一般来说，它们看起来像这样：

```dart
myObject..method1()..method2()..method3();
myList?..method1()..method2()..method3()..method4();
```

为了更清楚，请参阅下面的示例。

## 示例 1：级联和列表

下面的两个代码片段做同样的事情。

1. 使用级联：

```dart
void main() {
  List myList = [1, 2, 3];
  myList
    ..clear()
    ..add(4)
    ..add(5)
    ..add(6);

  print(myList);
}

// Output: [4, 5, 6]
```

2. 不使用级联：

```
void main() {
  List myList = [1, 2, 3];
  myList.clear();
  myList.add(4);
  myList.add(5);
  myList.add(6);
  print(myList);
}

// Output: [4, 5, 6]
```

## 示例 2：级联和类

编码：

```dart
class Person {
  String? name;
  int? age;

  void setName(String name) {
    this.name = name;
  }

  void setAge(int age) {
    this.age = age;
  }

  void printInfo() {
    print("User name: ${this.name ?? 'Unknown'}");
    print("User age: ${this.age ?? 'Not provided'}");
  }
}

void main() {
  new Person()
    ..setName('Kindacode.com')
    ..setAge(4)
    ..printInfo();

  final instance = new Person();
  instance
    ..setName('John Doe')
    ..setAge(99)
    ..printInfo();
}
```

输出：

```dart
// Person 1
User name: Kindacode.com
User age: 4

// Person 2
User name: John Doe
User age: 99
```

## 结论

我们已经通过几个在 Dart 中使用级联的例子。此时，您应该对使用此语法进行阅读和编写简洁紧凑的代码有更好的理解并获得舒适的感觉。
