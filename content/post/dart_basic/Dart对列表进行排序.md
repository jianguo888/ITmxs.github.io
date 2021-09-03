---
title: "Dart对列表进行排序"
date: 2021-09-01T16:16:03+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter","dart"]
categories: ["Flutter","dart"]
---

在 Dart 编程中，List 数据类型类似于其他编程语言中的数组。列表用于表示对象的集合。它是一组有序的对象。Dart 中的核心库负责 List 类的存在、创建和操作。列表的排序取决于我们正在排序的列表类型，即如果我们正在对整数列表进行排序，那么我们可以使用简单的排序函数，而如果它是一个字符串列表，那么我们使用 compareTo 对列表进行排序。

### 对整数列表进行排序

整数列表可以通过简单的排序函数进行排序。

**示例：**对整数列表进行排序。

```dart
// Main function
main() {

// Creating List
List<int> geeksforgeeks = [13, 2, -11, 142, -389, 32, 3032, 0];

// Sorting List
geeksforgeeks.sort();

// Printing Sorted List
print(geeksforgeeks);
}

```

### 对字符串列表进行排序

通过比较 sort 函数中的长度对字符串进行排序。

**示例：**对字符串列表进行排序。

```dart
// Main function
main() {

// Creating list of string
List<String> geeksforgeeks = ['one', 'two', 'three', 'four'];

// Sorting string by comparing the length
geeksforgeeks.sort((a, b) => a.length.compareTo(b.length));

// Printing the list
print(geeksforgeeks);
}

```

如果我们在不比较长度的情况下使用排序，则：

**示例：**在不比较长度的情况下对字符串列表进行排序。

```dart
// Main function
main() {

// Creating list of string
List<String> geeksforgeeks = ['one', 'two', 'three', 'four'];

// Sorting string without
// comparing the length
geeksforgeeks.sort();

// Printing the list
print(geeksforgeeks);
}

```

**示例：**在对列表进行排序时使用级联方法。

```dart
// Main function
main() {

// Creating list of string
List<int> geeksforgeeks = [13, 2, -11, 142, -389, 0];

// Sorting string and Printing the list
print(geeksforgeeks..sort());
}

```

1
