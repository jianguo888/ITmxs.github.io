---
title: "Dart找到列表中的最大值与最小值"
date: 2021-09-01T16:09:31+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["flutter","dart"]
categories: ["flutter","dart"]
---

在 Dart 中，我们可以通过七种方式找到给定列表中存在的最小值和最大值元素：

1. 使用**for 循环**查找最大和最小元素。
2. 使用**sort 函数**查找最大和最小元素。
3. 使用**forEach 循环**查找最大和最小元素。
4. 在 dart 中仅使用**reduce 方法**来查找最大和最小元素。
5. 在**dart:math**库中使用**reduce 方法**。
6. 使用带有 dart 的**fold 方法**来查找最大和最小元素。
7. 在**dart:math**库中使用**fold 方法**。

### 使用**for 循环**

这是查找列表中最大和最小元素的最基本方法，只需遍历所有元素并进行比较并给出答案。

**例子：**

```dart
// Main function
void main() {
	
// Creating a geek list
var geekList = [121, 12, 33, 14, 3];
	
// Declaring and assigning the
// largestGeekValue and smallestGeekValue
var largestGeekValue = geekList[0];
var smallestGeekValue = geekList[0];

for (var i = 0; i < geekList.length; i++) {
	
	// Checking for largest value in the list
	if (geekList[i] > largestGeekValue) {
	largestGeekValue = geekList[i];
	}
	
	// Checking for smallest value in the list
	if (geekList[i] < smallestGeekValue) {
	smallestGeekValue = geekList[i];
	}
}

// Printing the values
print("Smallest value in the list : $smallestGeekValue");
print("Largest value in the list : $largestGeekValue");
}

```

### 使用**排序功能**

Dart 还允许用户按升序对列表进行排序，即第一个最小，最后一个最大。

**例子：***

```dart
// Main function
void main() {
// Creating a geek list
var geekList = [121, 12, 33, 14, 3];
	
// Sorting the list
geekList.sort();

// Printing the values
print("Smallest value in the list : ${geekList.first}");
print("Largest value in the list : ${geekList.last}");
}

```

## 使用**forEach 循环**

与 for 循环不同，还可以使用 forEach 循环来获取列表元素，然后检查列表元素中的条件。

```dart
// Main function
void main() {
	
// Creating a geek list
var geekList = [121, 12, 33, 14, 3];
	
// Declaring and assigning the
// largestGeekValue and smallestGeekValue
var largestGeekValue = geekList[0];
var smallestGeekValue = geekList[0];
	
// Using forEach loop to find
// the largest and smallest
// numbers in the list
geekList.forEach((gfg) => {
		if (gfg > largestGeekValue) {largestGeekValue = gfg},
		if (gfg < smallestGeekValue) {smallestGeekValue = gfg},
	});

// Printing the values
print("Smallest value in the list : $smallestGeekValue");
print("Largest value in the list : $largestGeekValue");
}

```

## 使用**reduce方法**

可以使用 Dart *reduce*函数将列表减少到特定值并保存。

```dart
// Main function
void main() {
	
// Creating a geek list
var geekList = [121, 12, 33, 14, 3];
	
// Declaring and assigning the
// largestGeekValue and smallestGeekValue
// Finding the smallest and largest
// value in the list
var smallestGeekValue = geekList.reduce(
(current, next) => current < next ? current : next);
var largestGeekValue = geekList.reduce(
(current, next) => current > next ? current : next);

// Printing the values
print("Smallest value in the list : $smallestGeekValue");
print("Largest value in the list : $largestGeekValue");
}

```

## 在**dart:math**库中使用**reduce方法**

```dart
import "dart:math";

// Main function
void main() {
// Creating a geek list
var geekList = [121, 12, 33, 14, 3];
	
// Declaring and assigning the
// largestGeekValue and smallestGeekValue
// Finding the smallest and largest value in the list
var smallestGeekValue = geekList.reduce(min);
var largestGeekValue = geekList.reduce(max);

// Printing the values
print("Smallest value in the list : $smallestGeekValue");
print("Largest value in the list : $largestGeekValue");
}

```

## 使用**fold 方法**

除了减少飞镖还有折叠，它与减少非常相似，只是从一个初始值开始，然后在这个过程中改变它。

**例子：**

```dart
// Main function
void main() {
	
// Creating a geek list
var geekList = [121, 12, 33, 14, 3];
	
// Declaring and assigning the
// largestGeekValue and smallestGeekValue
// Finding the smallest and
// largest value in the list
var smallestGeekValue = geekList.fold(geekList[0],
(previous, current) => previous < current ? previous : current);
var largestGeekValue = geekList.fold(geekList[0],
(previous, current) => previous > current ? previous : current);

// Printing the values
print("Smallest value in the list : $smallestGeekValue");
print("Largest value in the list : $largestGeekValue");
}

```

## 在**dart:math**库中使用**fold 方法**

```dart
import "dart:math";

// Main function
void main() {
// Creating a geek list
var geekList = [121, 12, 33, 14, 3];
	
// Declaring and assigning
// the largestGeekValue and smallestGeekValue
// Finding the smallest and
// largest value in the list
var smallestGeekValue = geekList.fold(geekList[0],min);
var largestGeekValue = geekList.fold(geekList[0],max);

// Printing the values
print("Smallest value in the list : $smallestGeekValue");
print("Largest value in the list : $largestGeekValue");
}

```

1

