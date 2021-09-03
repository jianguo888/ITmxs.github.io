---
title: "《Dart之List相关》"
subtitle: ""
date: 2021-05-25T11:33:24+08:00
lastmod: 2021-05-25T11:33:24+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter","dart"]
categories: ["Flutter","dart"]

hiddenFromHomePage: false
hiddenFromSearch: false
twemoji: false

fontawesome: true
linkToMarkdown: true
rssFullText: false


featuredImage: "https://luckly007.oss-cn-beijing.aliyuncs.com/img/dart.jpg"
featuredImagePreview: "https://luckly007.oss-cn-beijing.aliyuncs.com/img/dart.jpg"

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



> 要说，List在我的开发使用中，确实是最为频繁的了，那么如何使用list，也就成了一个问题，list提供的方法又有哪些
>
> 这些都是需要掌握理解的。

<!--more-->

首先第一个，

## 对于固定长度的list，如何删除添加元素呢

```dart
void main() {
  // 声明一个固定长度的集合
  List<int> numList = List(5);

  numList[0] = 1;
  numList[1] = 2;
  numList[2] = 3;
  numList[3] = 4;
  numList[4] = 5;

  print('遍历元素：');
  for (int value in numList) {
    print(value);
  }

  print('----更新第一个元素为 10------');

  numList[0] = 10;

  print('遍历元素：');
  numList.forEach((value) => print(value));

  print('----将第一个元素置为NULL---');

  numList[0] = null;

  print('遍历元素：');
  for (int i = 0; i < numList.length; i++) {
    print('${numList[i]}');
  }
}

```

## 动态长度的 List

我们直接看示例：对于动态长度的list，

声明一：

```dart
void main() {
  // 动态长度的 集合
  List<int> list = List();

  list.add(1);
  list.add(2);

  list.forEach((value) => print('$value'));
  print('');
  
  list.removeAt(0);
  list.forEach((value) => print('$value'));
  print('');
  
  list.add(3);
  list[0] = null;
  list.forEach((value) => print('$value'));
}
```

声明二：

```dart
// 这样也是一个动态的集合
  List<String> letterList = ["A", "B", "C"];

  letterList.add("D");
  letterList.add("E");

  letterList.forEach((letter) => print('$letter'));


```



##### 循环遍历 List 中的数据

```
///代码清单 1-1
List<String> testList = ["test1", "xioming", "张三", "xioming", "张三", "李四"];

///方式一 遍历获取List中的所有数据
testList.forEach((value) {
  //value 就是List中对应的值
});

///方式二 遍历获取List中的所有的数据
for(int i=0;i<testList.length;i++){
  ///根据索引获取List中的数据
  var value = testList[i];
}

//方式三
//while+iterator迭代器遍历，类似Java中的iteator       
while(testList.iterator.moveNext()) {
  //获取对应的值
  var value = testList.iterator.current;
  
}

//方式四 增强for循环
//for-in遍历       
for (var value in testList) {
  //value 就是List中对应的值
}



```

##### List 数据转 Map 数据

```java
  ///代码清单 1-2
List<String> testList = ["test1", "xioming", "张三", "xioming", "张三", "李四"];
    print(testList); //[test1, xioming, 张三, xioming, 张三, 李四]
    //将list转为set,将后面重复的都去掉
    Map<int,String> map = testList.asMap();
    print(map); //{0: test1, 1: xioming, 2: 张三, 3: xioming, 4: 张三, 5: 李四}
```

##### 连接 List 中的数据

```java
    ///代码清单 1-3
List<String> testList = ["A", "D",  "F", "F","B", "C",];
    print(testList); //[A, D, F, F, B, C]

    ///用指定的字符将List中每个元素都连接起来，返回一个字符串
    /// 如这里将 List 中的数据 使用 , 号来连接
    String value = testList.join(",");

    print(value);///A,D,F,F,B,C

```

##### 合并 List 中的数据

