---
title: "Flutter面试题3之常见开发问题"
date: 2021-08-21T09:23:38+08:00
draft: false
tags: ["Flutter"]
categories: ["Flutter"]
---

> 本文主要介绍

<!--more-->

# 1.解释一下 Flutter 中的热重载？

热重载功能使您可以快速轻松地在项目中执行实验。它有助于构建 UI、添加新功能、修复错误并加快应用程序开发。要执行 Flutter 应用程序的热重载，请执行以下步骤：

- 在受支持的 Flutter 编辑器或终端窗口中运行应用程序。
- 修改项目中的任何 Dart 文件。
- 如果您使用支持 Flutter 的 IDE，则选择 Save All 或单击工具栏上的 Hot Reload 按钮。您可以立即在模拟器或真实设备中看到结果。

# 2.flutter 中的构建上下文是什么？

**BuildContext** Class 只是对 Widget 在所有**构建**的 Widget 的树结构中的位置的引用。每个**Flutter**小部件都有一个带有**BuildContext**参数的 @override **build** () 方法。

# 3.flutter中的脚手架是什么？

甲**脚手架**窗口小部件提供了实现的基本材料设计可视布局结构的框架**扑**应用程序。它提供了用于显示抽屉、小吃店和底部床单的 API。看看它的构造函数和它的属性。

# 4.flutter中的material app是什么？

**MaterialApp**是一个小部件，它引入了许多有趣的工具，例如 Navigator 或 Theme 来帮助您开发您的**应用程序**。…然后当您想使用 Text 或 InkWell 时，使用引入**Material**实例（如 Scaffold 、 Appbar 、 Dialog 等）的小部件。

# 5.flutter中setState有什么用？

状态对象由框架创建。为了更改您的小部件，您需要更新状态对象，这可以使用可用于有状态小部件的**setState** ()**函数**来完成。**setState** () 设置状态对象的属性，进而触发对 UI 的更新。但是这种技术有其自身的缺点。

# 6.Dart 中“var”和“dynamic”类型的区别？

**dynamic**是所有 Dart 对象的基础类型。在大多数情况下，您不需要明确使用它。

**var**是一个关键字，意思是“我不在乎说明这里的类型是什么。” Dart 将用初始化器类型替换**var**关键字，如果没有初始化器，则默认情况下保持**动态**。

如果您希望变量赋值在其生命周期内发生变化，请使用**var**：

```dart
var msg = "Hello world.";
msg = "Hello world again.";
```

如果您希望变量赋值在其生命周期内保持不变，请使用**final**：

```dart
final msg = "Hello world.";
```

使用**final**（自由地）将帮助您发现您无意中更改了变量赋值的情况。

请注意，在对象方面，**final**和**const**之间存在细微差别。 **final**不一定使对象本身不可变，而**const**则：

```dart
// can add/remove from this list, but cannot assign a new list to fruit.
final fruit = ["apple", "pear", "orange"];
fruit.add("grape");

// cannot mutate the list or assign a new list to cars.
final cars = const ["Honda", "Toyota", "Ford"];

// const requires a constant assignment, whereas final will accept both:
const names = const ["John", "Jane", "Jack"];
```

在[DartPad 中](https://dartpad.dartlang.org/)试试这个：

```dart
void main() {
  dynamic x = 'hal';
  x = 123;
  print(x);
  var a = 'hal';
  a = 123;
  print(a);
}
```

dynamic：可以改变变量的类型，&可以在代码后面改变变量的值。

**var：** *不能改变*变量的TYPE，但可以在代码后面改变变量的VALUE。

**final：** *不能改变*变量的类型，&*不能*在代码后面*改变*变量的值。

```dart
dynamic v = 123;   // v is of type int.
v = 456;           // changing value of v from 123 to 456.
v = 'abc';         // changing type of v from int to String.

var v = 123;       // v is of type int.
v = 456;           // changing value of v from 123 to 456.
v = 'abc';         // ERROR: can't change type of v from int to String.

final v = 123;       // v is of type int.
v = 456;           // ERROR: can't change value of v from 123 to 456.
v = 'abc';         // ERRO
```

# 7.Dart 支持命名可选参数和位置可选参数。两者之间有什么区别？



Dart 有两种类型的可选参数：*named*和*positional*。在我讨论差异之前，让我先讨论相似之处。

Dart 的可选参数是*可选*的，因为调用者在调用函数时不需要为参数指定值。

可选参数只能在任何必需参数之后声明。

可选参数可以有一个默认值，在调用者没有指定值时使用。

**位置可选参数**

包裹的参数`[ ]`是一个位置可选参数。下面是一个例子：

```dart
getHttpUrl(String server, String path, [int port=80]) {
  // ...
}
```

在上面的代码中，`port`是可选的，默认值为`80`。

您可以`getHttpUrl`使用或不使用第三个参数进行调用。

```dart
getHttpUrl('example.com', '/index.html', 8080); // port == 8080
getHttpUrl('example.com', '/index.html');       // port == 80
```

您可以为一个函数指定多个位置参数：

```dart
getHttpUrl(String server, String path, [int port=80, int numRetries=3]) {
  // ...
}
```

可选参数是*位置*参数，`port`如果要指定`numRetries`.

```dart
getHttpUrl('example.com', '/index.html');
getHttpUrl('example.com', '/index.html', 8080);
getHttpUrl('example.com', '/index.html', 8080, 5);
```

当然，除非您应该知道 8080 和 5 是什么，否则很难说出那些看似神奇的数字是什么。您可以使用*命名的可选参数*来创建更具可读性的 API。

**命名的可选参数**

包装的参数`{ }`是一个命名的可选参数。下面是一个例子：

```dart
getHttpUrl(String server, String path, {int port = 80}) {
  // ...
}
```

您可以`getHttpUrl`使用或不使用第三个参数进行调用。你**必须**调用函数时使用的参数名称。

```dart
getHttpUrl('example.com', '/index.html', port: 8080); // port == 8080
getHttpUrl('example.com', '/index.html');             // port == 80
```

您可以为一个函数指定多个命名参数：

```dart
getHttpUrl(String server, String path, {int port = 80, int numRetries = 3}) {
  // ...
}
```

因为命名参数是按名称引用的，所以它们的使用顺序可以不同于它们的声明。

```dart
getHttpUrl('example.com', '/index.html');
getHttpUrl('example.com', '/index.html', port: 8080);
getHttpUrl('example.com', '/index.html', port: 8080, numRetries: 5);
getHttpUrl('example.com', '/index.html', numRetries: 5, port: 8080);
getHttpUrl('example.com', '/index.html', numRetries: 5);
```

我相信命名参数使调用站点更易于理解，尤其是当有布尔标志或上下文外的数字时。

**检查是否提供了可选参数**

不幸的是，您无法区分“未提供可选参数”和“已为可选参数提供默认值”的情况。

**注意：**您可以使用位置可选参数**或**命名可选参数，*但不能*在同一函数或方法中同时使用。不允许出现以下情况。

```dart
thisFunctionWontWork(String foo, [String positonal], {String named}) {
  // will not work!
}
```



