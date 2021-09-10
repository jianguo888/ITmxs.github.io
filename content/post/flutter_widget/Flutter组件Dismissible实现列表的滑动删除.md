---
title: "Flutter组件Dismissible实现列表的滑动删除"
date: 2021-09-01T08:44:27+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter","dart"]
categories: ["Flutter","dart"]
---

## 一、Dismissible

`Dismissible` 在某些列表型页面中非常实用，iOS 的操作中，手势滑动清除是非常常见的需求(Android Native 开发实际上推崇的是长按弹菜单)，但不可否认这种操作模式对用户而言是更加友好的。

```dart
  const Dismissible({
    @required Key key,
    @required this.child,
    this.background,
    this.secondaryBackground,
    this.confirmDismiss,
    this.onResize,
    this.onDismissed,
    this.direction = DismissDirection.horizontal,
    this.resizeDuration = const Duration(milliseconds: 300),
    this.dismissThresholds = const <DismissDirection, double>{},
    this.movementDuration = const Duration(milliseconds: 200),
    this.crossAxisEndOffset = 0.0,
    this.dragStartBehavior = DragStartBehavior.start,
  })
```

`Dismissible` Widget 对需要实现滑动清楚的 Widget 包装在 child 中即可

`onDismissed` 是操作完成后的回调

`confirmDismiss` 方法用于判断是否进行 dismiss，如果返回 true 则会执行 dismiss 操作并且会触发 onDismissed，否则不会触发任何操作，也不会执行 onDismissed

`confirmDismiss` 定义如下：

```dart
  /// Gives the app an opportunity to confirm or veto a pending dismissal.
  ///
  /// If the returned Future<bool> completes true, then this widget will be
  /// dismissed, otherwise it will be moved back to its original location.
  ///
  /// If the returned Future<bool> completes to false or null the [onResize]
  /// and [onDismissed] callbacks will not run.
  final ConfirmDismissCallback confirmDismiss;
```

二、在列表中实现 滑动清除 功能

```dart
    return ListView.builder(
      itemCount: _list.length,
      itemBuilder: (BuildContext context, int index) {
        final item = _list[index];
        return Dismissible(
          key: Key(item.toString()),
          // crossAxisEndOffset: 1.0,
          // secondaryBackground: Container(color: Colors.pink),
          dragStartBehavior: DragStartBehavior.down,
          direction: DismissDirection.endToStart,
          background: Container(color: Colors.red),
          child: ListTile(
            title: Text(item['title']),
            subtitle: Text(item['subTitle']),
          ),
          onDismissed: (direction) {
            setState(() {
              _list.removeAt(index);
              print(_list.length);
            });
            Scaffold.of(context).hideCurrentSnackBar();
            Scaffold.of(context).showSnackBar(SnackBar(
              content: Text('删除成功...'),
            ));
          },

        );
      },
    );
```

上面代码中，使用 `ListView.builder` 构建整个列表，然后将列表的每个子项包装在 `Dismissible` 中

`direction` 能够指定滑动的方向，默认是从 右往左(end to start)

然后 child 中传入 `ListTile` 这个真正的子项

在 onDismissed 中进行真正的删除操作



## 三、效果：

![GIF4.gif](https://luckly007.oss-cn-beijing.aliyuncs.com/image/590354847.gif)
