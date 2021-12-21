---
title: "《在Flutter中的开发小技巧2》"
subtitle: ""
date: 2021-06-24T21:41:33+08:00
lastmod: 2021-06-24T21:41:33+08:00
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

> 本文主要介绍flutter中的开发技巧2

<!--more-->

30 silder



![image-20210724215501245](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210724215501245.png)

29chip



![image-20210724215549320](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210724215549320.png)





28 aspect ratio



![image-20210724215635974](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210724215635974.png)



27placehold 占位符





![image-20210724215726217](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210724215726217.png)





26flutter localizations



![image-20210724215824244](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210724215824244.png)

25flutter toast

![image-20210724220020961](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210724220020961.png)

24

pubspec assist 插件

23

better comments 插件

22

future.delayed

![image-20210724220319028](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210724220319028.png)











21 theme mode









20 trans form







![image-20210724220436351](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210724220436351.png)









19gridview



![image-20210724220556306](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210724220556306.png)







18 spacer





17 divide







16 url lanucher

![image-20210724220706608](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210724220706608.png)



15 image_picker





![image-20210724220732753](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210724220732753.png)



![image-20210724221004889](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210724221004889.png)

14







material.io  https://material.io/



[https://material.io/](https://www.youtube.com/redirect?event=video_description&redir_token=QUFFLUhqbXFsbENJakpoZXNfNDk0Ri1INDhQbG81bktKUXxBQ3Jtc0trYmZESy1LZUN3N1RPbTl3RDRSTnVueXYtQ0VoU0FzR0xjSG83ZVJwVEJ6RnpKbXFuTEtleHhHcG9fRHZDRHdMM3VjaHhXQ2ZESUNld0hBSktQRjlOVEdZbGpDV0tRMUJOSzZXaUhEOWlqQUw2NGVZUQ&q=https%3A%2F%2Fmaterial.io%2F)















13



![image-20210724221239881](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210724221239881.png)



12





![image-20210724221311502](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210724221311502.png)



11 image preview







10 listwheel scrollview



![image-20210724221348671](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210724221348671.png)

9



pageview



8mouseregin









![image-20210724221845509](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210724221845509.png)









7opacity



6  



![image-20210724221932915](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210724221932915.png)









5

![image-20210724222012729](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210724222012729.png)



4

![image-20210724222213327](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210724222213327.png)













3





![image-20210724222239142](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210724222239142.png)











2![image-20210724222330515](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210724222330515.png)

![image-20210724222304026](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210724222304026.png)



![image-20210724222321284](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210724222321284.png)





1![image-20210724222354152](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210724222354152.png)



![image-20210724222445154](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210724222445154.png)





最佳实践是一个领域中可接受的专业标准，对于任何编程语言来说，提高代码质量、可读性、可维护性和健壮性都非常重要。

让我们探索一些设计和开发 Flutter 应用程序的最佳实践。

## 命名约定：

类、枚举、类型定义和扩展名应该在 `UpperCamelCase.`



库、包、目录和源文件名应该在 `snake_case(lowercase_with_underscores).`



变量、常量、参数和命名参数应该在 `lowerCamelCase.`











## 使用`?? `和`?.`操作符

更喜欢使用 ?? （如果为空）和？。（空感知）运算符而不是条件表达式中的空检查。



## 使用传播集合

当现有项目已经存储在另一个集合中时，展开集合语法会导致代码更简单。



## 使用级联运算符

如果我们想对同一个对象执行一系列操作，那么我们应该使用 Cascades(..) 运算符。


## 使用原始字符串

可以使用原始字符串来避免仅转义反斜杠和美元。



## 不要显式初始化变量 `null`

在 Dart 中，当未指定变量的值时，变量会自动初始化为 null，因此添加 null 是多余且不需要的。



## 使用表达式函数体

对于仅包含一个表达式的函数，您可以使用表达式函数。的`=>`（箭头）符号用于表达功能。



## 避免对非私有的本地标识符使用前导下划线。

Dart 在标识符中使用前导下划线 (_) 将成员和顶级声明标记为私有。没有概念。private 使用前导下划线 (_) 作为局部变量、参数、局部函数或库前缀。

## 使用插值组合字符串

使用插值使字符串更干净、更短，而不是使用长链`+`来构建字符串。



## **当撕下可以做的时候不要创建一个 lambda**

如果我们有一个函数调用一个方法，该方法的参数与传递给它的参数相同，则不需要手动将调用包装在 lambda 中。


## 使用`async`/`await

异步代码难以阅读和调试。该`async`/`await`语法提高了可读性。



## 将小部件拆分为子小部件。

当`setState()`在状态上调用时，所有后代小部件都将重建。因此，将小部件拆分为小部件，以便`setState()`调用实际需要更改 UI 的子树部分。



## 使用 ListView.builder 获取长列表

在处理无限列表或非常大的列表时，通常建议使用`ListView`构建器以提高性能。

默认`ListView`构造函数一次构建整个列表。`ListView.builder`创建一个惰性列表，当用户向下滚动列表时，Flutter 会按需构建小部件。

## 在小部件中使用 Const

当 setState 调用时小部件不会改变，我们应该将其定义为常量。它将阻止小部件重建，从而提高性能。



## 避免使用`as`，使用`is`运算符

通常，`as`如果无法进行强制转换，则cast 运算符会抛出异常。为了避免抛出异常，可以使用`is`.

## 使用`if`条件代替条件表达式

很多时候我们需要根据Row和Column中的一些条件来渲染一个widget。如果**条件表达式**在任何情况下都返回 null，那么我们应该只使用 if 条件。

## 对文件使用相对路径导入 `lib`

当同时使用相对导入和绝对导入时，当从两种不同的方式导入同一个类时，可能会造成混淆。为了避免这种情况，我们应该在`lib/`文件夹中使用相对路径。


## 指定类成员的类型

当成员的值类型已知时，总是明确指定成员的类型。`var`尽可能避免使用。

## 避免`print()`

`print()`和`debugPrint()`两个用于在控制台登录。如果您使用`print()`并且一次输出太多，那么Android有时会丢弃一些日志行。为避免这种情况，请使用`debugPrint().`如果您记录的数据有太多数据，则使用`dart:developer` `log().`这允许您在日志输出中包含更细的粒度和信息。

我希望这能给你一些见解，使你的 Flutter 代码更具可读性，同时也提高你的应用程序的性能。快乐编码！
