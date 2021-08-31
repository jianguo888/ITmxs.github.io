---
title: "Dart 如何在flutter中反转文本"
date: 2021-08-31T12:50:10+08:00
draft: true
---

任何人都可以张贴一个简单的示例来反转放置在`Text`内的`Container`。

这是开始代码。

```
Widget _myWidget() {
  return Container(
    height: 100.0,
    color: Colors.orange,
    child: Center(child: Text("GO", style: TextStyle(fontSize: 70.0)),),
  );
}
```


上面的代码显示以下输出:

[![enter image description here](https://i.stack.imgur.com/RSnTF.png)](https://i.stack.imgur.com/RSnTF.png)

我希望它看起来像是:

[![enter image description here](https://i.stack.imgur.com/rPAAW.png)](https://i.stack.imgur.com/rPAAW.png)



**最佳答案**

这是这样做的代码

```
    import 'dart:math';
    class FlipedText extends StatelessWidget {
    @override
    Widget build(BuildContext context) {
      return Material(
        child: Transform(
          transform:Matrix4.rotationX(pi),
          alignment: Alignment.center,

          child: Container(
            height: 100.0,
            color: Colors.orange,
            child: Center(child: Text("GO", style: TextStyle(fontSize: 70.0)),),
          ),
        ),
      );
    }
  }
```
