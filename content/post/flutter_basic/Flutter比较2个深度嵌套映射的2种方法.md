---
title: "Flutter比较2个深度嵌套映射的2种方法"
date: 2021-09-07T10:44:09+08:00
draft: true
---

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/image/Flutter.jpeg)

如果 2 个映射具有相同的长度，并且包含与相同值关联的相同键，则认为它们相等。本文向您展示了在 Flutter 中正确比较 2 个深度嵌套映射的 2 种方法。闲话少说，让我们检查一下代码。



## 使用 equals() 方法（DeepCollectionEquality 类）

[DeepCollectionEquality](https://api.flutter.dev/flutter/package-collection_collection/DeepCollectionEquality-class.html)类的[equals](https://api.flutter.dev/flutter/package-collection_collection/DeepCollectionEquality/equals.html)方法可以帮助我们用几行代码比较 2 个嵌套映射。要使用它，您需要导入**集合**库：

```dart
import 'package:collection/collection.dart';
```

### **例子**

假设我们有 3 个Map：项目 1、项目 2 和项目 3。我们的工作是将第一个项目与第二个和第三个项目进行比较。

```dart
// main.dart
import 'package:collection/collection.dart';

Map<String, dynamic> item1 = {
  'name': 'T-shirt',
  'color': 'blue',
  'price': 10.99,
  'details': {
    'department': 'men',
    'manufactuer': 'KindaCode.com',
    'style': 'classic',
    'size': ['S', 'M', 'L', 'XL', '2XL', '3XL'],
    'material': 'cotton',
    'ship': ['Standart', 'FedEx Air']
  }
};

Map<String, dynamic> item2 = {
  'name': 'T-shirt',
  'color': 'blue',
  'price': 10.99,
  'details': {
    'department': 'men',
    'manufactuer': 'KindaCode.com',
    'style': 'classic',
    'size': ['S', 'M', 'L', 'XL', '2XL', '3XL'],
    'material': 'cotton',
    'ship': ['Standart', 'FedEx Air']
  }
};

Map<String, dynamic> item3 = {
  'name': 'T-shirt',
  'color': 'blue',
  'price': 10.99,
  'details': {
    'department': 'mens',
    'manufactuer': 'KindaCode.com',
    'style': 'classic',
    'size': ['S', 'M', 'L', 'XL'],
    'material': 'cotton',
    'ship': ['Standart', 'FedEx Air']
  }
};

void main() {
  // Comparing item #1 vs item #2
  if (DeepCollectionEquality().equals(item1, item2)) {
    print('Item #1 and item #2 are equal');
  } else {
    print('Item #1 and item #2 are not equal');
  }

  // Comparing item #1 vs item #3
  if (DeepCollectionEquality().equals(item1, item3)) {
    print('Item #1 and item #3 are equal');
  } else {
    print('Item #1 and item #3 are not equal');
  }
}
```

输出：

```dart
Item #1 and item #2 are equal
Item #1 and item #3 are not equal
```

如果你仔细看，物品 3 不适合 2XL 尺寸。这就是为什么它与其他人不同的原因。

## 将Map转换为 JSON

这里的策略是将每个映射转换为 JSON 字符串，然后比较返回的字符串。

### **例子**

我们将使用与第一个示例相同的Map。

```dart
// main.dart
import 'dart:convert';

Map<String, dynamic> item1 = {
  'name': 'T-shirt',
  'color': 'blue',
  'price': 10.99,
  'details': {
    'department': 'men',
    'manufactuer': 'KindaCode.com',
    'style': 'classic',
    'size': ['S', 'M', 'L', 'XL', '2XL', '3XL'],
    'material': 'cotton',
    'ship': ['Standart', 'FedEx Air']
  }
};

Map<String, dynamic> item2 = {
  'name': 'T-shirt',
  'color': 'blue',
  'price': 10.99,
  'details': {
    'department': 'men',
    'manufactuer': 'KindaCode.com',
    'style': 'classic',
    'size': ['S', 'M', 'L', 'XL', '2XL', '3XL'],
    'material': 'cotton',
    'ship': ['Standart', 'FedEx Air']
  }
};

Map<String, dynamic> item3 = {
  'name': 'T-shirt',
  'color': 'blue',
  'price': 10.99,
  'details': {
    'department': 'mens',
    'manufactuer': 'KindaCode.com',
    'style': 'classic',
    'size': ['S', 'M', 'L', 'XL'],
    'material': 'cotton',
    'ship': ['Standart', 'FedEx Air']
  }
};

void main() {
  // Converting maps to strings
  String s1 = json.encode(item1);
  String s2 = json.encode(item2);
  String s3 = json.encode(item3);

  // Comparing item #1 vs item #2
  if (s1 == s2) {
    print('Item #1 and item #2 are equal');
  } else {
    print('Item #1 and item #2 are not equal');
  }

  // Comparing item #1 vs item #3
  if (s1 == s3) {
    print('Item #1 and item #3 are equal');
  } else {
    print('Item #1 and item #3 are not equal');
  }
}
```

输出：

```dart
Item #1 and item #2 are equal
Item #1 and item #3 are not equal
```

## 下一步是什么？

广告

我们已经使用了一些技术来深入比较 Flutter 中的两个不同Map。这些东西可能对你在未来使用海量数据应用程序的某些情况下非常有用。
