---
title: "DartFlutter获取列表中特定元素的索引"
date: 2021-09-07T11:09:36+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter","dart"]
categories: ["Flutter","dart"]
---

# Dart & Flutter：获取列表中特定元素的索引

在 Dart 中，List 类有 4 个方法可以帮助您找到列表中特定元素的索引：

- **indexOf**：返回列表中与给定元素相等的第一个元素的第一个索引。如果没有找到，则返回 -1。
- **indexWhere**：返回列表中满足给定条件的第一个索引。如果没有找到，则返回 -1。
- **lastIndexOf**：返回列表中与给定元素相等的最后一个元素的索引。如果没有找到，则返回 -1。
- **lastIndexWhere**：返回列表中满足给定条件的最后一个索引。如果没有找到，则返回 -1。

下面的例子将帮助你更好地理解它们。

## 示例 1：在地图列表中查找特定地图的索引

假设我们有一个包含 id、姓名和年龄等信息的人员列表。第一个任务是找到 id 等于给定 id 的人的索引(因为 id 是唯一的，我们最多可以有 1 个结果)。第二个任务是找到最后一个年龄大于 80 的人的索引。

编码：

```dart
// main.dart
void main() {
  final List<Map<String, dynamic>> _people = [
    {"id": "c1", "name": "John Doe", "age": 40},
    {"id": "c2", "name": "Kindacode.com", "age": 3},
    {"id": "c3", "name": "Pipi", "age": 1},
    {"id": "c4", "name": "Jane Doe", "age": 99},
  ];

  // Find index of the person whose id = c3
  final index1 = _people.indexWhere((element) => element["id"] == "c3");
  if (index1 != -1) {
    print("Index $index1: ${_people[index1]}");
  }

  // Find the last index where age > 80
  final index2 = _people.lastIndexWhere((element) => element["age"] > 80);
  if (index2 != -1) {
    print("Index $index2: ${_people[index2]}");
  }
}
```

输出：

```dart
Index 2: {id: c3, name: Pipi, age: 1}
Index 3: {id: c4, name: Jane Doe, age: 99}
```

## 示例 2：在对象列表中查找特定对象的索引

假设我们有一个给定书籍的列表。第一项工作是找到 id 等于给定 id 的书的索引。第二项工作是在列表中找到标题包含给定字符串的第一本书的索引。

编码：

```dart
// main.dart

class Book {
  String id;
  String title;
  double price;

  Book(this.id, this.title, this.price);
}

void main() {
  final List<Book> _books = [
    Book('b1', 'A Blue Novel', 0.99),
    Book('b2', 'A Green Novel', 1.99),
    Book('b3', 'A Cooking Hanbook', 2.99),
    Book('b4', 'Kindacode.com', 0.00)
  ];

  // Find the index of the book whose id is 'b4'
  final int index1 = _books.indexWhere(((book) => book.id == 'b4'));
  if (index1 != -1) {
    print('Index: $index1');
    print('Title: ${_books[index1].title}');
  }

  // Find the index of the fist book whose title contains 'Novel'
  final int index2 = _books.indexWhere((book) => book.title.contains('Novel'));
  if (index2 != -1) {
    print('Index: $index2');
    print('Title: ${_books[index2].title}');
  }
}
```

输出：

```dart
Index: 3
Title: Kindacode.com
Index: 0
Title: A Blue Novel
```

## 总结

我们已经浏览了一些在列表中查找特定元素的索引的示例。
