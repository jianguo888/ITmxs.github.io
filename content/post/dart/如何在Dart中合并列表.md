---
title: "如何在Dart中合并列表"
date: 2021-09-01T15:28:07+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter","dart"]
categories: ["Flutter","dart"]
---

https://www.geeksforgeeks.org/how-to-combine-lists-in-dart/?ref=leftbar-rightbar

在 Dart 编程中，List 数据类型类似于其他编程语言中的数组。列表用于表示对象的集合。它是一组有序的对象。Dart 中的核心库负责 List 类的存在、创建和操作。有 5 种方法可以组合两个或多个列表：

1. 使用 addAll() 方法将另一个列表的所有元素添加到现有列表中。
2. 通过使用列表的 addAll() 方法添加两个或更多列表来创建新列表。
3. 通过使用 list 的 expand() 方法添加两个或多个列表来创建新列表**。**
4. 使用 + 运算符组合列表。
5. 使用扩展运算符组合列表。

### 使用 addAll() 方法将其他列表的所有元素添加到现有列表中

我们可以使用 addAll() 方法将另一个列表的所有元素添加到现有列表中。要了解此方法，您可以参考这篇[文章](https://www.geeksforgeeks.org/dart-programming-list/)。

**例子：**


- dart

```dart
// Main function
main() {
	
// Creating lists
List gfg1 = ['Welcome','to'];
List gfg2 = ['GeeksForGeeks'];
	
// Combining lists
gfg1.addAll(gfg2);
	
// Printing combined list
print(gfg1);
}

```


**输出：** 





```dart
[Welcome, to, GeeksForGeeks]
```

### 通过使用列表的 addAll() 方法添加两个或更多列表来创建新列表

我们可以通过使用 Dart 中的 addAll() 方法将列表中的所有元素一个接一个地添加到新列表中。要了解此方法，您可以参考这篇[文章](https://www.geeksforgeeks.org/dart-programming-list/)。

**例子：**


- dart

```dart
// Main function
main() {
	
// Creating lists
List gfg1 = ['Welcome','to'];
List gfg2 = ['GeeksForGeeks'];
	
// Combining lists
var newgfgList = new List.from(gfg1)..addAll(gfg2);
	
// Printing combined list
print(newgfgList);
}

```


**输出：** 

```dart
[Welcome, to, GeeksForGeeks]
```

### 通过使用列表的 expand() 方法添加两个或多个列表来创建新列表

我们可以通过使用 Dart 中的 expand() 方法将列表中的所有元素一个接一个地添加到新列表中。这通常用于将两个以上的列表添加在一起。

**例子：**


- dart

```dart
// Main function
main() {
	
// Creating lists
List gfg1 = ['Welcome'];
List gfg2 = ['to'];
List gfg3 = ['GeeksForGeeks'];
	
// Combining lists
var newgfgList = [gfg1, gfg2, gfg3].expand((x) => x).toList();
	
// Printing combined list
print(newgfgList);
}

```


**输出：** 





```dart
[Welcome, to, GeeksForGeeks]
```

### 使用 + 运算符组合列表

我们还可以通过在 Dart 中使用**+**运算符将列表添加到一起。这种方法是在**Dart 2.0 更新中**引入的。

**例子：**


- dart

```dart
// Main function
main() {
	
// Creating lists
List gfg1 = ['Welcome'];
List gfg2 = ['to'];
List gfg3 = ['GeeksForGeeks'];
	
// Combining lists
var newgfgList = gfg1 + gfg2 + gfg3;
	
// Printing combined list
print(newgfgList);
}

```


**输出：** 

```dart
[Welcome, to, GeeksForGeeks]
```

### 使用扩展运算符合并列表

从**Dart 2.3**更新开始，还可以使用扩展运算符来组合 Dart 中的列表。

**例子：**


- dart

```dart
// Main function
main() {
	
// Creating lists
List gfg1 = ['Welcome'];
List gfg2 = ['to'];
List gfg3 = ['GeeksForGeeks'];
	
// Combining lists
var newgfgList = [...gfg1, ...gfg2, ...gfg3];
	
// Printing combined list
print(newgfgList);
}

```

**输出：** 

```dart
[Welcome, to, GeeksForGeeks]
```

