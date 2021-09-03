---
title: "《Flutter中ShapeBorder使用总结》"
subtitle: ""
date: 2021-07-28T22:54:53+08:00
lastmod: 2021-07-28T22:54:53+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter"]
categories: ["Flutter"]

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



> 本文主要介绍

<!--more-->

## 简介

ShapeBorder 用于设置形状和轮廓，比如圆形，矩形，圆角矩形等。常用于 Container 中。

继承结构如下：

- ShapeBorder【abstract】
  - BeveledRectangleBorder
  - BoxBorder【abstract】
    - Border
    - BorderDirectional
  - CircleBorder
  - ContinuousRectangleBorder
  - RoundedRectangleBorder
  - StadiumBorder
  - InputBorder【abstract】
    - OutlineInputBorder
    - UnderlineInputBorder

其中 ShapeBorder、BoxBorder、InputBorder 是抽象父类。InputBorder 通常用于输入框相关的。

类的关系图

[![ShapeBorder子类继承关系图](https://luckly007.oss-cn-beijing.aliyuncs.com/img/border_directional_02.png)](https://blog.wangruofeng007.com/images/flutter_shape_border/ShapeBorder子类继承关系图.png)

## BeveledRectangleBorder

斜面圆角矩形

继承关系:

```
BeveledRectangleBorder > ShapeBorder
Widget _beveledRectangleBorder() {
  return Center(
    child: Container(
      width: 240,
      height: 120,
      margin: EdgeInsets.all(16),
      decoration: ShapeDecoration(
        image: DecorationImage(
          image: AssetImage('lib/assets/img_flutter.png'),
          fit: BoxFit.cover,
        ),
        shape: BeveledRectangleBorder(
          borderRadius: BorderRadius.circular(20),
          side: BorderSide(
            width: 2,
            color: Colors.blue,
            style: BorderStyle.solid,
          ),
        ),
      ),
    ),
  );
}
```

效果如下：

[![beveled_rectangle_border](https://luckly007.oss-cn-beijing.aliyuncs.com/img/border_directional_02.png)](https://blog.wangruofeng007.com/images/flutter_shape_border/beveled_rectangle_border.png)

## BoxBorder

BoxBorder主要掌管边线方面的事，自身是abstract，不能直接用

BoxBorder官方说明

> Base class for box borders that can paint as rectangles, circles, or rounded rectangles.

### Border

继承关系:

```
Border > BoxBorder > ShapeBorder
```

Border官方说明

> A border of a box, comprised of four sides: top, right, bottom, left.

```
Widget _border() {
  return Center(
    child: Container(
      margin: EdgeInsets.all(16),
      padding: EdgeInsets.all(16),
      decoration: ShapeDecoration(
        color: Colors.orange,
        shape: Border(
          top: BorderSide(width: 6.0, color: Colors.black12),
          left: BorderSide(width: 6.0, color: Colors.black12),
          right: BorderSide(width: 6.0, color: Colors.black26),
          bottom: BorderSide(width: 6.0, color: Colors.black26),
        ),
      ),
      child: Text(
        "Border",
        style: TextStyle(color: Colors.white, fontSize: 20),
      ),
    ),
  );
}
```

效果图：

[![border](https://luckly007.oss-cn-beijing.aliyuncs.com/img/border_directional_02.png)](https://blog.wangruofeng007.com/images/flutter_shape_border/border.png)

### BorderDirectional

继承关系:

```
BorderDirectional > BoxBorder > ShapeBorder
BorderDirectional` 通过 `top`,`bottom`,`start`,`end`分别控制上下左右的边线
边线对象`BorderSide
Widget _borderDirectional() {
  return Center(
    child: Container(
      width: 240,
      height: 120,
      margin: EdgeInsets.all(16),
      decoration: ShapeDecoration(
        image: DecorationImage(
          image: AssetImage('lib/assets/img_flutter.png'),
          fit: BoxFit.cover,
        ),
        shape: BorderDirectional(
          start: BorderSide(color: Colors.black, width: 15),
          end: BorderSide(color: Colors.black, width: 15),
          top: BorderSide(
            color: Colors.black,
          ),
          bottom: BorderSide(
            color: Colors.black,
          ),
        ),
      ),
    ),
  );
}
```

效果如下：

[![border_directional_01](https://luckly007.oss-cn-beijing.aliyuncs.com/img/border_directional_02.png)](https://blog.wangruofeng007.com/images/flutter_shape_border/border_directional_01.png)

只设置左右的`BorderSide`

```
Widget _borderDirectional2() {
  return Center(
    child: Container(
      width: 240,
      height: 120,
      margin: EdgeInsets.all(16),
      decoration: ShapeDecoration(
        image: DecorationImage(
          image: AssetImage('lib/assets/img_flutter.png'),
          fit: BoxFit.cover,
        ),
        shape: BorderDirectional(
          start: BorderSide(color: Colors.black, width: 15),
          end: BorderSide(color: Colors.black, width: 15),
        ),
      ),
    ),
  );
}
```

效果如下：

[![border_directional_02](https://luckly007.oss-cn-beijing.aliyuncs.com/img/border_directional_02.png)](https://blog.wangruofeng007.com/images/flutter_shape_border/border_directional_02.png)

## CircleBorder

圆形边框。

继承关系:

```
CircleBorder > ShapeBorder
Widget _circleBorder1() {
  return Center(
    child: Container(
      width: 120,
      height: 120,
      margin: EdgeInsets.all(16),
      decoration: ShapeDecoration(
        image: DecorationImage(
          image: AssetImage('lib/assets/img_flutter.png'),
          fit: BoxFit.cover,
        ),
        shape: CircleBorder(
          side: BorderSide(),
        ),
      ),
    ),
  );
}
```

效果如下：

[![circle_border_01](https://luckly007.oss-cn-beijing.aliyuncs.com/img/border_directional_02.png)](https://blog.wangruofeng007.com/images/flutter_shape_border/circle_border_01.jpg)

上面的是使用默认参数的效果

[![circle_border_03](https://luckly007.oss-cn-beijing.aliyuncs.com/img/circle_border_01.jpg)](https://blog.wangruofeng007.com/images/flutter_shape_border/circle_border_03.png)

通过设置BorderSide来设置边框颜色和宽度，以及是否显示边框

```
Widget _circleBorder2() {
  return Center(
    child: Container(
      width: 120,
      height: 120,
      margin: EdgeInsets.all(16),
      decoration: ShapeDecoration(
        image: DecorationImage(
          image: AssetImage('lib/assets/img_flutter.png'),
          fit: BoxFit.cover,
        ),
        shape: CircleBorder(
          side: BorderSide(
            width: 10,
            color: Colors.blue,
            style: BorderStyle.solid,
          ),
        ),
      ),
    ),
  );
}
```

效果如下：

[![circle_border_02](https://luckly007.oss-cn-beijing.aliyuncs.com/img/circle_border_01.jpg)](https://blog.wangruofeng007.com/images/flutter_shape_border/circle_border_02.jpg)

## ContinuousRectangleBorder

平滑过渡的矩形边框

继承关系:

```
ContinuousRectangleBorder > ShapeBorder
Widget _continuousRectangleBorder() {
  return Center(
    child: Container(
      width: 240,
      height: 120,
      margin: EdgeInsets.all(16),
      decoration: ShapeDecoration(
        image: DecorationImage(
          image: AssetImage('lib/assets/img_flutter.png'),
          fit: BoxFit.cover,
        ),
        shape: ContinuousRectangleBorder(
          borderRadius: BorderRadius.circular(40),
          side: BorderSide(
            width: 2,
            color: Colors.blue,
            style: BorderStyle.solid,
          ),
        ),
      ),
    ),
  );
}
```

效果如下：

[![continuous_rectangle_border](https://luckly007.oss-cn-beijing.aliyuncs.com/img/circle_border_01.jpg)](https://blog.wangruofeng007.com/images/flutter_shape_border/continuous_rectangle_border.png)

## RoundedRectangleBorder

圆角矩形。

继承关系:

```
RoundedRectangleBorder > ShapeBorder
Widget _roundedRectangleBorder() {
  return Center(
    child: Container(
      width: 240,
      height: 120,
      margin: EdgeInsets.all(16),
      decoration: ShapeDecoration(
        image: DecorationImage(
          image: AssetImage('lib/assets/img_flutter.png'),
          fit: BoxFit.cover,
        ),
        shape: RoundedRectangleBorder(
          borderRadius: BorderRadius.circular(20),
          side: BorderSide(
            width: 2,
            color: Colors.blue,
            style: BorderStyle.solid,
          ),
        ),
      ),
    ),
  );
}
```

效果如下：

[![rounded_rectangle_border_01](https://luckly007.oss-cn-beijing.aliyuncs.com/img/circle_border_01.jpg)](https://blog.wangruofeng007.com/images/flutter_shape_border/rounded_rectangle_border_01.png)

## StadiumBorder

体育场形状。即两边是半圆。

继承关系:

```
StadiumBorder > ShapeBorder
Widget _stadiumBorder() {
  return Center(
    child: Container(
      width: 240,
      height: 120,
      margin: EdgeInsets.all(16),
      decoration: ShapeDecoration(
        image: DecorationImage(
          image: AssetImage('lib/assets/img_flutter.png'),
          fit: BoxFit.cover,
        ),
        shape: StadiumBorder(
          side: BorderSide(
            width: 2,
            color: Colors.blue,
            style: BorderStyle.solid,
          ),
        ),
      ),
    ),
  );
}
```

效果如下：

[![stadiu_border_01](https://luckly007.oss-cn-beijing.aliyuncs.com/img/circle_border_01.jpg)](https://blog.wangruofeng007.com/images/flutter_shape_border/stadiu_border_01.png)

## InputBorder

继承关系:

```
InputBorder > ShapeBorder
```

官方说明：

> Defines the appearance of an [InputDecorator]’s border.
>
> An input decorator’s border is specified by [InputDecoration.border].
>
> The border is drawn relative to the input decorator’s “container” which
>
> is the optionally filled area above the decorator’s helper, error,and counter.

常用的输入边框，有2个衍生子类`OutlineInputBorder` 和`UnderlineInputBorder`

### OutlineInputBorder

继承关系:

```
OutlineInputBorder > InputBorder > ShapeBorder
Widget _outlineInputBorder() {
  return Center(
    child: Container(
      margin: EdgeInsets.all(16),
      padding: EdgeInsets.all(16),
      decoration: ShapeDecoration(
        color: Colors.orange,
        shape: OutlineInputBorder(
          borderSide: BorderSide(width: 2.0, color: Colors.purple),
          borderRadius: BorderRadius.circular(20.0),
        ),
      ),
      child: Text(
        "OutlineInputBorder",
        style: TextStyle(color: Colors.white, fontSize: 20),
      ),
    ),
  );
}
```

效果如下：

[![outlineInput_borde](https://luckly007.oss-cn-beijing.aliyuncs.com/img/circle_border_01.jpg)](https://blog.wangruofeng007.com/images/flutter_shape_border/outlineInput_borde.png)

### UnderlineInputBorder

继承关系:

```
UnderlineInputBorder > InputBorder > ShapeBorder
Widget _underlineInputBorder() {
  return Center(
    child: Container(
      margin: EdgeInsets.all(16),
      padding: EdgeInsets.all(16),
      decoration: ShapeDecoration(
        color: Colors.orange,
        shape: UnderlineInputBorder(
          borderSide: BorderSide(width: 2.0, color: Colors.purple),
          borderRadius: BorderRadius.circular(20.0),
        ),
      ),
      child: Text(
        "UnderlineInputBorder",
        style: TextStyle(color: Colors.white, fontSize: 20),
      ),
    ),
  );
}
```

效果如下：

[![underlineInput_border](https://luckly007.oss-cn-beijing.aliyuncs.com/img/border_directional_01.png)](https://blog.wangruofeng007.com/images/flutter_shape_border/underlineInput_border.png)