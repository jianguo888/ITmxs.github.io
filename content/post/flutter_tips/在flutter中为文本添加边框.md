---
title: "在flutter中为文本添加边框"
date: 2021-10-05T13:29:21+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---

> 下面的示例向您展示了如何在 Flutter 中为文本添加笔触（或边框）。

**截屏：**

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/img/Screen-Shot-2021-10-04-at-16.12.32.jpg)

**编码：**

```dart
Scaffold(
      appBar: AppBar(
        title: const Text('KindaCode.com'),
      ),
      body: Center(
          child: Stack(
        children: [
          // The text border
          Text(
            'Hi There',
            style: TextStyle(
              fontSize: 70,
              letterSpacing: 5,
              fontWeight: FontWeight.bold,
              foreground: Paint()
                ..style = PaintingStyle.stroke
                ..strokeWidth = 10
                ..color = Colors.red,
            ),
          ),
          // The text inside
          const Text(
            'Hi There',
            style: TextStyle(
              fontSize: 70,
              letterSpacing: 5,
              fontWeight: FontWeight.bold,
              color: Colors.amber,
            ),
          ),
        ],
      )),
    );
```