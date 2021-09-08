---
title: "从Dart列表中删除重复项的2种方法"
date: 2021-09-06T16:41:28+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter","dart"]
categories: ["Flutter","dart"]
---

本文向您展示了从 Flutter 中的列表中删除重复项的 2 种方法。第一个适用于**原始数据类型列表**。第二个稍微复杂一些，但适用**于地图****列表**或**对象列表**。

## 转换为 Set 然后反转为 List

这是一个简单列表的简单快速的解决方案。

例子：

```dart
void main(){
  final myNumbers = [1, 2, 3, 3, 4, 5, 1, 1];
  final uniqueNumbers = myNumbers.toSet().toList();
  print(uniqueNumbers);
  
  final myStrings = ['a', 'b', 'c', 'a', 'b', 'a'];
  final uniqueStrings = myStrings.toSet().toList();
  print(uniqueStrings); 
}
```

输出：

```dart
[1, 2, 3, 4, 5]
[a, b, c]
```

## 从map或对象列表中删除重复项

我们的策略是将列表的每个项目转换为 JSON 字符串，然后像第一种方法一样使用**toSet()**和**toList()**。

例子：

```dart
import "dart:convert";
void main(){
  final myList = [
    {
      'name': 'Andy',
      'age': 41
    },
    {
      'name': 'Bill',
      'age': 43
    },
    {
      'name': 'Andy',
      'age': 41
    }
  ];
  
  // convert each item to a string by using JSON encoding
  final jsonList = myList.map((item) => jsonEncode(item)).toList();
  
  // using toSet - toList strategy
  final uniqueJsonList = jsonList.toSet().toList();
  
  // convert each item back to the original form using JSON decoding
  final result = uniqueJsonList.map((item) => jsonDecode(item)).toList();
  
  print(result); 
}
```

输出：

```dart
[{name: Andy, age: 41}, {name: Bill, age: 43}]
```