通过 List 的 followedBy 方法，可以合并 多个 List中的数据至一个 Iterable中，如下代码所示：

```java
    ///代码清单 1-4
	///创建一个 List 并添加初始化数据
    List<int> list = [1,2,3,4];

    ///创建一个  List并添加初始化数据
    List<int> list2 = [2,3,4,5];

    ///将两个 List 中的数据合并成一个 Iterable
    Iterable<int> lsit3=list2.followedBy(list);
    print("list: "+list.toString());//list: [1, 2, 3, 4]
    print("list2: "+list2.toString());//list2: [2, 3, 4, 5]
    print("list3: "+lsit3.toString());//list3: (2, 3, 4, 5, 1, 2, 3, 4)
```

#####  随机排列 List中的数据顺序

```java
        ///代码清单 1-5
	List<String> testList = ["test1", "xioming", "张三", "xioming", "张三", "李四"];
    print(testList); //[test1, xioming, 张三, xioming, 张三, 李四]
    
    //将list中数据 重新随机排列
    testList.shuffle();
    print(testList); //[test1, xioming, xioming, 李四, 张三, 张三]

```

#####  升序排列 List 中的数据

```java
     ///代码清单 1-6
	List<String> testList = ["A", "D",  "F", "F","B", "C",];
    print(testList); //[A, D, F, F, B, C]

    ///升序排列
    testList.sort();
    print(testList);//[A, B, C, D, F, F]
```

##### 去重复

在 List 中保存的数据是允许重复的，通过 toSet 方法 将List转为Set ，可去除后面重复的元素，如下所示：

```java
     ///代码清单 1-7
	List<String> testList = ["test1", "xioming", "张三", "xioming", "张三", "李四"];
    print(testList); //[test1, xioming, 张三, xioming, 张三, 李四]
    //将list转为set,将后面重复的都去掉
    Set<String> set = testList.toSet();
    print(set); //{test1, xioming, 张三, 李四}
```

##### 查找 List 中的元素

```java
    List<String> list = ["test1", "xioming", "张三","xioming", "张三","李四"];

    ///从索引0处开始查找指定元素，返回指定元素的索引
    int index = list.indexOf("张三"); ///index 2
    ///
    ///从索引0处开始查找指定元素，如果存在返回元素索引，否则返回-1
    int index2 = list.indexOf("张三",3); ///  4
    ///  
    ///从后往前查找，返回查找到的第一个元素的索引
    int index4 = list.lastIndexOf("张三");/// 4
```

##### 获取 List 中指定位置的值

```java
    ///直接根据索引获取 0 号位置上的数据
    String  value = list[0];
    /// 等效于 elementAt 方法获取
    String value1 = list.elementAt(0);
```

#####  List 中数据修改

List中的数据修改操作方法就比较简单，如下代码清单 4-1所示：

```java
    ///代码清单 1-10
    List<String> list = ["test1", "xioming", "张三", "李四"];

    print("list: " + list.toString()); //list: [test1, xioming, 张三, 李四]

    ///修改数据
    list[0] = "测试数据";
    
    print("list: " + list.toString()); //list: [测试数据, xioming, 张三, 李四]

```

通过 setRange 方法批量修改一定范围的数据，如下代码清单 4-2 所示：

```java
 ///代码清单 1-11
    List<String> list = ["test1", "xioming", "张三", "李四"];

    print("list: " + list.toString()); //list: [test1, xioming, 张三, 李四]
    ///修改 索引范围为 [0,2) 也就是修改了 0号位与1号位上的元素
    list.setRange(0, 2, list2);

    print("list: " + list.toString()); //list: [223, 35, 张三, 李四]
```

##### List 中数据删除

删除 List中1个元素，如下代码清单3-1 所示：

```java
    ///代码清单 1-12
    ///创建一个 List 并添加初始化数据
    List<String> list = ["test1", "xioming", "张三", "李四"];

    ///移除最后一个元素
    list.removeLast();

    ///移除指定索引位置的元素 如这里指定的 2号位置
    list.removeAt(2);

    ///移除List 中指定的元素
    list.remove("张三");

```

