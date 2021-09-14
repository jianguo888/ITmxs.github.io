---
title: "Dart查找满足条件的列表元素"
date: 2021-09-07T11:12:23+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter","dart"]
categories: ["Flutter","dart"]
---

使用 Flutter 和 Dart 开发应用程序时，您将大量使用列表。使用列表时最常见的任务之一是查找满足单个或多个条件的元素。本文将向您介绍如何完成该任务的几个示例。我们将使用**Iterable**类中的**where()**方法。此方法将返回一个新的 Iterable 而不更改原始列表。

目录



[从数字/字符串列表中搜索](https://www.kindacode.com/article/dart-find-list-elements-that-satisfy-conditions/#Search_from_a_List_of_NumbersStrings)[从对象列表中搜索](https://www.kindacode.com/article/dart-find-list-elements-that-satisfy-conditions/#Search_from_a_List_of_Objects)[从地图列表中搜索](https://www.kindacode.com/article/dart-find-list-elements-that-satisfy-conditions/#Search_from_a_List_of_Maps)[结论](https://www.kindacode.com/article/dart-find-list-elements-that-satisfy-conditions/#Conclusion)

## 从数字/字符串列表中搜索

**例**1.1

下面的程序将从给定的列表中查找所有大于 3 且小于 9 的数字。

```
void main() {
  final List<int> numbers = [1, 2, 10, 9, 11, 4, 5, 7, 2];
  final List<int> results =
      numbers.where((element) => element < 9 && element > 3).toList();
  print(results);
  runApp(MyApp());
}
```

输出：

```
[4, 5, 7]
```

**例 1.2**

该程序将返回以大写“B”开头的名称列表。

```
void main() {
  final List<String> names = [
    'John Doe',
    'Bobby Right',
    'Voldermort',
    'Pennywise The Clown',
    'Bill Clinton'
  ];

  final List<String> namesStartWithB =
      names.where((element) => element.startsWith('B')).toList();
  print(namesStartWithB);
}
```

输出：

```
[Bobby Right, Bill Clinton]
```

## 从对象列表中搜索

此示例查找所有价格低于 100 且免运费的产品。

```
import 'package:flutter/foundation.dart';

/// Define how a product looks like
class Product {
  String name;
  double price;
  bool isFreeShip;

  Product(
      {@required this.name, @required this.price, @required this.isFreeShip});
}

void main() {
  // The list of all products
  final List<Product> _allProducts = [
    Product(name: 'Book', price: 9, isFreeShip: false),
    Product(name: 'Cooking Pan', price: 81.29, isFreeShip: true),
    Product(name: 'DVD', price: 6.99, isFreeShip: false),
    Product(name: 'Laptop', price: 500, isFreeShip: true),
    Product(name: 'Shoes', price: 48.99, isFreeShip: true)
  ];

  final _results = _allProducts
      .where((product) => product.price < 100 && product.isFreeShip == true);

  for (Product p in _results) {
    print(p.name);
  }
}
```

  

输出：

```
Cooking Pan
Shoes
```

## 从地图列表中搜索

这个例子会找到所有有 Flutter 经验的开发者：

```
// main.dart

const List<Map<String, dynamic>> developers = [
  {
    "name": "Dev A",
    "skills": ["Flutter", "Node.js", "Swift UI", "Java"]
  },
  {
    "name": "Dev B",
    "skills": ["Flutter", "SQL", "Kotin", "Python"]
  },
  {
    "name": "Dev C",
    "skills": ["Xamarin", "React Native", "SQL"]
  }
];

void main() {
  final List<Map<String, dynamic>> d = developers
      .where((developer) => developer["skills"].contains("Flutter"))
      .toList();

  print(d);
}
```

输出：

```
[
  {name: Dev A, skills: [Flutter, Node.js, Swift UI, Java]}, 
  {name: Dev B, skills: [Flutter, SQL, Kotin, Python]}
]
```

## 结论

您已经学习了如何从列表中获取与一个或多个给定条件匹配的所有元素
