---
title: "在dart和flutter中使用静态方法"
date: 2021-10-05T13:25:12+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---

> 本文主要介绍**在 Dart 和 Flutter 中使用静态方法**

在 Dart 和 Flutter 中，静态方法（或静态函数）是定义为类的成员但无需通过构造函数创建对象实例即可直接调用的方法。换句话说，静态方法是类的端口，而不是特定实例的一部分。

**句法：**

```dart
class MyClass {
  static void method1(){
     /* ... */
  }
 
  static String method2(){
    /*...*/
    return "Some string";
  }
}

// Call static methods
void main(){
  MyClass.method1();
  String s = MyClass.method2();
}
```

**例子：**

```dart
// main.dart
class ExampleClass {
  static void sayHello() {
    print("Hello buddy");
  }

  static int sum(int input1, int input2) {
    return input1 + input2;
  }
}

void main() {
  ExampleClass.sayHello();

  final int sum = ExampleClass.sum(10, 11);
  print(sum);
}
```

输出：

```dart
Hello buddy
21
```