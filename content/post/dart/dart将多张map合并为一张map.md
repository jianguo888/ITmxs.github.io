---
title: "Dart将多张map合并为一张map"
date: 2021-08-31T08:42:15+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["flutter","dart"]
categories: ["flutter","dart"]
---

如何将 dart 中的 2 个或更多 map 合并/合并为 1 张 map ？
例如我有类似的东西:

```
 var firstMap = {"1":"2"};
 var secondMap = {"1":"2"};
 var thirdMap = {"1":"2"};
```


我想要:

```
 var finalMap = {"1":"2", "1":"2", "1":"2"};
```



**最佳答案**

您可以使用 `addAll` `Map`的方法目的

```
var firstMap = {"1":"2"};
var secondMap = {"2":"3"};

var thirdMap = {};

thirdMap.addAll(firstMap);
thirdMap.addAll(secondMap);

print(thirdMap);
```


或者

```
var thirdMap = {}..addAll(firstMap)..addAll(secondMap);
```


更新

从 dart sdk 2.3 开始
您可以使用价差运算符 `...`

```dart
final firstMap = {"1":"2"};
final secondMap = {"2":"3"};

final thirdMap = {
   ...firstMap,
   ...secondMap,
};
```

