---
title: "Dart编程Map"
date: 2021-09-01T16:30:42+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter","dart"]
categories: ["Flutter","dart"]
---

在 Dart 编程中，Map 是类似字典的数据类型，以键值形式(称为锁键)存在。map数据类型中的数据类型没有限制。map非常灵活，可以根据需求改变其大小。但是，需要注意的是，所有锁(键)在map数据类型中都必须是唯一的。

我们可以通过两种方式声明 Map： 

 

1. 使用map
2. 使用map构造函数

### Map Literals:

可以使用map literals声明map，如下所示： 

```dart
句法： 
// 使用map文字创建map
var map_name = { key1 : value1, key2 : value2, ..., key n : value n }
```

 

**示例 1：** 

 

使用map文字创建map 
 

```dart
void main() {
// Creating Map using is literals
var gfg = {'position1' : 'Geek', 'position2' : 'for', 'position3' : 'Geeks'};

// Printing Its content
print(gfg);

// Printing Specific Content
// Key is defined
print(gfg['position1']);

// Key is not defined
print(gfg[0]);
}

```

示例2
 

```dart
void main() {
// Creating Map using is literals
var gfg = {'position1' : 'Geek' 'for' 'Geeks'};

// Printing Its content
print(gfg);

// Printing Specific Content
// Key is defined
print(gfg['position1']);
}

```

**示例 3：** 

在 Map 中插入一个新值 
  

```dart
void main() {
// Creating Map
var gfg = {'position1' : 'Geeks' 'for' 'Geeks'};

// Printing Its content before insetion
print(gfg);

// Inserting a new value in Map
gfg ['position0'] = 'Welcome to ';

// Printing Its content after insertion
print(gfg);

// Printing Specific Content
// Keys is defined
print(gfg['position0'] + gfg['position1']);
}

```

## map构造器： 

```dart
句法： 
// 使用 Map Constructor 创建 Map
var map_name = new Map();
// 在 Map 中分配值和键
map名称 [ 键 ] = 
```

**示例 1：**

使用map构造函数创建map  

```dart
void main() {
// Creating Map using Constructors
var gfg = new Map();

// Inserting values into Map
gfg [0] = 'Geeks';
gfg [1] = 'for';
gfg [2] = 'Geeks';

// Printing Its content
print(gfg);

// Printing Specific Content
// Key is defined
print(gfg[0]);
}

```

**示例 2：** 

 

将相同的键分配给不同的元素  

```dart
void main() {
// Creating Map using Constructors
var gfg = new Map();

// Inserting values into Map
gfg [0] = 'Geeks';
gfg [0] = 'for';
gfg [0] = 'Geeks';

// Printing Its content
print(gfg);

// Printing Specific Content
// Key is defined
print(gfg[0]);
}

```

