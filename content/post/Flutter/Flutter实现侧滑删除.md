---
title: "Flutter实现侧滑删除"
date: 2021-08-16T11:15:26+08:00
draft: true
---





```dart
Widget _infoItem(BuildContext context, int index) {
    Dynamics info = _moments[index];
    return Dismissible(
      onDismissed: (_) {
        //参数暂时没有用到，则用下划线表示
        _moments.removeAt(index);
      },
      movementDuration: Duration(milliseconds: 100),
      key: Key(info.activityId.toString()),
      child: Container(
        height: 60,
        child: Row(
          children: [
            Container(
              width: 16,
            ),
            ExtendedImage.network(
              info.userInfo.avatar,
              width: 80,
              height: 80,
            ),
            Text(
              info.userInfo.nickname,
              overflow: TextOverflow.ellipsis,
              style: TextStyle(),
            ),
           
          ],
        ),
      ),
    );
  }

```

