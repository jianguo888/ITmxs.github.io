---
title: "《Flutter中factory的使用总结》"
subtitle: ""
date: 2021-07-30T22:53:26+08:00
lastmod: 2021-07-30T22:53:26+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["flutter"]
categories: ["flutter"]

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



> 本文主要介绍

<!--more-->

## 0x01官方描述

> Use the `factory` keyword when implementing a constructor that doesn’t always create a new instance of its class. For example, a factory constructor might return an instance from a cache, or it might return an instance of a subtype.
>
> 当实现并非总是创建其类的新实例的构造函数时，请使用“ factory”关键字。例如，工厂构造函数可能会从缓存返回一个实例，或者可能会返回一个子类型的实例。

## 0x02使用场景

1. 避免创建过多的重复实例，如果该实例已创建，则直接从缓存获取。
2. 调用子类的构造函数(工厂模式 factory pattern）
3. 实现单例模式(singleton pattern)

## 0x03 Demo

### 创建缓存实例

> 引用自 https://dart.dev/guides/language/language-tour#factory-constructors

```
class Logger {
  final String name;
  // 缓存Map
  static final Map<String, Logger> _cache = <String, Logger>{};

  factory Logger(String name) {
    // name对应的key在_cache不存在，执行Logger._internal(name)，否则直接返回
    return _cache.putIfAbsent(name, () => Logger._internal(name));
  }

  // 私有构造函数
  Logger._internal(this.name) {
    print('生成新实例:$name');
  }
}

void main(List<String> args) {
  var p1 = Logger('1');
  var p2 = Logger('2');
  var p3 = Logger('1');
  print(identical(p1, p2));
  print(identical(p1, p3));
  // 生成新实例:1
  // 生成新实例:2
  // false
  // true
}
```

### 调用子类的构造函数

```
abstract class Animal extends Object {
  String name;
  void getNoise();
  factory Animal(String type, String name) {
    switch (type) {
      case 'cat':
        return Cat(name);
        break;
      case 'dog':
        return Dog(name);
        break;
      default:
        throw "The '$type' is not an animal";
    }
  }
}

class Cat implements Animal {
  String name;
  Cat(this.name);
  @override
  void getNoise() {
    print('${this.name}:mew~');
  }
}

class Dog implements Animal {
  String name;
  Dog(this.name);
  @override
  void getNoise() {
    print('${this.name}:wang~');
  }
}

void main(List<String> args) {
  var cat = Animal('cat', 'haha');
  var dog = Animal('dog', 'hehe');
  cat.getNoise();
  dog.getNoise();
  // haha:mew~
  // hehe:wang~
}
```

### 实现单例模式

```
class Singleton {
  static final Singleton _singleton = Singleton._internal();

  factory Singleton() {
    return _singleton;
  }

  Singleton._internal();
}

void main(List<String> args) {
  var s1 = Singleton();
  var s2 = Singleton();
  print(identical(s1, s2));
  // true
}
```