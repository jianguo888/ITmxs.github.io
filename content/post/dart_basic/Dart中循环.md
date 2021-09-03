---
title: "Dart中循环"
date: 2021-09-01T15:56:17+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter","dart"]
categories: ["Flutter","dart"]
---

Dart 或任何其他编程语言中的循环语句用于重复某组命令，直到某些条件未完成。有不同的方法可以做到这一点。他们是： 
 

- for循环
- for... 循环
- 对于每个循环
- while 循环
- do-while 循环

##  for循环

Dart 中的 for 循环与 Java 中的类似，执行流程也与 Java 中相同。
**句法：** 

```dart
 for（初始化；条件；测试表达式）{
    // 循环体
}
```

### **控制流：** 

控制流程如下： 

1. 初始化
2. 健康）状况
3. 循环体
4. 测试表达式

第一个只执行一次，即在开始时执行，而其他三个执行直到条件为假。
**例子：** 

```dart
// Printing GeeksForGeeks 5 times
void main()
{
	for (int i = 0; i < 5; i++) {
		print('GeeksForGeeks');
	}
}

```

## for...in 循环

Dart 中的 For...in 循环将表达式或对象作为迭代器。它与Java 中的类似，其执行流程也与Java 中的相同。 

```dart
 for（表达式中的变量）{
   // 循环体
}

```

**例子：** 

```dart
void main()
{
	var GeeksForGeeks = [ 1, 2, 3, 4, 5 ];
	for (int i in GeeksForGeeks) {
		print(i);
	}
}

```

## for each … loop

for-each 循环遍历某个容器/集合中的所有元素，并将这些元素传递给某个特定的函数。

```dart
 collection.foreach(void f(value))
```

**例子：** 

```dart
void main() {
var GeeksForGeeks = [1,2,3,4,5];
GeeksForGeeks.forEach((var num)=> print(num));
}

```

## while 循环

循环体将一直运行，直到且除非条件为真。

```dart
 while(condition){
    text expression;
    // Body of loop
}
```

**例子：** 
 

```dart
void main()
{
	var GeeksForGeeks = 4;
	int i = 1;
	while (i <= GeeksForGeeks) {
		print('Hello Geek');
		i++;
	}
}


```

## do..while 循环

将首先执行循环体，然后测试条件。

```dart
 do{
    text expression;
    // Body of loop
}while(condition);
```

**例子：** 

```dart
void main()
{
	var GeeksForGeeks = 4;
	int i = 1;
	do {
		print('Hello Geek');
		i++;
	} while (i <= GeeksForGeeks);
}

```

