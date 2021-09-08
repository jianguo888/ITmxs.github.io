---
title: "如何在Flutter中编码解码JSON"
date: 2021-09-07T10:38:38+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter","dart"]
categories: ["Flutter","dart"]
---

本文将向您展示如何在 Flutter 中编码/解码 JSON。

1. 导入**dart:convert**库：

```dart
import 'dart:convert';
```

2. 使用：

- **json.encode()**或**jsonEncode()**用于编码
- **json.decode()**或**jsonDecode()**用于解码

## **例子**

**示例 1：JSON 编码**

```dart
final products = [
    {
      'id': 1,
      'name': 'Product #1'
    },
    {
      'id': 2,
      'name': 'Product #2'
    }
];
  
print(json.encode(products)); 
```

输出：

```dart
[{"id":1,"name":"Product #1"},{"id":2,"name":"Product #2"}]
```

**示例 2：JSON 解码**

```dart
final String responseData = '[{"id":1,"name":"Product #1"},{"id":2,"name":"Product #2"}]';
  
final products = json.decode(responseData);
  
// Print the name of the second product in the list
print(products[1]['name']);
```

输出：

```dart
Product #2
```

希望这对你有帮🐖