通过 removeRange 方法可删除 List 中指定范围的多个元素，这个范围是连续的，如下代码清单3-2所示：

```java
    ///代码清单 1-13 
    ///创建一个 List 并添加初始化数据
    List<String> list = ["test1", "xioming", "张三", "李四"];

    print("list: " + list.toString()); //list: [test1, xioming, 张三, 李四]

    ///移除指定 索引区域的元素
    ///如这里移除索引范围为 [0,2) 也就是移除了 0号位与1号位上的元素
    list.removeRange(0, 2);


```

通过 removeWhere 方法可删除满足一定条件的元素数据，如下代码清单3-3所示:

```java
    ///代码清单 1-14
    ///创建一个 List 并添加初始化数据
    List<String> list = ["test1", "xioming", "张三", "李四"];

    print("list: " + list.toString()); //list: [test1, xioming, 张三, 李四]

    // 从List中中删除满足条件的所有对象。
    list.removeWhere((item){
      ///在这里 张三, 李四 的 length 分别为2 ，满足条件 所以会被删除
      return item.length == 2;
    });
    print("list: " + list.toString()); //list: [test1, xioming]

```

通过 clear 方法可清空 List，也就是删除List中的所有的数据。

```java
 list.clear();
```



## Set

**Set 是无序的；Set 是不允许添加重复元素；Set 不能使用下标来获取里面里面的元素，因为它是无序 的。**

```dart
void main() {
  // 方式一：
  Set<String> letterSet = Set.from(["A", "B", "C"]);
  letterSet.add("D");
  letterSet.add("E");
  // 重复的元素将被忽视
  letterSet.add("A");

  // 使用 for..in.. 遍历 Set
  for (String letter in letterSet) {
    print('$letter');
  }

  print('');
  
  // 方式二：
  Set<int> numSet = Set();
  numSet.add(0);
  numSet.add(1);
  numSet.add(2);

  // 使用 Lambda 遍历 Set
  numSet.forEach((value) => print('$value'));
}

```



## Map

Dart 中 Map 的特性和 Java 中的类似，都是以键值对的形式存放，Map 中的键是唯一的，但是值可以重复，Map 也是无序的。

下面看看 Dart 中 Map 的基本操作：

```dart
void main() {
  Map<String, String> letterMap = Map();
  letterMap["a"] = "A";
  letterMap["b"] = "B";
  letterMap["c"] = "C";

  // 检查是否存在某个 key
  letterMap.containsKey("a");
  // 更新某个 key 对应的 value，注意 value 为一个 Lambda 表达式
  letterMap.update("a", (value) => "${value}AA");

  // 获取 Map 的长度
  letterMap.length;
  // 删除 Map 中的某个元素
  letterMap.remove("c");
  // 清空 Map
  letterMap.clear();

  // 遍历所有的 key
  for (String key in letterMap.keys) {
    print('$key');
  }

  print('');

  // 遍历所有的 value
  for (String value in letterMap.values) {
    print('$value');
  }

  print('');

  // 遍历所有的 key-value
  letterMap.forEach((key, value) => print('$key == $value'));
}

```

上述代码中使用的是构造方法的方式来创建 Map，我们还可以使用一下方式来创建：

```dart
  Map<String, int> studentScoreMap = {
    "Jack": 90,
    "Rost": 100,
    "Mary": 30,
    "LiLei": 56
  };

  studentScoreMap.forEach((name, source) => print('$name == $source'));
```



## Callable

Callable 能让我们像调用方法一样调用某个类，不过我们在类中需要实现 `call` 方法：

```dart
void main() {
  var personOne = Person();
  var info = personOne("Jack", 18);
  print('$info');
}

class Person {
  // 实现 call 方法
  String call(String name, int age) {
    return "$name is $age years old";
  }
}

```

