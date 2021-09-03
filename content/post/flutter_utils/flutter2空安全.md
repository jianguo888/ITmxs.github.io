---
title: "Flutter2空安全"
date: 2021-08-31T09:21:21+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter","dart"]
categories: ["Flutter","dart"]
---

作为开发人员，我们总是努力处理*NullPointerException*，这会在开发软件时导致大量时间和金钱。它被称为一个*十亿美元的错误。*（[维基](https://en.wikipedia.org/wiki/Tony_Hoare#Apologies_and_retractions)）

Flutter 2 支持空安全。您现在可以迁移 Flutter 包以使用不可为 null 的类型。按照以下链接为旧项目执行颤振迁移。

https://dart.dev/null-safety/migration-guide

**注意：**要使用它，必须使用空安全功能更新所有库，否则您无法将项目迁移到 Flutter 2。

Dart 将可空引用和不可空引用作为其类型系统的一部分进行区分，这有助于消除*NullPointerException*的风险*。*

在 Dart 中，默认情况下所有变量都是不可为空的。我们不能为变量分配*空*值，因为它会引发编译错误：

```
final String country = null; // Cannot be null.
  final String? country = null; // Can be null.
```

空安全是一项重要的新生产力功能，可帮助您避免空异常，这是一类通常难以发现的错误。作为额外的好处，此功能还可以实现**一系列性能改进。**

***— — — — — — — — — — — — — — — — — — — — — — — — — — —\***

# 可空类型的编译时错误。

默认情况下，变量是不可为空的，这意味着每个变量都应该被赋值并且不能为空。

```
void main() {
  String country;
  print(country);
}
Error: The non-nullable local variable 'country' must be assigned before it can be used.
```

**必须在声明时分配值，否则我们必须使用 '?' 将其标记为可空。**此外，无法为变量分配 null 值，它会给出错误**“不能将类型为 'Null' 的国家分配给类型为 'String' 的变量。”**

```
void main() { 
  String country = null; 
}
```

下面的代码将起作用，因为在**赋值**之前**不使用变量 country**。

```
void main() {
  String country;
  country = "Welcome To Flutter";
}
```

下面的代码给了我们一个错误，因为我们在变量被赋值之前就使用了它，并且抛出**“不可为空的局部变量‘值’必须在使用前赋值”**的错误**。**

```
void main() {
  String country;
  print(country);
  country = "USA";
}
```

***— — — — — — — — — — — — — — — — — — — — — — — — — — —\***

# 可空类型（使用问号“?”）

要强制变量可以为空，请使用**'?'** 如下所示，它告诉系统它可以为空，并分配字母。这将通过空异常导致移动设备上出现红屏。

```
void main() {
  String? country;
  print(country);
}
Output:
null
```

***— — — — — — — — — — — — — — — — — — — — — — — — — — —\***

# **不可空类型 - 感叹号 (**`!)`

附加`**!**`到任何变量告诉编译器该变量不为空，并且可以安全使用。

```
void main() {
  String? country = "USA";
  String myCountry = country!; // myCountry is non-nullable; would throw an error if country is null
```

***— — — — — — — — — — — — — — — — — — — — — — — — — — —\***

# `**late** variables`

该关键字`late`可用于标记**稍后**将被**初始化的**变量，即不是在声明时而是在访问时进行**初始化**。**这也意味着我们可以拥有稍后初始化的不可为空的\*实例字段\*：在初始化**`value`之前访问将引发运行时错误。

```
void main() {
    late String country; // non-nullable
    // print(value) here would throw a runtime error
    country = "USA";
}
```

***— — — — — — — — — — — — — — — — — — — — — — — — — — —\***

# `late final`

哇！！，这些是不是很神奇，Final 变量可以赋值一个字母。

可以在`late final`没有初始化器的情况下声明 a ，这与只有一个`late`变量相同，但它只能分配一次。

```
late final String country;
country = "USA"; 
print(country); // Working
country = "India";//Error: The late final local variable is already assigned.
```

如果后期 final 被声明为静态，那么它将在运行时进行评估。

```
class MyClass {
  static late final country;
}

void main() {
  MyClass.country = "USA";
  MyClass.country = "USA";
  print(MyClass.values.toString());
}
Output:
[VERBOSE-2:ui_dart_state.cc(186)] Unhandled Exception: LateInitializationError: Field 'values' has already been initialized.
```

这个错误将在运行时抛出，而不是在编译时抛出，所以不要再次声明最终的后期值，这会导致运行时错误。

***— — — — — — — — — — — — — — — — — — — — — — — — — — —\***

# **条件运算符(** `**?**`**)**

级联运算符帮助我们验证给定的对象/变量是否为 null 如果它为 null 那么我们可以使用另一个值，它更像是 if, else

```
class MyClass {
  String? country;
}

void main() {
   var myClass = MyClass();
   print(myClass.country?? "Japan");
/// myClass.value is returning null, to avoid null exception we can /// pass placeholder value like above("Japan"), this must be  
/// utilized whenever possible inside the code base. 
}
Output: Japan
```

我尝试将所有内容都放在类文件下，分别为其编写测试用例。

**例子：**

```
import 'package:flutter/material.dart';

class NullSafety {
  int? nullCheckForCountry() {
    String country = "USA";
    // country = null; //compilation error
    return country != null ? country.length : null;
  }

  int? safeCallForCountry() {
    String? country;
    return country?.length;
  }

  int? nullCheckForCity() {
    String? city = "Kolkata";
    city = null;
    return city != null ? city.length : null;
  }

  int? safeCallForCity() {
    String? city;
    return city?.length;
  }

  String? safeCallChainForValue() {
    Country? country = Country(City("Kolkata", "003"));
    return country.city?.code;
  }

  List<String?> safeCallUsingList() {
    List<String?> cities = ["Kolkata", null, "Mumbai"];
    List<String?> name = [];
    for (int i = 0; i < cities.length; i++) {
      String? city = cities[i];
      name.add(city);
    }
    return name;
  }

  String getDefaultValueIfObjectNull() {
    Country? country = Country(City("New Delhi", null));
    return country.city?.code ?? "Not available";
  }

  int? notNullAssertionForException() {
    String? country;
    return country!.length;
  }
}

class Country {
  City? city;

  Country(City? city) {
    this.city = city;
  }
}

class City {
  String? name;
  String? code;

  City(String? name, String? code) {
    this.name = name;
    this.code = code;
  }
}
```

**类文件：** [https ](https://github.com/jitsm555/Null-Safety-Flutter/blob/master/lib/main.dart)**:** [//github.com/jitsm555/Null-Safety-Flutter/blob/master/lib/main.dart](https://github.com/jitsm555/Null-Safety-Flutter/blob/master/lib/main.dart)

**测试文件：** [https ](https://github.com/jitsm555/Null-Safety-Flutter/blob/master/test/null_safety_test.dart)**:** [//github.com/jitsm555/Null-Safety-Flutter/blob/master/test/null_safety_test.dart](https://github.com/jitsm555/Null-Safety-Flutter/blob/master/test/null_safety_test.dart)
