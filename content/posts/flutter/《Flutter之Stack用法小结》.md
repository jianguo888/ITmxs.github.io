---
title: "《Flutter之Stack用法小结》"
subtitle: ""
date: 2021-07-31T21:14:43+08:00
lastmod: 2021-07-31T21:14:43+08:00
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



> 本文主要介绍记录一下如何使用 Flutter Stack Widget 来将文本跟图片中的某个特定点对齐。

<!--more-->





某个产品需求要求显示本月累计签到天数，于是设计给了开发如下一张图。

![-w420](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2020/02/15/15817377604093.jpg)

设计姐姐”好心”地将文案也写好了，只留了个格让开发填，够简单吧。但其实开发很头疼：”累计签到”那一行文案是特殊字体，所以并不能吐槽设计的切图方式完全不合理。但问题是对背景图”填空”容易引起适配问题，稍有不慎就如下图一样没对齐。

![-w314](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2020/02/15/15817384649043.jpg)

接下来看如何在 Flutter 中解决上述对齐问题。涉及到的几个要点：

- 图片缩放 - 我们知道图片适配不同大小屏幕大小
- 了解 `Image.fit` 属性 - 这个属性控制着图片如何缩放
- 了解 `Stack` + `Positioned` 的基本用法
- 了解 `Stack` + `Align` 的基本用法

# 图片缩放

我们知道图片适配不同大小屏幕时必然出现不同程度的缩放。Flutter 中使用 `Image` 控件显示图片，`Image.fit` 属性则用于控制图片缩放方式。`fit` 属性为枚举类型 `BoxFit`，最常用值的包括如下几种：

- `fill` - Fill the target box by distorting the source’s aspect ratio.
- `contain` - As large as possible while still containing the source entirely within the target box.
- `cover` - As small as possible while still covering the entire target box.

![box_fit_fill.png (300×90)](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2020/02/15/15817395834810.jpg)

![box_fit_contain.png (300×90)](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2020/02/15/15817396100662.jpg)

![box_fit_cover.png (300×90)](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2020/02/15/15817396361784.jpg)

先来看如何将背景图铺满 `Stack`。代码如下：

```
Stack(
  children: <Widget>[
    Image.asset(
      'images/sign_in/sign_in_head.webp',
    ),
  ]
)
```

不同参数时效果分别如下：

`Image.asset(width: null, fit: null)`
![-w595](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2020/02/15/15817527373819.jpg)

`Image.asset(width: double.infinity, fit: null)`
![-w579](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2020/02/15/15817530863297.jpg)

`Image.asset(width: double.infinity, fit: BoxFit.fill)`
![-w576](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2020/02/15/15817531751686.jpg)

`Image.asset(width: double.infinity, fit: BoxFit.cover)`
![-w571](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2020/02/15/15817532756950.jpg)

就这里的场景而言(图片高度无限制)，`BoxFit.cover` 和 `BoxFit.fill` 效果无区别。不过 `BoxFit.cover` 保证图片不会变形，是个更好的选择。

# 控件定位

