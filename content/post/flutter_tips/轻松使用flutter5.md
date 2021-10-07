---
title: "Dart & Flutter 开发技巧 29-35"
date: 2021-08-08T19:25:26+08:00
draft: false
tags: ["Flutter"]
categories: ["Flutter"]

---

# Dart & Flutter 开发技巧 29-35

## 29. 当扩展类时，所有抽象方法必须被覆盖，其他方法可以被覆盖。在实现类时，必须覆盖所有方法。

![扩展类时，必须覆盖所有抽象方法，并且可以覆盖其他方法。 在实现类时，必须覆盖所有方法。](https://luckly007.oss-cn-beijing.aliyuncs.com/img/029-extends-vs-implements.png)

## 30. 需要以空安全的方式遍历map吗？使用`.entries`：

![需要以空安全的方式遍历地图吗？ 使用 .entries](https://luckly007.oss-cn-beijing.aliyuncs.com/img/030-map-entries.png)

## 31. 需要针对 Web 和Native平台的不同实现吗？使用条件导入。

更多信息：[有条件地导入和导出库文件](https://dart.dev/guides/libraries/create-library-packages#conditionally-importing-and-exporting-library-files)

![需要针对 Web 和本机平台的不同实现？ 使用条件导入。](https://luckly007.oss-cn-beijing.aliyuncs.com/img/031-conditional-imports.png)

## 32.需要用泛型声明一个函数类型吗？使用类型定义：

![需要用泛型声明一个函数类型吗？ 使用类型定义](https://luckly007.oss-cn-beijing.aliyuncs.com/img/032-typedef-function-builder.png)

## 33. 需要使用*计算*变量？使用 getter 和 setter。

![需要使用<em>计算</em>变量？ 使用 getter 和 setter](https://luckly007.oss-cn-beijing.aliyuncs.com/img/033-getters-setters.png)

## 34. 需要返回一个立即返回的 Future 吗？使用`Future.value`。

![需要返回一个立即返回的 Future 吗？ 使用 Future.value](https://luckly007.oss-cn-beijing.aliyuncs.com/img/034-future-value.png)

## 35. 想要在执行某些代码之前强制延迟？使用`Future.delayed`。

![想要在执行某些代码之前强制延迟吗？ 使用 Future.delayed](https://codewithandrea.com/tips/dart-flutter-easy-wins-29-35/images/035-future-delayed.png)