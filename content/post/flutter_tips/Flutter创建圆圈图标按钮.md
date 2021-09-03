---
title: "Flutter创建圆圈图标按钮"
date: 2021-09-03T15:49:33+08:00
draft: true
---

我找不到任何显示如何创建`IconButton`类似于的圆的示例`FloatingActionButton`。任何人都可以建议创建一个自定义按钮的方式/需要什么`FloatingActionButton`吗？


我认为RawMaterialButton更适合。

```dart
RawMaterialButton(
  onPressed: () {},
  elevation: 2.0,
  fillColor: Colors.white,
  child: Icon(
    Icons.pause,
    size: 35.0,
  ),
  padding: EdgeInsets.all(15.0),
  shape: CircleBorder(),
)
```


您可以尝试一下，它是完全可定制的。

```dart
ClipOval(
  child: Material(
    color: Colors.blue, // button color
    child: InkWell(
      splashColor: Colors.red, // inkwell color
      child: SizedBox(width: 56, height: 56, child: Icon(Icons.menu)),
      onTap: () {},
    ),
  ),
)
```

------

**输出：**

[![在此处输入图片说明](https://i.stack.imgur.com/yRtvq.gif)](https://i.stack.imgur.com/yRtvq.gif)



您只需要使用形状： `CircleBorder()`

```dart
MaterialButton(
  onPressed: () {},
  color: Colors.blue,
  textColor: Colors.white,
  child: Icon(
    Icons.camera_alt,
    size: 24,
  ),
  padding: EdgeInsets.all(16),
  shape: CircleBorder(),
)
```

[![在此处输入图片说明](https://i.stack.imgur.com/xLOYo.png)](https://i.stack.imgur.com/xLOYo.png)



您可以使用[InkWell](https://docs.flutter.io/flutter/material/InkWell-class.html)来做到这一点：

> 响应触摸的材料的矩形区域。

下面的示例演示如何使用`InkWell`。**注意：**您不需`StatefulWidget`要这样做。我用它来改变计数状态。

例：

```dart
import 'package:flutter/material.dart';

class SettingPage extends StatefulWidget {
  @override
  _SettingPageState createState() => new _SettingPageState();
}

class _SettingPageState extends State<SettingPage> {
  int _count = 0;
  @override
  Widget build(BuildContext context) {
    return new Scaffold(
      body: new Center(
        child: new InkWell(// this is the one you are looking for..........
        onTap: () => setState(() => _count++),
        child: new Container(
          //width: 50.0,
          //height: 50.0,
          padding: const EdgeInsets.all(20.0),//I used some padding without fixed width and height
          decoration: new BoxDecoration(
            shape: BoxShape.circle,// You can use like this way or like the below line
            //borderRadius: new BorderRadius.circular(30.0),
            color: Colors.green,
          ),
          child: new Text(_count.toString(), style: new TextStyle(color: Colors.white, fontSize: 50.0)),// You can add a Icon instead of text also, like below.
          //child: new Icon(Icons.arrow_forward, size: 50.0, color: Colors.black38)),
        ),//............
      ),
      ),
    );
  }
}
```

如果要利用`splashColor`，请使用材料类型为circle的小部件`highlightColor`包装`InkWell`小`Material`部件。然后`decoration`在`Container`小部件中删除。

结果：

[![在此处输入图片说明](https://i.stack.imgur.com/rQMIM.png)](https://i.stack.imgur.com/rQMIM.png)

```dart
RawMaterialButton(
  onPressed: () {},
  constraints: BoxConstraints(),
  elevation: 2.0,
  fillColor: Colors.white,
  child: Icon(
    Icons.pause,
    size: 35.0,
  ),
  padding: EdgeInsets.all(15.0),
  shape: CircleBorder(),
)
```

记下 `constraints: BoxConstraints()`，这是为了不允许向左填充。


如果需要背景图像，则可以将CircleAvatar与IconButton一起使用。设置backgroundImage属性。

```dart
CircleAvatar(
  backgroundImage: NetworkImage(userAvatarUrl),
)
```

按钮示例：

```dart
        CircleAvatar(
          backgroundColor: Colors.blue,
          radius: 20,
          child: IconButton(
            padding: EdgeInsets.zero,
            icon: Icon(Icons.add),
            color: Colors.white,
            onPressed: () {},
          ),
        ),
```

[![在此处输入图片说明](https://i.stack.imgur.com/1m96y.png)](https://i.stack.imgur.com/1m96y.png)



实际上，有一个示例如何创建类似于FloatingActionButton的圆形IconButton。

```dart
Ink(
    decoration: const ShapeDecoration(
        color: Colors.lightBlue,
        shape: CircleBorder(),
    ),
    child: IconButton(
        icon: Icon(Icons.home),
        onPressed: () {},
    ),
)
```

要使用此代码示例创建本地项目，请运行：

```dart
flutter create --sample=material.IconButton.2 mysample
```

此代码将帮助您添加按钮而不会出现不必要的填充，

```dart
RawMaterialButton(
      elevation: 0.0,
      child: Icon(Icons.add),
      onPressed: (){},
      constraints: BoxConstraints.tightFor(
        width: 56.0,
        height: 56.0,
      ),
      shape: CircleBorder(),
      fillColor: Color(0xFF4C4F5E),
    ),
```

