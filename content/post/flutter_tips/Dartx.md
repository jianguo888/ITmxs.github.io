---
title: "Dartx"
date: 2021-08-24T23:27:54+08:00
draft: false
tags: ["Flutter"]
categories: ["Flutter"]
---

> 本文主要介绍

<!--more-->

[**dartx**](https://pub.dev/packages/dartx)包允许开发人员对集合(和其他 Dart 类型)使用优雅且可读的单行操作。

下面我们将比较如何使用普通 Dart 与[dartx](https://pub.dev/packages/dartx)解决这些任务：

✔ 第一个/最后一个集合项或空/默认值/按条件；

✔ 根据索引映射/过滤/迭代集合项；

✔ 将集合转换为地图；

✔ 分类收藏；

✔收集独特的物品；

✔ 物品属性的最小值/最大值/平均值；

✔ 过滤掉空项目；

并查看一些其他有用的扩展。

```dart

import 'package:dartx/dartx.dart';
```

要在普通 Dart 中获取第一个和最后一个集合项，可以这样写：

```dart
final first = list.first;

final last = list.last;
```

抛出一个`*StateError*` if`*list*`是空的。或明确返回`*null*`：

```dart
final firstOrNull = list.isNotEmpty ? list.first : null;

final lastOrNull = list.isNotEmpty ? list.last : null;
```

使用[dartx](https://pub.dev/packages/dartx)允许：

```dart
final firstOrNull = list.firstOrNull;

final lastOrNull = list.lastOrNull;
```

同样

```dart
final elementAtOrNull = list.elementAtOrNull(index);
```

## ... 或默认值

鉴于你现在还记得`.first`和`.last`当吸气抛出错误`*list*`是空的，拿到第一个和最后的集合项目或默认值，以dart，你会写：

```dart

final firstOrDefault = (list.isNotEmpty ? list.first : null) ?? defaultValue;

final lastOrDefault = (list.isNotEmpty ? list.last : null) ?? defaultValue;
```

使用[dartx](https://pub.dev/packages/dartx)：

```dart

final firstOrDefault = list.firstOrDefault(defaultValue);

final lastOrDefault = list.lastOrElse(defaultValue);
 
```

类似于`*elementAtOrNull*`：

```dart
final elementAtOrDefault = list.elementAtOrDefault(index, defaultValue);
```

返回`*defaultValue*`如果`*index*`是的出界`*list*`。

## … 按条件

要获取匹配某个条件 or 的第一个和最后一个集合项`*null*`，一个简单的 Dart 实现将是：

```dart

final firstWhere = list.firstWhere((x) => x.matches(condition));

final lastWhere = list.lastWhere((x) => x.matches(condition));
```

除非提供`*StateError*`，`*list*`否则将抛出空`*orElse*` ：

```dart
final firstWhereOrNull = list.firstWhere((x) => x.matches(condition), orElse: () => null);

final lastWhereOrNull = list.lastWhere((x) => x.matches(condition), orElse: () => null);
```

[dartx](https://pub.dev/packages/dartx)将实现缩短为：

```dart

final firstWhereOrNull = list.firstOrNullWhere((x) => x.matches(condition));

final lastWhereOrNull = list.lastOrNullWhere((x) => x.matches(condition));
```

# ✔ … 收集项目取决于它们的索引

## 地图…

当您需要获取一个新集合，其中每个项目都以某种方式取决于其索引时，这种情况并不少见。例如，每个新项目都是来自原始集合的项目 + 其索引的字符串表示。

如果你喜欢我喜欢单线，那么简单的 Dart 就是：

```dart

final newList = list.asMap()
  .map((index, x) => MapEntry(index, '$index $x'))
  .values
  .toList();
```

使用[dartx](https://pub.dev/packages/dartx)就简单多了：

```dart
final newList = list.mapIndexed((index, x) => '$index $x').toList();
```

我申请`*.toList()*`是因为这个和大多数其他扩展方法返回 lazy `*Iterable*`。

## 过滤…

再举一个例子，假设只需要收集奇数索引项。使用普通的 Dart，可以这样实现：

```dart
final oddItems = [];
for (var i = 0; i < list.length; i++) {
  if (i.isOdd) {
    oddItems.add(list[i]);
  }
}
```

或者在一行中：

```dart
final oddItems = list.asMap()
  .entries
  .where((entry) => entry.key.isOdd)
  .map((entry) => entry.value)
  .toList();
```

使用[dartx，](https://pub.dev/packages/dartx)它只是：

```dart

final oddItems = list.whereIndexed((x, index) => index.isOdd).toList();

// or

final oddItems = list.whereNotIndexed((x, index) => index.isEven).toList();
```

## 迭代…

您将如何记录指定项目索引的集合内容？

在普通的飞镖中：

```dart
for (var i = 0; i < list.length; i++) {
  print('$i: ${list[i]}');
}
```

使用[dartx](https://pub.dev/packages/dartx)：

```dart
list.forEachIndexed((element, index) => print('$index: $element'));
```

# ✔ 将集合转换为地图

例如，需要将不同`*Person*`对象的列表转换为`*Map<String, Person>*`键是`*person.id*`，值是整个人的实例。

```dart

final peopleMap = people.asMap().map((index, person) => MapEntry(person.id, person));
```

使用[dartx](https://pub.dev/packages/dartx)：

```dart
final peopleMap = people.associate((person) => MapEntry(person.id, person));

// or

final peopleMap = people.associateBy((person) => person.id);
```

要获得一张地图，其中键`*DateTime*`和值是`*List<Person>*`那天出生的人的，用普通的 Dart 写：

```dart
final peopleMapByBirthDate = people.fold<Map<DateTime, List<Person>>>(
  <DateTime, List<Person>>{}, 
  (map, person) {
    if (!map.containsKey(person.birthDate)) {
      map[person.birthDate] = <Person>[];
    }
    map[person.birthDate].add(person);
    return map;
  },
);
```

使用[dartx 就](https://pub.dev/packages/dartx)简单多了：。

```dart
final peopleMapByBirthDate = people.groupBy((person) => person.birthDate);
```

# ✔ 排序集合

您将如何在普通 Dart 中对集合进行排序？你必须记住

```dart
list.sort();
```

修改源集合，并获得一个新的实例，你必须写：

```dart
final orderedList = [...list]..sort();
```

[dartx](https://pub.dev/packages/dartx)提供了一个扩展来获取新`*List*`实例：

```dart

final orderedList = list.sorted();

// and

final orderedDescendingList = list.sortedDescending();
```

您将如何根据某些属性对收藏品进行排序？

普通飞镖：

```dart
final orderedPeople = [...people]
  ..sort((person1, person2) => person1.birthDate.compareTo(person2.birthDate));
```

使用[dartx](https://pub.dev/packages/dartx)：

```dart
final orderedPeople = people.sortedBy((person) => person.birthDate);

// and

final orderedDescendingPeople = people.sortedByDescending((person) => person.birthDate)
```

更进一步，您可以按多个属性对集合项进行排序：

```dart
final orderedPeopleByAgeAndName = people
  .sortedBy((person) => person.birthDate)
  .thenBy((person) => person.name);

// and

final orderedDescendingPeopleByAgeAndName = people
  .sortedByDescending((person) => person.birthDate)
  .thenByDescending((person) => person.name);
```

# ✔ 收集独特的物品

要获得不同的集合项，可以使用这种简单的 Dart 实现：

```dart

final unique = list.toSet().toList();
```

这并不能保证保留项目订单或提出多行解决方案。

使用[dartx](https://pub.dev/packages/dartx)，它就像：

```dart
final unique = list.distinct().toList();

// and

final uniqueFirstNames = people.distinctBy((person) => person.firstName).toList();
```

# ✔ 按项目属性的最小值/最大值/平均值

要查找最小/最大集合项，我们可以例如对其进行排序并获取`*first*`/`*last*`项：

```dart

final min = ([...list]..sort()).first;

final max = ([...list]..sort()).last;
```

同样的方法可以应用于按 items 属性排序：

```dart
final minAge = (people.map((person) => person.age).toList()..sort()).first;

final maxAge = (people.map((person) => person.age).toList()..sort()).last;
```

或：

```dart
final youngestPerson = 
  ([...people]..sort((person1, person2) => person1.age.compareTo(person2.age))).first;

final oldestPerson = 
  ([...people]..sort((person1, person2) => person1.age.compareTo(person2.age))).last;
```

但是使用[dartx](https://pub.dev/packages/dartx)就容易多了：

```dart
final youngestPerson = people.minBy((person) => person.age);

final oldestPerson = people.maxBy((person) => person.age);
```

顺便说一下，这将返回`*null*`空集合。

如果集合项实现`*Comparable*`，则可以应用没有选择器的方法：

```dart
final min = list.min();

final max = list.max();
```

您还可以轻松获得平均值：

```dart
final average = list.average();

// and

final averageAge = people.averageBy((person) => person.age);
```

以及`*num*`集合或`*num*`项目属性的总和：

```dart

final sum = list.sum();

// and

final totalChildrenCount = people.sumBy((person) => person.childrenCount);
```

# ✔ 过滤掉空项目

使用普通飞镖：

```dart
final nonNullItems = list.where((x) => x != null).toList();
```

使用[dartx](https://pub.dev/packages/dartx)：

```dart
final nonNullItems = list.whereNotNull().toList();
```

# ✔ 更多有用的扩展

[dartx 中](https://pub.dev/packages/dartx)还有其他有用的扩展。我们不会在这里深入细节，但我希望命名和代码是不言自明的。

## 连接到字符串

```dart
final report = people.joinToString(
  separator: '\n',
  transform: (person) => '${person.firstName}_${person.lastName}',
  prefix: '<<️',
  postfix: '>>',
);
```

## 所有(每个)/无

```dart
final allAreAdults = people.all((person) => person.age >= 18);

final allAreAdults = people.none((person) => person.age < 18);
```

## 第一个/第二个/第三个/第四个收藏品

```dart

final first = list.first;
final second = list.second;
final third = list.third;
final fourth = list.fourth;
```

## takeFirst / takeLast

```dart
final youngestPeople = people.sortedBy((person) => person.age).takeFirst(5);

final oldestPeople = people.sortedBy((person) => person.age).takeLast(5);
```

## firstWhile / lastWhile

```dart
final orderedPeopleUnder50 = people
  .sortedBy((person) => person.age)
  .firstWhile((person) => person.age < 50)
  .toList();

final orderedPeopleOver50 = people
  .sortedBy((person) => person.age)
  .lastWhile((person) => person.age >= 50)
  .toList();
```

该[dartx](https://pub.dev/packages/dartx)包中包含有更多的扩展`*Iterable*`，`*List*`和[其他类型的飞镖](https://github.com/leisim/dartx/tree/master/lib/src)。探索其功能的最佳方式是浏览[源代码](https://github.com/leisim/dartx)。

https://medium.com/flutter-community/dart-collections-with-dartx-extensions-959a0b42849e