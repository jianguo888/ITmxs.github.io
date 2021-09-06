---
title: "Dart和Flutter中的排序列表（5个示例）"
date: 2021-09-06T16:21:30+08:00
draft: true
---

在本文中，我们将介绍几个在 Dart（以及 Flutter）中排序列表的示例，这是您在绝大多数项目中可能必须处理的常见任务。示例按照从易到高级、从简单到复杂的顺序排列，涵盖了您在现实生活中可能遇到的大多数场景。

## 对数字列表进行排序

使用**sort()**方法可以帮助我们快速按升序或降序对数字列表进行排序。请注意，此方法不会返回新列表，而是操作原始列表。

**编码：**

```dart
void main() {
  List<double> myNumbers = [1, 2.5, 3, 4, -3, 5.4, 7];
  myNumbers.sort();
  print('Ascending order: $myNumbers');
  print('Descending order ${myNumbers.reversed}');
}
```

**输出：**

```dart
Ascending order: [-3.0, 1.0, 2.5, 3.0, 4.0, 5.4, 7.0]
Descending order (7.0, 5.4, 4.0, 3.0, 2.5, 1.0, -3.0)
```

## 对字符串列表进行排序

这个例子与第一个类似。我们使用**sort()**方法按字母顺序（从 A 到 Z 或从 Z 到 A）对列表的字符串元素进行排序。

**编码：**

```dart
void main() {
  List<String> myFruits = [
    'apple',
    'coconut',
    'banana',
    'strawberry',
    'water melon',
    'pear',
    'plum'
  ];
  myFruits.sort();
  print('Ascending order (A to Z): $myFruits');
  print('Descending order (Z to A): ${myFruits.reversed}');
}
```

**输出：**

```dart
Ascending order (A to Z): [apple, banana, coconut, pear, plum, strawberry, water melon]
Descending order (Z to A): (water melon, strawberry, plum, pear, coconut, banana, apple)
```

## 对map列表进行排序

对map列表进行排序有点复杂。我们需要使用**sort()**方法和**compareTo()**方法。



下面的小程序按照销售价格的升序和降序对虚拟产品列表进行排序。

编码：

```dart
void main() {
  List<Map<String, dynamic>> myProducts = [
    {"name": "Shoes", "price": 100},
    {"name": "Pants", "price": 50},
    {"name": "Book", "price": 10},
    {"name": "Lamp", "price": 40},
    {"name": "Fan", "price": 200}
  ];

  // Selling price from low to high
  myProducts.sort((a, b) => a["price"].compareTo(b["price"]));
  print('Low to hight in price: $myProducts');

  // Selling price from high to low
  myProducts.sort((a, b) => b["price"].compareTo(a["price"]));
  print('High to low in price: $myProducts');
}
```

输出：

```dart
Low to hight in price: [{name: Book, price: 10}, {name: Lamp, price: 40}, {name: Pants, price: 50}, {name: Shoes, price: 100}, {name: Fan, price: 200}]

High to low in price: [{name: Fan, price: 200}, {name: Shoes, price: 100}, {name: Pants, price: 50}, {name: Lamp, price: 40}, {name: Book, price: 10}]
```

## 对对象列表进行排序

类似于对地map列表进行排序，您可以使用**sort()**和**compareTo()**方法按属性值对对象列表进行**排序**。

假设您的应用获得了一些用户，并且您希望按年龄对他们进行排序。下面的程序会帮助你。



**编码：**

```dart
class User {
  String name;
  int age;

  User(this.name, this.age);

  @override
  toString() {
    return ('${this.name} - ${this.age} years old \n');
  }
}

void main() {
  List<User> users = [
    User('Plumber', 10),
    User('John Doe', 50),
    User('Pipi', 4),
    User('Invisible Man', 31),
    User('Electric Man', 34)
  ];

  // Age from small to large
  users.sort((a, b) => a.age.compareTo(b.age));
  print('Age ascending order: ${users.toString()}');
  

  // Age from large to small
  users.sort((a, b) => b.age.compareTo(a.age));
  print('Age descending order: ${users.toString()}');
}
```

**输出：**

```
Age ascending order: [Pipi - 4 years old 
, Plumber - 10 years old 
, Invisible Man - 31 years old 
, Electric Man - 34 years old 
, John Doe - 50 years old 
]
Age descending order: [John Doe - 50 years old 
, Electric Man - 34 years old 
, Invisible Man - 31 years old 
, Plumber - 10 years old 
, Pipi - 4 years old 
]
```

## 使用 Comparable 抽象类对列表进行排序

此示例**执行前一个示例的操作，**但使用了**不同的方法**。我们扩展**Comparable**抽象类并覆盖**compareTo()**方法。排序逻辑移到**User**类中。

**编码：**

```dart
class User extends Comparable {
  String name;
  int age;

  User(this.name, this.age);

  @override
  toString() {
    return ('${this.name} - ${this.age} years old \n');
  }

  @override
  int compareTo(other) {
    if (this.age < other.age) {
      return -1;
    }

    if (this.age > other.age) {
      return 1;
    }

    if (this.age == other.age) {
      return 0;
    }

    return null;
  }
}

void main() {
  List<User> users = [
    User('Plumber', 10),
    User('John Doe', 50),
    User('Pipi', 4),
    User('Invisible Man', 31),
    User('Electric Man', 34)
  ];

  // Age from small to large
  users.sort();
  print('Age ascending order: ${users.toString()}');
}
```

**输出：**

```dart
Age ascending order: [Pipi - 4 years old 
, Plumber - 10 years old 
, Invisible Man - 31 years old 
, Electric Man - 34 years old 
, John Doe - 50 years old 
```



这种方法的优点是您不必多次重写排序算法，但缺点是它有时会使您的代码更加混乱和冗余。

## 结论

我们已经学习了几个在 Dart 中对列表进行排序的示例，并且您已经学习了不止一种技术来完成工作。
