---
title: "Dart编程List"
date: 2021-09-01T16:22:00+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["flutter","dart"]
categories: ["flutter","dart"]
---

在 Dart 编程中，List 数据类型类似于其他编程语言中的数组。列表用于表示对象的集合。它是一组有序的对象。Dart 中的**核心**库负责 List 类的存在、创建和操作。
**列表的逻辑表示：** 
 

![列表飞镖编程](https://luckly007.oss-cn-beijing.aliyuncs.com/image/CommonArticleDesign1-min.png)

元素的**索引**表示特定数据的位置，当调用该索引的列表项时，将显示该元素。通常，列表项是从其索引中调用的。
**列表类型 –** 
根据长度，大致有两种类型的列表： 
 

1. **固定长度列表**
2. **成长清单**

 

## 固定长度列表

在这里，列表的大小是最初声明的，不能在运行时更改。
**句法：** 



```dart
void main()
{
	var gfg = new List(3);
	gfg[0] = 'Geeks';
	gfg[1] = 'For';
	gfg[2] = 'Geeks';

	// Printing all the values in List
	print(gfg);

	// Printing value at specific position
	print(gfg[2]);
}

```

**向可增长列表添加多个值 –** 



```dart
void main()
{
	var gfg = [ 'Geeks' ];

	// Printing all the values in List
	print(gfg);

	// Adding multiple values in List and printing it

	// list_name.addAll([val 1, val 2, ...]);
	gfg.addAll([ 'For', 'Geeks' ]);
	print(gfg);
}

```

**在特定索引处向可增长列表添加值 -** 

```dart
void main()
{
	var gfg = [ 'Geeks', 'Geeks' ];

	// Printing all the values in List
	print(gfg);

	// Adding new value in List at specific index and printing it

	// list_name.insert(index, value);
	gfg.insert(1, 'For');
	print(gfg);

```

**在特定索引处向可增长列表添加多个值 -** 

```dart
void main()
{
	var gfg = [ 'Geeks' ];

	// Printing all the values in List
	print(gfg);

	// Adding new value in List at specific index and printing it

	// list_name.insertAll(index, list_of_values);
	gfg.insertAll(1, [ 'For', 'Geeks' ]);
	print(gfg);

	// Element at index 1 in list
	print(gfg[1]);
}

```

**列表类型（其维度的基础）：** 
基于维度的列表有多种数量，但其中最流行的是：

1. **一维 (1-D) 列表**
2. **二维 (2-D) 列表**
3. **3 维 (3-D) 列表**
4. **多维列表**

**在这里，我们已经讨论了 1-D 列表。**
 

### 二维 (2-D) 列表 –

在这里，列表是在两个维度中定义的，从而形成了表格的外观。
**创建二维列表 -** 

```dart
void main()
{
	int a = 3;
	int b = 3;

	// Creating two dimensional list
	var gfg = List.generate(a, (i) = > List(b), growable: false);

	// Printing its value
	print(gfg);

	// Inserting values
	for (int i = 0; i < 3; ++i) {
		for (int j = 0; j < 3; ++j) {
			gfg[i][j] = i + j;
		}
	}
	// Printing its value
	print(gfg);
}

```

**另一种创建二维列表的方法 -** 

```dart
void main()
{
	// Creating three dimensional list
	var gfg = List.generate(3, (i) = > List.generate(3, (j) = > i + j));

	// Printing its value
	print(gfg);
}

```

还有另一种创建二维列表的方法，即给出与索引相关的值，这将导致创建二维列表。
 

### 3 维 (3-D) 列表 –

3-D 列表的表示非常困难，但其创建类似于 2-D 列表。
**例子：** 

```dart
void main()
{
	// Creating three dimensional list
	var gfg = List.generate(3, (i) = > List.generate(3,
							(j) = > List.generate(3,
							(k) = > i + j + k)));

	// Printing its value
	print(gfg);
}

```

**注意：** 
以类似的方式可以创建***n 维*****列表，**即使用“List.generate()”方法。

