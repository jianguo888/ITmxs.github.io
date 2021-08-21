---
title: "《Flutter开发Tip1》"
subtitle: ""
date: 2021-07-30T22:42:24+08:00
lastmod: 2021-07-30T22:42:24+08:00
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



> 本文主要介绍flutter开发小技巧

<!--more-->

## …

延展操作符(`...`)可以对数组或者字典进行操作

```
// 合并数组
test1() {
  var list = [1, 2, 3];
  var list2 = [0, ...list, 4];
  print(list2);
  // [0, 1, 2, 3, 4]
}

// 合并字典
test2() {
  var map1 = {
    "key1": "value1",
    "key2": "value2",
  };
  var map2 = {
    "key3": "value3",
    "key4": "value4",
  };
  var map3 = {...map1, ...map2};
  print(map3);
  // {key1: value1, key2: value2, key3: value3, key4: value4}
}
```

## for ()…[]

合并widgets到集合中，使用`for ()...[]`范式，使用延展操作符(`...`)来合并一个数组的widgets到一个存在的集合中。例如在构造Row或者Column的children时，非常方便。下面是示例：

```
List<Food> foods = [
  Food(name: 'apple', isVegetarian: true),
  Food(name: 'nuts', isVegetarian: true),
  Food(name: 'eggs', isVegetarian: true),
  Food(name: 'chicken', isVegetarian: false),
];

class Food {
  Food({
    this.name,
    this.isVegetarian,
  });

  String name;
  bool isVegetarian;
}

class HomePage extends StatelessWidget {
  const HomePage({Key key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Container(
      child: Column(
        children: [
          Container(),
          for (final food in foods) ...[
            if (food.isVegetarian) ListTile(title: Text(food.name)),
            SizedBox(height: 50.0),
          ],
        ],
      ),
    );
  }
```

## () {} ()

立即执行一个匿名函数：`() {} ()`,相当于声明一个匿名函数并且里面执行，这种范式在处理一个widget可能有多种输出时特别有用。而不是使用镶嵌的三目运算符或者通过一个函数调用，这个代码跟内联。下面是示例：

```
T getRandomElement<T>(List<T> list) => list[Random().nextInt(list.length)];

Column(
          children: [
            Container(
              color: () {
                switch (getRandomElement(foods).name) {
                  case 'apple':
                    return Colors.green;
                  case 'nuts':
                    return Colors.brown;
                  case 'eggs':
                    return Colors.yellow;
                  default:
                    return Colors.transparent;
                }
              }(),
              child: Text('Food of the Day'),
            ),
          ],
        )
```

![image-20210821192906170](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210821192906170.png)



![image-20210821192941146](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210821192941146.png)

![image-20210821193031177](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210821193031177.png)

![image-20210821193129279](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210821193129279.png)

![image-20210821193202829](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210821193202829.png)

![image-20210821193235686](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210821193235686.png)

![image-20210821193325476](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210821193325476.png)

![image-20210821193338140]( https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210821193338140.png)

![image-20210821193419896](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210821193419896.png)