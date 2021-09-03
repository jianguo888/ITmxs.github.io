---
title: "《Flutter之Dart空安全概览》"
subtitle: ""
date: 2021-05-28T12:50:36+08:00
lastmod: 2021-05-28T12:50:36+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter","dart"]
categories: ["Flutter","dart"]

hiddenFromHomePage: false
hiddenFromSearch: false
twemoji: false

fontawesome: true
linkToMarkdown: true
rssFullText: false


featuredImage: "https://luckly007.oss-cn-beijing.aliyuncs.com/img/90c6cc12-742e-4c9f-b318-b912f163b8d0.png"
featuredImagePreview: "https://luckly007.oss-cn-beijing.aliyuncs.com/img/90c6cc12-742e-4c9f-b318-b912f163b8d0.png"

toc:
  enable: true
  auto: true
math:
  enable: false
lightgallery: false
license: ""
share:
  enable: true
comment: true
mapbox:
  accessToken: ""
---



> 本文概述空安全是如何影响 Dart 语言的。如果希望深入了解空安全性的工作原理

<!--more-->

Dart 语言即将 **全面引入** 健全的空安全机制了，当您选择使用空安全时，代码中的类型将默认是非空的， 意味着 **除非您声明它们可空**，它们的值都不能为空。有了空安全，原本处于您的 **运行时** 的空值引用错误 将变为 **编译时** 的分析错误。