[Stack 类](https://api.flutter.dev/flutter/widgets/Stack-class.html)用于对若干个控件以层叠方式布局。例如：

```
Stack(
  children: <Widget>[
    Container(
      width: 100,
      height: 100,
      color: Colors.red,
    ),
    Container(
      width: 90,
      height: 90,
      color: Colors.green,
    ),
    Container(
      width: 80,
      height: 80,
      color: Colors.blue,
    ),
  ],
)
```

![img](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2020/02/15/15817536932540.jpg)

`Stack` 控件的子节点要么是 `positioned` (`Positioned` 或 `Align` 控件) 要么是 `non-positioned`。`Stack` 控件的大小刚好包含所有的 `non-positioned` 子控件(这些控件默认位于 Stack 的左上角)。而 `positioned` 子节点的位置，则由其 top, right, bottom, left 属性来决定。

`Positioned` 和 `Align` 控件都能用于 `Stack` 控件定位和对齐。`Positioned` 以 top, right, bottom, left 属性来定位，这些属性分别用于指定控件到 Stack 各边框的距离；`Align` 以 `Alignment(x, y)` 属性来定位，这些属性分别用于指定控件水平方向和垂直方向的距离范围。

`Alignment` 的 x, y 属性规定如下：

- `Alignment(0.0, 0.0)` - 表示矩形中点
- `Alignment(-1.0, -1.0)` - 表示矩形左上角
- `Alignment(1.0, 1.0)` - 表示矩形右下角

关于 `Stack` 有一个小细节要注意。通过如下两个例子说明：

```
Scaffold(
  appBar: AppBar(title: Text('hello')),

  body: Stack(
    children: <Widget>[
      Align(
        alignment: Alignment(0, 0),
        child: Text('hello, stack'),
      )
    ],
  ),
```

这个例子中，`Text` 按预期居显示：

![-w337](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2020/02/15/15817549810555.jpg)

然而，当 Stack 外面嵌套一个 Column 或 ListView 之后，Align 似乎在垂直方向失去了定位的作用。

```
Scaffold(
  appBar: AppBar(title: Text('hello')),

  body: Column(
    children: <Widget>[
      Stack(
        children: <Widget>[
          Align(
            alignment: Alignment(0, 0),
            child: Text('hello, stack'),
          )
        ],
      ),
    ],
  ),
```

![-w324](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2020/02/15/15817551181468.jpg)

我们观察 Stack 外面嵌套一个 Column 或 ListView 之后控件树，截图如下：

![-w549](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2020/02/15/15817553643641.jpg)

所以这个现象不难理解：

- Stack 外面无嵌套时，它占满了父控件，所以 `Align` 生效
- Stack 外面嵌套 `Column` 或 `ListView` 时，Stack 大小未指定(最终以 `Text` 大小为准)，所以 `Align` 从视觉效果上看在垂直方向不起作用。

给 Stack 指定大小就能解决这个问题。这里使用 `SizedBox` 指定 Stack 高度为 300。

![-w551](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2020/02/15/15817557227844.jpg)

# 解决方案

## 方案一

方案一使用 `Stack` + `Positioned` 定位，代码如下：

```
  @override
  Widget build(BuildContext context) {
    // 计算屏幕宽度
    double width = MediaQuery.of(context).size.width;
    // 计算图片缩放比
    final scale = width / 375.0;
    return Stack(children: <Widget>[
      Image.asset(
        _signInHeadBg,
        width: double.infinity,
        fit: BoxFit.cover,
      ),
      // 使用 Positioned 定位，指定 top 和 right 边距
      Positioned(
        top: 113 * scale,
        right: 108 * scale,
        child: Text(
          '2',
          style: TextStyle(color: c_FFFFFFFF, fontSize: 12),
        ),
      ),
    ]);
}
```

注意这里的实现细节，即按图片缩放比 scale 来调整 top 和 right，保证不同大小屏幕上都能完美适配。

## 方案二

方案二使用 `Stack` + `Align` 定位，代码如下：

```
Widget build(BuildContext context) {
  double width = MediaQuery.of(context).size.width;
  return SizedBox(
    width: width,
    height: 798.0 / 634 * width,
    child: Stack(
      children: <Widget>[
        Image.asset(
          _signInHeadBg,
          width: double.infinity,
          fit: BoxFit.cover,
        ),
        Align(
          child: Text(
            '2',
            style: TextStyle(color: c_FFFFFFFF, fontSize: 12),
          ),
          alignment: Alignment(0.42, -0.51),
        ),
      ],
    ),
  );
```

注意这里的实现细节，即为 `Stack` 添加一个指定大小的 `SizeBox`(大小与图片显示的大小相同)，保证不同大小屏幕上都能完美适配。

![-w547](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2020/02/15/15817565491620.jpg)

如果不为 `Stack` 指定大小，则 `Align` 在垂直方向不生效。如下图：

![-w547](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2020/02/15/15817566311909.jpg)