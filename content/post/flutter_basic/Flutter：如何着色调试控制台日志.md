---
title: "Flutter：如何着色调试控制台日志"
date: 2021-09-07T12:56:33+08:00
draft: true
---


![img](https://luckly007.oss-cn-beijing.aliyuncs.com/image/Screen-Shot-2021-08-26-at-16.10.30.jpg)



## 介绍

以不同颜色记录消息使使用 Flutter 修复错误和开发应用程序变得更加有趣和愉快。本文将向您展示如何在不使用任何第三方插件的情况下做到这一点。

## 概述

要在没有第三个包的调试控制台中输出彩色日志，我们需要使用**ANSI 转义码**和**dart:developer 中**的**log()**函数。

一些常用的**ANSI 转义码**：

```dart
Reset:   \x1B[0m
Black:   \x1B[30m
White:   \x1B[37m
Red:     \x1B[31m
Green:   \x1B[32m
Yellow:  \x1B[33m
Blue:    \x1B[34m
Cyan:    \x1B[36m
```

可以像这样将带有自定义颜色的文本记录到调试控制台：

```dart
import 'dart:developer' as developer;

/*...*/
developer.log('[ANSI color code][your text][ANSI reset code]');
```

您可能想知道为什么我们不使用**print()**函数。这里的原因是ANSI颜色代码在使用时被忽略（至少它不适用于VS Code）。

您可以在 Wikipedia 上的[这篇文章中](https://en.wikipedia.org/wiki/ANSI_escape_code)找到有关 ANSI 转义码的更多信息。

## **例子**

编码：

```dart
// main.dart
import 'dart:developer' as developer;

// Blue text
void logInfo(String msg) {
  developer.log('\x1B[34m$msg\x1B[0m');
}

// Green text
void logSuccess(String msg) {
  developer.log('\x1B[32m$msg\x1B[0m');
}

// Yellow text
void logWarning(String msg) {
  developer.log('\x1B[33m$msg\x1B[0m');
}

// Red text
void logError(String msg) {
  developer.log('\x1B[31m$msg\x1B[0m');
}

void main() {
  logInfo('Hello buddy');
  logSuccess('Welcome to KindaCode.com');
  logWarning('I am a bad man. Be careful');
  logError('Ops. We ran into some trouble');
}
```

结果：

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/image/Screen-Shot-2021-08-26-at-16.13.24.jpg)

## 结论



您已经学习了如何为调试控制台中的文本输出着色。
