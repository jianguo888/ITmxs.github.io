---
title: "Flutter 从列表获取最接近的时间戳"
date: 2021-08-30T21:40:38+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---

> 本文主要介绍

<!--more-->

如何从列表中获取最接近的时间戳？
我得到了一个时间戳列表，我想确定将来与当前时间戳最接近的时间戳。
我该如何实现？

**最佳答案**

像这样吗我不确定您是如何表示时间戳的，所以我通过使用`DateTime`对象进行了示例:

```
void main() {
  final dateTimes = <DateTime>[
    DateTime(2020, 8, 1),
    DateTime(2020, 8, 5),
    DateTime(2020, 7, 13),
    DateTime(2020, 7, 18),
    DateTime(2020, 8, 15),
    DateTime(2020, 8, 20)
  ];
  final now = DateTime(2020, 7, 14);
  
  final closetsDateTimeToNow = dateTimes.reduce(
      (a, b) => a.difference(now).abs() < b.difference(now).abs() ? a : b);

  print(closetsDateTimeToNow); // 2020-07-13 00:00:00.000
}
```

请注意，该解决方案会在列表中找到壁橱时间戳，并查看过去和将来。