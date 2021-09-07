---
title: "Dart如何向map添加新的键值对"
date: 2021-09-07T10:58:19+08:00
draft: true
---

# Dart：如何向地图添加新的键/值对

这篇简短的文章向您展示了在 Dart（以及 Flutter）中向给定地图添加新键/值对的几种方法。



## 使用 map[newKey] = newValue 语法

**例子：**

```
// main.dart
Map<String, dynamic> map = {
  'key1': 'Dog',
  'key2': 'Chicken',
  'key3': 'Kindacode.com',
  'key4': 'Buffalo'
};

void main() {
  map['key5'] = 'Blue';
  map['key6'] = 'Green';
  map['some_key'] = 'Hey Bro';

  print(map);
}
```

**输出：**

```
{
  key1: Dog, 
  key2: Chicken, 
  key3: Kindacode.com, 
  key4: Buffalo, 
  key5: Blue, 
  key6: Green, 
  some_key: Hey Bro
}
```

## 使用 addAll() 方法

此方法可用于组合 2 个不同的地图。它将一个地图的所有键/值对添加到另一个地图。如果一个键已经存在，它的值将被覆盖。

**例子：**

```
// main.dart
Map<String, dynamic> map1 = {'key1': 'A', 'key2': 'B', 'key3': 'C'};
Map<String, dynamic> map2 = {'key3': '3', 'key4': '4', 'key5': '5'};

void main() {
  map1.addAll(map2);
  print(map1);
}
```

**输出：**

```
{
  key1: A, 
  key2: B, 
  key3: 3, // This value is overwritten
  key4: 4, 
  key5: 5
}
```

## 使用 addEntries() 方法

此方法用于将键/值对的集合添加到给定的映射。与**addAll()**方法一样，当一个键已经在映射中时，它的值将被覆盖。

**例子：**

```
// main.dart
Map<String, dynamic> map = {'key1': 'A', 'key2': 'B', 'key3': 'C'};

void main() {
  map.addEntries([
    MapEntry('key3', 'A new value'),
    MapEntry('key4', 'D'),
    MapEntry('key5', 'E'),
    MapEntry('key6', 'Kindacode.com')
  ]);
  print(map);
}
```

广告

**输出：**

```
{
  key1: A, 
  key2: B, 
  key3: A new value, 
  key4: D, 
  key5: E, 
  key6: Kindacode.com
}
```

## 结论

我们已经通过几种不同的方法向 Dart 中的给定地图添加新条目。在使用数据密集型应用程序时，这些技术可能对您非常有用。
