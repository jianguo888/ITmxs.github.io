---
title: "如何在Flutter中解析Json"
date: 2021-09-29T08:49:03+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---

> 本文主要展示了在 Flutter 中转换 json 的最佳和最方便的方法。

Dart 内置了对解析 json 的支持。给定一个字符串，您可以使用该`dart:convert`库并将 Json（如果有效 json）转换为带有字符串键和动态对象的 Map。您可以直接解析 json 并使用映射，也可以将其解析并放入类型化对象中，以便您的数据具有更多结构并且更易于维护。

## 直接解析和使用

```dart
var jsonData = '{ "name" : "Dane", "alias" : "FilledStacks"  }';
var parsedJson = json.decode(jsonData);
print('${parsedJson.runtimeType} : $parsedJson');
```

运行上面的代码会给你

```text
_InternalLinkedHashMap<String, dynamic> : {name: Dane, alias: FilledStacks}
```

因此，您访问解析数据的方式是在返回的地图上使用键索引。让我们索引映射并获取名称和别名。

```dart
...
var name = parsedJson['name'];
var alias = parsedJson['alias'];
print('$name is $alias');
```

这将输出。

```text
Dane is FilledStacks
```

## 解析为对象

程序员通常的做法是将这些数据放入一个为您的数据建模的类中，而不是直接使用解析的数据。这通常通过 Dart 中的命名构造函数来完成。让我们将测试数据放入模型中。我们将首先以基本方式定义模型，然后进入命名构造函数。

```dart
class User {
  String name;
  String alias;

  User(Map<String, dynamic> data) {
    name = data['name'];
    alias = data['alias'];
  }
}

...

var jsonData = '{ "name" : "Dane", "alias" : "FilledStacks"  }';
var parsedJson = json.decode(jsonData);
var user = User(parsedJson);
print('${user.name} is ${user.alias}');
```

这与原始访问相同，但更易于阅读、理解和维护。除了在构造函数中，也没有使用 String 键。这种方式很好，但只有一个构造函数限制了您，并且不会让您的模型开放以供扩展。您必须对其进行修改才能将任何其他功能加入其中。相反，通常的做法是让构造函数为您设置所有属性，并使用命名构造函数使用外部数据显式创建模型。

```dart
class User {
  final String name;
  final String alias;

  User({this.name, this.alias});

  User.fromJson(Map<String, dynamic> data)
      : name = data['name'],
        alias = data['alias'];
}

...

var jsonData = '{ "name" : "Dane", "alias" : "FilledStacks"  }';
var parsedJson = json.decode(jsonData);
var user = User.fromJson(parsedJson);
print('${user.name} is ${user.alias}');
```

这更简洁、更明确，您的模型现在更少依赖传递给它的 Map。要考虑的一个问题是需要复杂模型的复杂 json。你真的想为一个简单的模型输入所有这些字段吗？我会这样做，因为我认为从长远来看它会更好，但幸运的是我们不必这样做。[json_to_dart](https://javiercbk.github.io/json_to_dart/)