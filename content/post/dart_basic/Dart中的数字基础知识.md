---
title: "Dart中的数字基础知识"
date: 2021-09-01T15:36:50+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter","dart"]
categories: ["Flutter","dart"]
---

其他语言一样，[**Dart Programming**](https://www.geeksforgeeks.org/html-document-object-model-and-dart-programming/)也支持数值作为 Number 对象。Dart Programming 中的数字是用于保存数值的数据类型。飞镖数可分为： 
 

**int：** int 数据类型用于表示整数。

> **语法：** int var_name;

**double：** double 数据类型用于表示 64 位浮点数。

**语法：** double var_name;

- dart

```dart
void main() {
	
// declare an integer
int num1 = 2;			
	
// declare a double value
double num2 = 1.5;

// print the values
print(num1);
print(num2);
}

```

**输出：** 

```dart
2
1.5
```

**注意：**该**NUM**类型是int和double类型的继承的数据类型。

**在 Dart 中** 解析**：** parse() 函数用于解析包含数字文字的字符串并将其转换为数字。 
 

**示例 2：** 
 

- dart

```dart
void main()
{
	
var a1 = num.parse("1");
var b1 = num.parse("2.34");

var c1 = a1+b1;
print("Product = ${c1}");
}

```

**输出：** 

```dart
Product = 3.34
```

**特性：** 

- **hashcode：**该属性用于获取给定数字的哈希码。
- **isFinite：**如果给定的数字是有限的，则此属性将返回 true。
- **isInfinite：**如果数字是无限的，那么这个属性将返回真。
- **isNan：**如果数字为非负数，则此属性将返回 true。
- **isNegative：**如果数字为负，则此属性将返回 true。
- **符号：**此属性用于根据给定数字的符号获得 -1、0 或 1。
- **isEven：**如果给定的数字是偶数，则此属性将返回 true。
- **isOdd：**如果给定的数字是奇数，则此属性将返回 true。

**方法：** 

- **abs()：**此方法给出给定数字的绝对值。
- **ceil()：**此方法给出给定数字的上限值。
- **floor()：**此方法给出给定数字的下限值。
- **compareTo()：**此方法将值与其他数字进行比较。
- **余数()：**此方法给出两个数相除后的截断余数。
- **round()：**此方法返回数字的轮次。
- **toDouble()：**此方法给出数字的双重等效表示。
- **toInt()：**此方法返回数字的整数等效表示。
- **toString()：**此方法返回数字的 String 等效表示
- **truncate()：**此方法在丢弃小数位后返回整数。