今天的推送文章将概述空安全是如何影响 Dart 语言的。如果希望深入了解空安全性的工作原理，点击此处[《深入理解 Dart 空安全》](https://luckly.work/flutter%E4%B9%8B%E6%B7%B1%E5%85%A5%E7%90%86%E8%A7%A3dart%E7%A9%BA%E5%AE%89%E5%85%A8/)。

有了空安全，Dart 分析器可以进行更好的检查。例如：它将在您读取一个可空的变量前提示您进行空检查。由于 Dart 的空安全是十分有效的， Dart 编译器和运行环境也同时可以通过优化减少内部的空安全检查， 这样应用就可以更快且更小。

> 由于目前空安全仍然处于技术预览阶段， 所以 **请勿在生产环境使用空安全。**请注意，Flutter 目前尚未支持空安全。请通过 Dart SDK 测试这项特性， 并且给予我们反馈。

与空安全相关的新操作符和关键字有 `?`、`!` 和 `late`。如果您曾经使用过 Kotlin、TypeScript 或 C# 进行开发， 那么这些空安全的语法看起来会有些熟悉。这是设计使然：**Dart 语言的目标是不让您感到惊讶**。

您可以在您的项目设置里启用技术预览版 SDK（见本文下面章节），从而在您的项目里实践空安全。或者通过 **支持空安全的 DartPad** (nullsafety.dartpad.cn) 进行练习。

![image-20210530220249906](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210530220249906.png)

支持空安全的 DartPad 界面截图

## 创建变量

当您创建变量时， 您可以使用 `?` 和 `late`告诉 Dart 这些变量是否可空。

以下是一些声明 **非空变量** 的例子（假设您已经使用了空安全）：

```
// In null-safe Dart, none of these can ever be null.
var i = 42; // Inferred to be an int.
String name = getFileName();
final b = Foo();
```

如果这些变量 **可以** 为空值 ( `null` )， 在类型声明处 **加上 `?`**。

```
int? aNullableInt = null;
```

在您已经明确一个非空变量一定会在使用前初始化， 而 Dart 分析器仍然无法明确的情况下， 您可以在变量的类型前 **加上 `late`**：

```
class IntProvider {
  late int aRealInt;
  
  IntProvider() {
    aRealInt = calculate();
  }
}
```

关键词 `late` 有两个作用：

- 分析器并不要求您立刻对使用 `late` 声明的变量初始化为非空值；
- 运行环境将懒加载 `late` 变量。例如：如果一个非空的实例变量必须被计算， 那么 `late` 将会让它的计算延迟到首次使用再进行。

## 在变量和表达式中使用

有了空安全，Dart 分析器会在要求非空而得到一个空值时抛出错误。这并不像听起来那么糟糕。当函数内的一个变量或一句表达式为可空的类型， 但不能包含空值时，分析器通常也可以识别。

> 分析器不能对整个应用流程进行模拟， 所以它并不能对全局变量或者类的字段进行推断。

当您正在调用一个可空的变量或者表达式时， 请确保您自己处理了空值。例如：您可以使用 `if` 条件句、`??` 操作符 或是 `?.` 操作符来处理可能为空的值。

下面是一个使用 **`??` 操作符**来避免将非空变量赋予空值的例子。

```
int value = aNullableInt ?? 0; // 0 if it's null; otherwise, the integer
```

下面是一段类似的代码，但使用了 `if` 条件句来检查是否为空：

```
int definitelyInt(int? aNullableInt) {
  if (aNullableInt == null) {
    return 0;
  }
  return aNullableInt; // Can't be null!
}
```

如果您能确定一条可空的表达式不为空， 您可以在其后添加 `!` 让 Dart 处理为非空。

```
int? aNullableInt = 2;
int value = aNullableInt!; // `aNullableInt!` is an int.
// This throws if aNullableInt is null.
```

> 如果您并不完全确定值为非空，**请不要使用 `!` 操作符**。

如果您想改变一个可空变量的类型，您可以使用 **类型转换操作符 (`as`)**， 这是 `!` 操作符做不到的。下面的例子使用了 `as` 将 `num?` 转换为 `int`。

```
return maybeNum() as int;
```

一旦您开始使用空安全，当操作对象可能为空时， 您将不再能使用 **成员访问符 (`.`)**。取而代之的是可空版本的 `?.`。

```
double? d;  
print(d?.floor()); // Uses `?.` instead of `.` to invoke `floor()`.
```

## 理解列表、集合及映射类型中的使用

列表、集合和映射是 Dart 程序中常用的集合类型， 所以您会需要了解它们如何与空安全进行交互。下面是一些 Dart 代码如何使用这些集合类型的例子：

- Flutter 布局 widgets 中的 `Column` 拥有 `children` 属性，它是元素为 `Widget` 的 `List`；
- Veggie Seasons 示例应用中使用了元素为 `VeggieCategory` 的 `Set` 用于保存用户的食物偏好；
- GitHub Dataviz 示例应用中的 `fromJson()` 方法会 从类型为 `Map<String, dynamic>` 的 JSON 数据中 创建一个实例。



- 示例应用: Veggie Seasons
  github.com/flutter/samples/tree/master/veggieseasons 
- 示例应用: GitHub Dataviz
  github.com/flutter/samples/tree/master/web/github_dataviz

### 列表和集合类型

当您在声明列表或集合类型时， 仔细思考什么可以为空。下面的表格展示了使用了空安全的列表的可空可能性。

|       类型       | 列表能为空吗？ | 元素 (string) 能为空吗？ |                 描述                  |
| :--------------: | :------------: | :----------------------: | :-----------------------------------: |
|  `List<String>`  |       No       |            No            |     一个包含非空字符串的 非空列表     |
| `List<String>?`  |      Yes       |            No            |   一个包含非空字符串的 **可空**列表   |
| `List<String?>`  |       No       |           Yes            |   一个包含**可空**字符串的 非空列表   |
| `List<String?>?` |      Yes       |           Yes            | 一个包含**可空**字符串的 **可空**列表 |

在通过字面量创建一个列表或集合时，通常您会看到字面量类型注解，而不是表格上面的类型声明。例如下面的代码可以用于创建一个名为 `nameList` 的 `List<String?>`和一个名为 `nameSet` 的 `Set<String?>`：

```
var nameList = <String?>['Andrew', 'Anjan', 'Anya'];
var nameSet = <String?>{'Andrew', 'Anjan', 'Anya'};
```

### 映射类型

映射类型大部分会与您预期的表现一致，除了一处不同：**查询的返回值可能为空**。当一个映射中并未出现包含某个 key 时，将会是空值。

看看下面的示例代码。您认为 `uhOh` 的类型和值会是什么呢？

```
var myMap = <String, int>{'one': 1};
var uhOh = myMap['two'];
```

答案是 `uhOh` 是空值，类型为 `int?`。

就像列表和集合一样，映射也会有一些不同的类型：

|         类型         | 映射能 为空吗？ | 元素 (int) 能为空吗？ |
| :------------------: | :-------------: | :-------------------: |
|  `Map<String, int>`  |       No        |          No*          |
| `Map<String, int>?`  |       Yes       |          No*          |
| `Map<String, int?>`  |       No        |          Yes          |
| `Map<String, int?>?` |       Yes       |          Yes          |

*** 尽管映射中的所有 int 值都是非空值， 但当您在查询时使用了无效的 key，返回的仍是空值。**

由于映射查询可能返回空值， 您不能将其传递给非空变量：

```
// Assigning a lookup result to a non-nullable
// variable causes an error.
int value = <String, int>{'one': 1}['one']; // ERROR
```

一个变通的方法是将变量的类型改为可空：

```
int? value = <String, int>{'one': 1}['one']; // OK
```

如果您确定查询必定成功， 那么另一种避免问题的方法是加上 `!`：

```
int value = <String, int>{'one': 1}['one']!; // OK
```

更安全的实现是仅在查询结果不为空时使用结果。您可以使用 `if` 条件句或者 `??` 操作符进行判断。下面是当查询结果为空时使用 `0` 为值的一个例子：

```
var aMap = <String, int>{'one': 1};
...
int value = aMap['one'] ?? 0;
```

## 启用空安全

Dart 工具目前对空安全代码的分析、编译和运行的支持仍然在实验阶段。如果想使用空安全，请按照如下步骤通过命令行命令或者 IDE 设置启用技术预览版本的空安全。

- 为 Dart 项目配置支持空安全的技术预览版 SDK；
- 为所有的 Dart 工具加入实验性的命令行参数。

### 设置 SDK 版本

到 Dart.cn 查看文档 SDK 版本约束来设定一个支持空安全的 SDK 版本。

```
environment: sdk: '>=2.10.0-56.0.dev <3.0.0'
```

> 我们建议这里使用 Dart 或者 Flutter SDK 的 **最新 dev 发布渠道**。了解如何获得最新的发布渠道信息，请查看 Dart SDK 页面的 Dev 发布渠道部分。或者 **Flutter SDK** 的 Dev 发布渠道部分。

### 加入实验性的命令行参数

如果需要使用空安全，需要把 `non-nullable` 这个实验性的命令行参数 加入到所有的 Dart 工具。比如：

```
$ ~/dev/dart-sdk/bin/dart --enable-experiment=non-nullable bin/main.dart
```

关于如何在 IDE 和命令行工具里加入实验性的命令行参数，请查看文档：**实验性的参数标记**。

- 加入实验性参数标记
  dart.cn/tools/experiment-flags

> 等到空安全在 beta 或者 stable 渠道发布时，就无需再加入空安全的实验性参数了。

### 示例

有关启用和使用空安全的 Dart 命令行程序的完整示例，请参见 **空安全示例** 应用。

- 空安全示例应用
  github.com/dart-lang/samples/tree/master/null_safety/calculate_lix

## 学习更多

更多关于空安全的信息，请前往以下内容继续阅读：

- Dart 重要通知邮件群组
  groups.google.com/a/dartlang.org/d/forum/announce



- Dart 官方博客
  medium.com/dartlang



- 支持空声明的 DartPad
  nullsafety.dartpad.cn



- 空安全的问题跟踪
  github.com/dart-lang/language/issues/110