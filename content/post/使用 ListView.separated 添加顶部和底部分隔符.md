+++
title="使用 ListView.separated 添加顶部和底部分隔符"
tags=["flutter"]
categories=["flutter"]
date="2019-12-13T21:00:00+08:00"
+++

[`ListView.separated`](https://api.flutter.dev/flutter/widgets/ListView/ListView.separated.html)是一个方便的 API，我们可以使用它在 Flutter [ListView](https://api.flutter.dev/flutter/widgets/ListView-class.html)内的项目之间添加分隔符。



> 分隔符仅出现在列表项之间：分隔符 0 出现在项 0 之后，最后一个分隔符出现在最后一项之前。

这意味着第一个项目上方和最后一个项目下方没有分隔符。

这在 iOS 上很明显，默认情况下列表可以过度滚动。

这里有一些代码可以直接设置，并为您的`ListView`s添加顶部和底部分隔符：

```dart
Container(
                  height: 400,
                  child: ListView.separated(
                    itemCount: 100 + 2,
                    separatorBuilder: (_, __) => Divider(height: 0.5),
                    itemBuilder: (context, index) {
                      if (index == 0 || index == 100 + 1) {
                        return Container(); // zero height: not visible
                      }
                      return Text("${index}");
                    },
                  ),
                )
```

