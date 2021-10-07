---
title: "Flutter中的玻璃态"
date: 2021-10-06T11:25:42+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---

> 本文主要介绍2021年《设计》中的一个倾向是玻璃变形，它赋予你的物体玻璃的感觉。

![image-20211006112823723](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20211006112823723.png)



在这里，我将展示如何使用这个在飘动，你看它很容易。

在此之后，您将能够有一个小部件，您将能够在任何其他小部件中快速。我们走吧。

在您的项目中创建dart件。在这里让我们称之为 "玻璃态"



![image-20211006113556929](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20211006113556929.png)

我们需要改变入口的dart：ui and flutter/material.dart。

```dart

import 'dart:ui';
import 'package:flutter/material.dart';
```

现在我们class：

```dart
import 'dart:ui';
import 'package:flutter/material.dart';

class GlassMorphism extends StatelessWidget {
  final Widget child;
  final double start;
  final double end;
  const GlassMorphism({
    Key? key,
    required this.child,
    required this.start,
    required this.end,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return ClipRRect(
      child: BackdropFilter(
        filter: ImageFilter.blur(sigmaX: 3, sigmaY: 3),
        child: Container(
          decoration: BoxDecoration(
            gradient: LinearGradient(
              colors: [
                Colors.white.withOpacity(start),
                Colors.white.withOpacity(end),
              ],
              begin: AlignmentDirectional.topStart,
              end: AlignmentDirectional.bottomEnd,
            ),
            borderRadius: BorderRadius.all(Radius.circular(10)),
            border: Border.all(
              width: 1.5,
              color: Colors.white.withOpacity(0.2),
            ),
          ),
          child: child,
        ),
      ),
    );
  }
}
```



正如你所看到的，它很简单。

我使用剪辑反应来控制效果只是在小部件。

To make the glass sensation, we use the BackdropFilter.

filter: ImageFilter.blur(sigmaX: 3, sigmaY: 3)我不建议改变 values。但你可以给任何 values来获得你的结果。

现在在容器内，我们使用带线性渐变的盒装装饰。

线性渐变接收"颜色："，其中您使用颜色列表，所以它需要在 [] 之间

使用"Colors.white.withOpacity(0.6), Colors.white.withOpacity(0.3)"将给玻璃的感觉。

在我的class里， 我把它们交给了 "开始" 和 "结束"， 因为你可以如愿改变values 。

```dart
child: Container(
          decoration: BoxDecoration(
            gradient: LinearGradient(
              colors: [
                Colors.white.withOpacity(start),
                Colors.white.withOpacity(end),
              ],
              begin: AlignmentDirectional.topStart,
              end: AlignmentDirectional.bottomEnd,
            ),
```

在线性梯度内，您必须设置方向。与 "开始" 和 "结束" 。使用分配定向设置。

添加边框也白色。

```dart
borderRadius: BorderRadius.all(Radius.circular(10)),
            border: Border.all(
              width: 1.5,
              color: Colors.white.withOpacity(0.2),
            ),
```

Finally pass the child.

child: child,



![image-20211006113851604](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20211006113851604.png)

save and you have your editable GlassMorphism Widget to use as you like:



![image-20211006113905736](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20211006113905736.png)

In a DialogBox

![image-20211006113519927](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20211006113519927.png)

In a Image

And a example of using the widget:

```dart
Form(
          key: _form,
          child: GlassMorphism(
            start: 0.9,
            end: 0.6,
            child: Column(
              mainAxisSize: MainAxisSize.min,
              children: [
                Padding(
                  padding: EdgeInsets.all(8.0),
                  child: TextFormField(
                    controller: obsController,
                    decoration: const InputDecoration(
                        labelText: 'Observações do pedido',
                        border: InputBorder.none),
                    keyboardType: TextInputType.multiline,
                    minLines: 3,
                    maxLines: 5,
                  ),
                ),
```

希望大家喜欢，并在您的项目中使用这些提示。