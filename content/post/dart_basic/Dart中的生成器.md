---
title: "Dart中的生成器"
date: 2021-09-01T15:44:59+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter","dart"]
categories: ["Flutter","dart"]
---

Dart 中的生成器允许用户轻松生成值序列。可以借助两个生成器函数在 dart 中生成一系列值：

1. **同步**生成器：返回一个可[**迭代**](https://api.dart.dev/stable/dart-core/Iterable-class.html)对象。
2. **异步**生成器：返回一个[**Stream**](https://api.dart.dev/stable/dart-async/Stream-class.html)对象。

### Dart 中的同步生成器

同步生成器返回一个可迭代对象，即它返回可以顺序访问的值或“元素”的集合。要实现**同步**生成器功能，请将函数体标记为**sync\***，并使用**yield 语句**传递值。

**示例：**在 Dart 中实现同步生成器。

```dart
// sync* functions return an iterable
Iterable geeksForGeeks(int number) sync* {
int geek = number;
while (geek >= 0) {
	
	// Checking for even number
	if (geek % 2 == 0) {
		
	// 'yield' suspends
	// the function
	yield geek;
		
	}
	
	// Decreasing the
	// variable geek
	geek--;
}
}

// Main Function
void main()
{
print("------- Geeks For Geeks --------");
	
print("Dart Synchronous Generator Example For Printing Even Numbers From 10 In
		Reverse Order:");
	
// Printing positive even numbers
// from 10 in reverse order
geeksForGeeks(10).forEach(print);
}

```

### Dart 中的异步生成器

异步生成器返回一个流对象。Stream 提供了一种接收一系列事件的方法。每个事件要么是一个数据事件，也称为流的一个***元素***，要么是一个错误事件，它是某事失败的通知。要实现**异步**生成器函数，请将函数体标记为**async\***，并使用**yield 语句**传递值。

**示例：**在 Dart 中实现异步生成器。

```dart
// async* function(s) return an stream
Stream geeksForGeeks(int number) async* {
int geek = 0;
	
// Checking for every
// geek less than number
while (geek <= number) yield geek++;
// Incrementing geek
// after printing it
}

// Main Function
void main()
{
print("-------- Geeks For Geeks -----------");
	
print("Dart Asynchronous Generator Example For Printing Numbers Less Than 10:");
	
// Printing numbers less
// than or equal to 10
geeksForGeeks(10).forEach(print);
}

```



```dart
-------- Geeks For Geeks -----------
Dart Asynchronous Generator Example For Printing Numbers Less Than 10:
0
1
2
3
4
5
6
7
8
9
10
```

