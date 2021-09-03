---
title: "Dart–Common_Collection_Methods"
date: 2021-09-01T17:02:00+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter","dart"]
categories: ["Flutter","dart"]
---

List、Set 和 Map 共享许多集合中的通用功能。其中一些常见功能是由 Iterable 类定义的，List 和 Set 实现了该类。

### 1. isEmpty() 或 isNotEmpty：

使用 isEmpty 或 isNotEmpty 检查列表、集合或 Map是否有项目：

**例子：**

```dart
void main(){
	
var coffees = [];
var teas = ['green', 'black', 'chamomile', 'earl grey'];
print(coffees.isEmpty);
print(teas.isNotEmpty);
}

```

### 2. forEach()：

要将函数应用于列表、集合或映射中的每个项目，您可以使用 forEach()：

**例子：**



```dart
void main(){
	
	
var teas = ['green', 'black', 'chamomile', 'earl grey'];

var loudTeas = teas.map((tea) => tea.toUpperCase());
loudTeas.forEach(print);
}

```

### 3.where()：

使用 Iterable 的 where() 方法获取匹配条件的所有项目。使用 Iterable 的 any() 和 every() 方法来检查部分或所有项目是否与条件匹配。

```dart
void main(){
	
var teas = ['green', 'black', 'chamomile', 'earl grey'];

// Chamomile is not caffeinated.
bool isDecaffeinated(String teaName) =>
	teaName == 'chamomile';

// Use where() to find only the items that return true
// from the provided function.


// Use any() to check whether at least one item in the
// collection satisfies a condition.
print(teas.any(isDecaffeinated));

// Use every() to check whether all the items in a
// collection satisfy a condition.
print(!teas.every(isDecaffeinated));
}

```

