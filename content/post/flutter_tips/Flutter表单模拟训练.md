---
title: "Flutter表单模拟训练"
date: 2021-09-01T08:51:10+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["flutter","dart"]
categories: ["flutter","dart"]
---

## 一、Flutter 中的表单

相关文章：

- [Flutter TextField 、样式控制及输入框取值](http://www.ptbird.cn/flutter-textfield.html)
- [Flutter Checkbox、CheckboxListTile、Radio、RadioListTile、Switch 选择相关的表单 Widget](http://www.ptbird.cn/flutter-checkbox-checkboxlisttile-radio-radiolisttile-switch.html)
- [Flutter Form、TextFormField及表单验证、表单输入框聚焦](http://www.ptbird.cn/flutter-form-textformfield.html)



## 二、实现的效果

![65226-65nc37hgp5r.png](https://luckly007.oss-cn-beijing.aliyuncs.com/image/728683826.png)

实现上面所示的布局，并且在提交的时候，获取到所有的表单值



## 三、Widget 实现



### 1、复用输入框

用户名和描述信息都是输入框，因此使用同一个实现，无非指定一个 maxLines 即可

```dart
class TextContainer extends StatelessWidget {
  final Function onChanged;
  final String hintText;
  final int maxLines;
  TextContainer({
    Key key,
    this.onChanged,
    this.hintText,
    this.maxLines = 1,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return TextField(
      cursorColor: Colors.pink,
      maxLines: maxLines,
      onChanged: this.onChanged,
      decoration: InputDecoration(
        hintText: this.hintText,
        hintStyle: TextStyle(color: Colors.grey, fontSize: 12),
      ),
    );
  }
}
```



### 2、性别选择

```dart
// 性别
class SexContainer extends StatelessWidget {
  final Function onChanged;
  final int groupValue;
  // final

  SexContainer({Key key, this.onChanged, this.groupValue}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Row(
      children: <Widget>[
        Text('男'),
        Radio(
          activeColor: Colors.pink,
          value: 1,
          groupValue: this.groupValue,
          onChanged: this.onChanged,
        ),
        Text('女'),
        Radio(
          activeColor: Colors.pink,
          value: 2,
          groupValue: this.groupValue,
          onChanged: this.onChanged,
        ),
      ],
    );
  }
}
```



### 3.爱好多选择

多选这里通过传入 list 和 onChanged 来控制数据和事件

```dart
class Favorites extends StatelessWidget {
  final List<Map> list;
  final Function onChanged;
  List<Widget> _children = new List<Widget>();
  Favorites({Key key, this.list, this.onChanged}) : super(key: key) {
    this._initCheckboxList();
  }

  void _initCheckboxList() {
    for (int i = 0; i < this.list.length; i++) {
      Map item = this.list[i];
      this._children.add(Row(
        children: <Widget>[
          Text(item['title']),
          Checkbox(
            activeColor: Colors.pink,
            value: item['checked'],
            onChanged: this.onChanged(i),
          )
        ],
      ));
    }
  }

  @override
  Widget build(BuildContext context) {
    return Column(
      children: this._children,
    );
  }
}
```



### 4、整体布局

```dart
class _HomeContentState extends State<HomeContent> {
  String _username;
  int _sex = 1;
  String _desc;

  List<Map> _favorites = [
    {'title': '篮球', 'checked': true},
    {'title': '足球', 'checked': false},
    {'title': '英式橄榄球', 'checked': false},
  ];

  void _userNameChangedHandle(value) {
    setState(() {
      this._username = value;
    });
  }

  void _sexChangedHandle(value) {
    setState(() {
      this._sex = value;
    });
  }

  Function _favoritesChangedHandle(index) {
    return (value) {
      setState(() {
        this._favorites[index]['checked'] = value;
      });
    };
  }

  _descChangedHandle(value) {
    setState(() {
      this._desc = value;
    });
  }

  _submitHandle() {
    print(this._username);
    print(this._sex);
    print(this._favorites);
    print(this._desc);
  }

  @override
  Widget build(BuildContext context) {
    return ListView(
      padding: EdgeInsets.all(10),
      children: <Widget>[
        TextContainer(
          hintText: '用户名',
          onChanged: this._userNameChangedHandle,
        ),
        SizedBox(height: 10),
        SexContainer(
          groupValue: this._sex,
          onChanged: this._sexChangedHandle,
        ),
        SizedBox(height: 10),
        Favorites(
          list: this._favorites,
          onChanged: _favoritesChangedHandle,
        ),
        SizedBox(height: 10),
        TextContainer(
          maxLines: 3,
          hintText: '描述信息...',
          onChanged: this._descChangedHandle,
        ),
        SizedBox(height: 40),
        FlatButton(
          color: Colors.pink,
          textColor: Colors.white,
          child: Text('提交'),
          onPressed: this._submitHandle,
        )
      ],
    );
  }
}
```



### 4、点击提交的数据：

```dart
V/DartMessenger(13440): Received message from Dart over channel 'flutter/platform'
I/flutter (13440): [{title: 篮球, checked: true}, {title: 足球, checked: true}, {title: 英式橄榄球, checked: true}]
V/DartMessenger(13440): Deferring to registered handler to process message.
I/flutter (13440): null
V/PlatformChannel(13440): Received 'SystemSound.play' message.
```



## 四、完整代码

```dart
import 'package:flutter/material.dart';
// import 'mock/list.dart' as newsList;

const TITLE = '标题标题标题标题标题标题标题';
const SUB_TITLE = '二级标题二级标题二级标题二级标题二级标题二级标题二级标题二级标题二';
const IMAGE_SRC =
    'https://cdn.pixabay.com/photo/2019/06/17/07/47/madeira-4279303__240.jpg';
const IMAGE_SRC_2 =
    'https://cdn.pixabay.com/photo/2019/06/20/05/51/squirrel-4286247__480.jpg';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  MyApp({Key key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: Scaffold(
        appBar: AppBar(
          title: Text('个人信息编辑Demo'),
          backgroundColor: Colors.pink,
        ),
        body: HomeContent(),
      ),
    );
  }
}

class HomeContent extends StatefulWidget {
  HomeContent({Key key}) : super(key: key);

  _HomeContentState createState() => _HomeContentState();
}

class _HomeContentState extends State<HomeContent> {
  String _username;
  int _sex = 1;
  String _desc;

  List<Map> _favorites = [
    {'title': '篮球', 'checked': true},
    {'title': '足球', 'checked': false},
    {'title': '英式橄榄球', 'checked': false},
  ];

  void _userNameChangedHandle(value) {
    setState(() {
      this._username = value;
    });
  }

  void _sexChangedHandle(value) {
    setState(() {
      this._sex = value;
    });
  }

  Function _favoritesChangedHandle(index) {
    return (value) {
      setState(() {
        this._favorites[index]['checked'] = value;
      });
    };
  }

  _descChangedHandle(value) {
    setState(() {
      this._desc = value;
    });
  }

  _submitHandle() {
    print(this._username);
    print(this._sex);
    print(this._favorites);
    print(this._desc);
  }

  @override
  Widget build(BuildContext context) {
    return ListView(
      padding: EdgeInsets.all(10),
      children: <Widget>[
        TextContainer(
          hintText: '用户名',
          onChanged: this._userNameChangedHandle,
        ),
        SizedBox(height: 10),
        SexContainer(
          groupValue: this._sex,
          onChanged: this._sexChangedHandle,
        ),
        SizedBox(height: 10),
        Favorites(
          list: this._favorites,
          onChanged: _favoritesChangedHandle,
        ),
        SizedBox(height: 10),
        TextContainer(
          maxLines: 3,
          hintText: '描述信息...',
          onChanged: this._descChangedHandle,
        ),
        SizedBox(height: 40),
        FlatButton(
          color: Colors.pink,
          textColor: Colors.white,
          child: Text('提交'),
          onPressed: this._submitHandle,
        )
      ],
    );
  }
}

class TextContainer extends StatelessWidget {
  final Function onChanged;
  final String hintText;
  final int maxLines;
  TextContainer({
    Key key,
    this.onChanged,
    this.hintText,
    this.maxLines = 1,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return TextField(
      cursorColor: Colors.pink,
      maxLines: maxLines,
      onChanged: this.onChanged,
      decoration: InputDecoration(
        hintText: this.hintText,
        hintStyle: TextStyle(color: Colors.grey, fontSize: 12),
      ),
    );
  }
}

// 性别
class SexContainer extends StatelessWidget {
  final Function onChanged;
  final int groupValue;
  // final

  SexContainer({Key key, this.onChanged, this.groupValue}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Row(
      children: <Widget>[
        Text('男'),
        Radio(
          activeColor: Colors.pink,
          value: 1,
          groupValue: this.groupValue,
          onChanged: this.onChanged,
        ),
        Text('女'),
        Radio(
          activeColor: Colors.pink,
          value: 2,
          groupValue: this.groupValue,
          onChanged: this.onChanged,
        ),
      ],
    );
  }
}

class Favorites extends StatelessWidget {
  final List<Map> list;
  final Function onChanged;
  List<Widget> _children = new List<Widget>();
  Favorites({Key key, this.list, this.onChanged}) : super(key: key) {
    this._initCheckboxList();
  }

  void _initCheckboxList() {
    for (int i = 0; i < this.list.length; i++) {
      Map item = this.list[i];
      this._children.add(Row(
            children: <Widget>[
              Text(item['title']),
              Checkbox(
                activeColor: Colors.pink,
                value: item['checked'],
                onChanged: this.onChanged(i),
              )
            ],
          ));
    }
  }

  @override
  Widget build(BuildContext context) {
    return Column(
      children: this._children,
    );
  }
}
```

