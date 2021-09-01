---
title: "Dart中的Getter和Setter方法"
date: 2021-09-01T16:45:46+08:00
draft: true
---

getter 和 setter 方法是用于操作类字段数据的类方法。getter 用于读取或获取类字段的数据，而 setter 用于将类字段的数据设置为某个变量。

### Dart 中的 Getter 方法

它用于检索特定的类字段并将其保存在变量中。所有类都有一个默认的 getter 方法，但它可以被显式覆盖。getter 方法可以使用**get 关键字**定义为：

```dart
return_type获取字段名称{
    ...
}
return_type get field_name{
    ...
}
```

需要注意的是，在上面的方法中，我们必须定义一个返回类型，但不需要定义参数。

### Dart 中的 Setter 方法

它用于设置从 getter 方法接收到的变量内的数据。所有类都有一个默认的 setter 方法，但它可以被显式覆盖。setter 方法可以使用**set 关键字**定义为：

```dart
设置字段名称{
    ...
}
set field_name{
    ...
}
```

**示例：**在 dart 程序中使用 Getter 和 Setter 方法。

```dart
// Creating Class named Gfg
class Gfg {
	
// Creating a field
String geekName;
	
// Using the getter
// method to take input
String get geek_name {
	return geekName;
}
	
// Using the setter method
// to set the input
set geek_name (String name) {
	this.geekName = name;
}
}

void main()
{
	// Creating Instance of class
	Gfg geek = new Gfg();
	
// Calling the geek_name
// getter method to get the name
	geek.geek_name = "GeeksForGeeks";
	
// Printing the input
// taken through getter method
print("Welcome to ${geek.geek_name}");
}

```

