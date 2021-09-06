---
title: "在Flutter中给文本加下划线的完整指南（2021）"
date: 2021-09-06T16:46:36+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter","dart"]
categories: ["Flutter","dart"]
---

# 在 Flutter 中给文本加下划线的完整指南（2021）

这篇文章带你了解几个在 Flutter 中给 Text 加下划线的例子（附有说明和解释），从简单到高级，从简单到复杂。

## 示例 1：基本

该**文字样式**类有一个名为参数**的装饰**，可用于下划线添加到您的文本小。默认情况下，该行与文本具有相同的颜色。

**截屏：**

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/image/Screen-Shot-2021-04-08-at-17.55.22.jpg)

**编码：**

```dart
Column(
          children: [
            Text(
              'The Green Planet',
              style: TextStyle(
                  fontSize: 40,
                  color: Colors.green,
                  decoration: TextDecoration.underline),
            ),
            SizedBox(
              height: 30,
            ),
            Text(
              'Summer Time',
              style:
                  TextStyle(fontSize: 30, decoration: TextDecoration.underline),
            )
          ],
        ),
```

## 示例 2：下划线样式

您还可以使用 TextStyle 类提供的以下选项来控制下划线的外观：

- **装饰风格**：实心（默认）、虚线、虚线、波浪线、双线。
- **装饰**厚度：你的下划线有多粗，
- **DecorationColor** : 控制下划线的颜色，

**截屏：**

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/image/Screen-Shot-2021-04-08-at-18.01.17.jpg)

**编码：**

```dart
olumn(
          children: [
            Text(
              'Random Forest',
              style: TextStyle(
                  fontSize: 40,
                  color: Colors.green,
                  decoration: TextDecoration.underline,
                  decorationStyle: TextDecorationStyle.dashed,
                  decorationColor: Colors.orange,
                  decorationThickness: 3),
            ),
            SizedBox(
              height: 30,
            ),
            Text(
              'Deep Dream',
              style: TextStyle(
                fontSize: 45,
                color: Colors.purple,
                decoration: TextDecoration.underline,
                decorationStyle: TextDecorationStyle.wavy,
                decorationThickness: 2,
                decorationColor: Colors.red,
              ),
            )
          ],
        ),
```

## 示例 3：在文本和下划线之间添加一些空格

您可以通过添加阴影来控制文本与其下划线之间的间隙并使原始文本透明。阴影的**Y 偏移**将决定间隙。将其设置为负数有助于增加空间。



请注意，您在屏幕上看到的是文本的阴影，而不是实际文本。

**截屏：**

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/image/Screen-Shot-2021-04-08-at-18.51.46.jpg)

**编码：**

```dart
Text(
            'The Big Dog',
            style: TextStyle(
              fontSize: 40,
              color: Colors.transparent,
              fontWeight: FontWeight.bold,
              shadows: [
                Shadow(offset: Offset(0, -20), color: Colors.black)
              ],
              decoration: TextDecoration.underline,
              decorationStyle: TextDecorationStyle.dashed,
              decorationColor: Colors.green,
              decorationThickness: 4
            ),
          ),
```

## 示例 4：仅在段落中的几个单词下划线

您可以使用**RichText**和**TextSpan**仅在段落的某些部分加下划线。

**截屏：**

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/image/Screen-Shot-2021-04-08-at-18.30.39.jpg)



**编码：**

```dart
RichText(
            text: TextSpan(
                style: TextStyle(fontSize: 30, color: Colors.black),
                children: [
                  TextSpan(
                    text: 'He thrusts the post ',
                  ),
                  TextSpan(
                      text: 'and still insists',
                      style: TextStyle(
                          decoration: TextDecoration.underline,
                          color: Colors.blue)),
                  TextSpan(text: ' he see the ghost')
                ]),
          ),
```

## 结论

我们已经经历了几个不同的在 Flutter 中给文本加下划线的例子。
