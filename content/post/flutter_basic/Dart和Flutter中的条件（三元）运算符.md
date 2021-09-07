---
title: "Dart和Flutter中的条件（三元）运算符"
date: 2021-09-07T11:19:00+08:00
draft: true
---

# Dart 和 Flutter 中的条件（三元）运算符

本文深入探讨了 Dart 和 Flutter 中的条件（三元）运算符。



## 基础的

条件（三元）运算符是唯一采用三个操作数的 Dart 运算符：一个条件后跟一个问号 (?)，然后是一个如果条件为**真**则执行的表达式，后跟一个冒号 (:)，最后是要执行的表达式如果条件为**假**则**执行**。

**语法：**

```dart
condition ? exprIfTrue : exprIfFalse
```

在哪里：

- **条件**：一个表达式，其值用作条件。
- **exprIfTrue**：一个表达式，如果条件计算为**真**值（等于或可以转换为**真值**），则计算该表达式。
- **exprIfFalse**：如果条件为**假**则执行的表达式（即，具有可以转换为**false 的值**）。

**例子**

```dart
String message(bool isValid) {
  return isValid ? 'This is valid' : 'This is not valid';
}

void main() {
  print(message(true));
}
Center(
        child: isLogin ? Text('You are a member') : Text('Hello Guest'),
),
```

## 条件链

三元运算符是右结合的，这意味着它可以“链接”以依次检查多个条件。

**语法：**

```dart
condition1 ? value1
         : condition2 ? value2
         : condition3 ? value3
         : condition4 ? value4
         : condition5 ? value5 // and so on
         : valueN;
```

**例子：**

```dart
int age = 30;
String output = age < 1 ? 'Infrant'
      : age < 4 ? 'Baby' 
      : age < 12 ? 'Middle Childhood'
      : age < 18 ? 'Adolescence'
      : 'Grown Up';
print(output);
```

## 空检查表达式



此表达式使用双问号，可用于测试**null**。

**语法：**

```dart
expr1 ?? expr2
```

如果**expr1**非空，则返回其**值**；否则，计算并返回**expr2**的值。

**例子：**

```dart
// Flutter
Center(
        child: Text(name ?? 'No Name Found'),
),
```

## 三元运算符 vs If-else 语句

条件表达式**条件** **? expr1：expr2**的值为**expr1**或**expr2，**而**if-else**语句没有值。一个语句通常包含一个或多个表达式，但一个表达式不能**直接**包含一个语句。



有些事情你可以用三元运算符做，但不能用 if-else 做。例如，如果您需要初始化一个常量或引用：

```dart
const int number = (some conditions) ? 100 : 1;
```

## 编写简洁的代码

在绝大多数情况下，您可以使用三元运算符和 if-else 语句执行相同的操作。但是，使用**? :**帮助我们避免重复相同语句/函数调用的其他部分，例如：

```
if (someCondition)
    return x;
else
    return y;
```

与之比较：

```dart
return condition ? x : y;
```

## 结论

您已经学习了在 Dart 中编程时使用条件表达式的基础知识。
