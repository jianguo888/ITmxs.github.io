---
title: "Dart检查map是否为空"
date: 2021-09-07T12:46:35+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter","dart"]
categories: ["Flutter","dart"]
---

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/image/Dart-220x120.png)

在 Dart 中，您可以通过多种方式检查给定的map是否为空。最方便的方法是使用**isEmpty**、**isNotEmpty**或**length**属性。

**例子：**

```
// main.dart
Map<String, dynamic> map1 = {'website': 'Kindacode.com', 'age': 5};
Map map2 = {};
Map map3 = {'key1': 'Value 1', 'key2': 'Value 2'};

void main() {
  // Using isEmpty
  if (map1.isEmpty) {
    print('Map1 is empty');
  } else {
    print('Map1 is not empty');
  }

  // Using isNotEmpty
  if (map2.isNotEmpty) {
    print('Map2 is not empty');
  } else {
    print('Map2 is empty');
  }

  // Clear map3's entries
  map3.clear();
  // Using length property
  if (map3.length == 0) {
    print('Map3 is empty');
  } else {
    print('Map 3 is not empty');
  }
}
```

输出：

```
Map1 is not empty
Map2 is empty
Map3 is empty
```
