---
title: "Dart如何从map中删除特定条目"
date: 2021-09-07T10:55:49+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter","dart"]
categories: ["Flutter","dart"]
---

在 Dart 中，您可以使用以下方法之一从给定映射中删除键/值对：

- **remove()**：删除与提供的键关联的条目。即使提供的密钥不存在，也不会发生错误。
- **removeWhere()**：删除满足给定条件的所有条目。

为了更清楚，请参阅下面的示例。



## **示例 1**：使用 remove()

此代码片段删除包含键的条目：**age**、**occupation**、**hometown**。

```dart
// main.dart
Map<String, dynamic> person = {
  'name': 'John Doe',
  'age': 39,
  'occupation': 'Thief',
  'hometown': 'Unknown',
  'isMarried': false
};

void main() {
  person.remove('age');
  person.remove('occupation');
  person.remove('hometown');
  print(person);
}
```

输出：

```dart
name: John Doe, isMarried: false}
```

## 示例 2：使用 removeWhere()

以下代码删除值为**null**或密钥为**secret 的**条目：

```dart
// main.dart
Map<String, dynamic> project = {
  'title': 'KindaCode.com: A Programming Website',
  'age': 5,
  'language': 'English',
  'technologies': 'Unknown',
  'traffic': null,
  'contact': null,
  'secret': 'Flying pan'
};

void main() {
  project.removeWhere((key, value) => value == null || key == 'secret');
  print(project);
}
```

输出：

```dart
{title: KindaCode.com: A Programming Website, age: 5, language: English, technologies: Unknown}
```
