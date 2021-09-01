---
title: "Flutter_Form、TextFormField及表单验证、表单输入框聚焦"
date: 2021-09-01T08:58:21+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["flutter","dart"]
categories: ["flutter","dart"]
---

## 一、Form 及 TextFormField 输入框校验

之前写过一篇文章是关于 Flutter 中的输入框的各种使用方式，使用的输入框是 `TextField`：

- http://www.ptbird.cn/flutter-textfield.html

`TextField` 比较简单，使用起来比较方便，但是并不支持表单数据的前置验证和错误提示。

`TextFormField` 基于 `TextField` 封装了一层，能够做到数据的前置校验

不过前置校验基本会和 `Form` Wdiget 一起使用

```dart
///
///  * [GlobalKey], a key that is unique across the entire app.
///  * [FormField], a single form field widget that maintains the current state.
///  * [TextFormField], a convenience widget that wraps a [TextField] widget in a [FormField].
  const Form({
    Key key,
    @required this.child,
    this.autovalidate = false,
    this.onWillPop,
    this.onChanged,
  })
```

一个 `Form` 中如果有多个输入框，一般通过 `Column` 或者 `ListView` 布局

这个 `Form` 我们可以通过指定 key 来统一整个 Form 中的输入框，参照 Form 的构造函数说明可以发现：

```dart
/// final _formKey = GlobalKey<FormState>();
```

一般我们通过创建一个 `GolbalKey<FormState>` 关联

```dart
  final GlobalKey<FormState> _formKey = GlobalKey<FormState>();
```

创建一个输入框也非常简单：

```dart
TextFormField(
  decoration: InputDecoration(
    hintText: '电话号码',
  ),
  validator: (value) {
    RegExp reg = new RegExp(r'^\d{11}$');
    if (!reg.hasMatch(value)) {
      return '请输入11位手机号码';
    }
    return null;
  },
)
```

创建一个输入框和 `TextField` 的使用没有任何区别，唯一多出来的属性时 `validator`，接收一个方法，并且方法的参数中会传入 `value` 当前表单的值。

**`validator` 校验逻辑**:

如果不通过，直接可以返回提示的文案，如果通过，则返回 null 即可

**调用数据校验：**

调用数据校验的时机一般放在按钮提交点击的时候，这个时候 `_formKey` 就派上用处了

```dart
if (_formKey.currentState.validate()) {
  Scaffold.of(context).showSnackBar(SnackBar(
    content: Text('提交成功...'),
  ));
}
_formKey.currentState.validate()` 这个方法可以直接校验整个 Form 表单中的输入框的值，调用每个输入框中我们设置 `validator
```

如果不通过，会提示错误，这是 `TextFormField` 完成的

完整代码：

```dart
class HomeContent extends StatefulWidget {
  HomeContent({Key key}) : super(key: key);

  _HomeContentState createState() => _HomeContentState();
}

class _HomeContentState extends State<HomeContent> {
  final GlobalKey<FormState> _formKey = GlobalKey<FormState>();

  @override
  Widget build(BuildContext context) {
    return Padding(
      padding: EdgeInsets.all(10),
      child: Column(
        children: <Widget>[
          Form(
            key: _formKey,
            child: Column(
              children: <Widget>[
                TextFormField(
                  decoration: InputDecoration(
                    hintText: '电话号码',
                  ),
                  validator: (value) {
                    RegExp reg = new RegExp(r'^\d{11}$');
                    if (!reg.hasMatch(value)) {
                      return '请输入11位手机号码';
                    }
                    return null;
                  },
                ),
                TextFormField(
                  decoration: InputDecoration(
                    hintText: '用户名',
                  ),
                  validator: (value) {
                    if (value.isEmpty) {
                      return '请输入用户名';
                    }
                    return null;
                  },
                ),
              ],
            ),
          ),
          SizedBox(height: 10),
          Row(
            children: <Widget>[
              Expanded(
                child: RaisedButton(
                  child: Text('提交'),
                  onPressed: () {
                    if (_formKey.currentState.validate()) {
                      Scaffold.of(context).showSnackBar(SnackBar(
                        content: Text('提交成功...'),
                      ));
                    }
                  },
                ),
              )
            ],
          ),
        ],
      ),
    );
  }
}
```

效果：

![1.gif](https://luckly007.oss-cn-beijing.aliyuncs.com/image/1436611943.gif)



## 二、表单聚焦 FocusNode

某些情况下我们可能需要动态的聚焦输入框，一般有两种情况，一种是进入页面自动聚焦输入框，另一种是通过控制变更输入框的聚焦



### 1、默认焦点

`TextFormField` 支持 `autofocus` 属性，在页面进来的时候，为 true 的输入框，会自动聚焦

![78186-41s9vfmdv7m.png](https://luckly007.oss-cn-beijing.aliyuncs.com/image/2981992153.png)

```dart
TextFormField(
  autofocus: true,
  decoration: InputDecoration(
    hintText: '用户名',
  ),
)
```



### 2、动态指定输入框聚焦

除了 `autofocus` 属性之外，`TextFormField` 还支持传入一个 `focusNode`，类型是 `FocusNode`，通过传入之后，能够实现这个基本操作

首先我们需要创建一个 FocusNode:

```dart
  FocusNode _focusNode = FocusNode();
TextFormField(
  focusNode: _focusNode,
  decoration: InputDecoration(
    hintText: '电话号码',
  ),
)
```

通过 `FocusScope` 使 FocusNode 生效

```dart
Positioned(
  child: InkWell(
      child: Icon(Icons.edit),
      onTap: () => FocusScope.of(context).requestFocus(_focusNode)),
  bottom: 30,
  right: 40,
)
```

效果：

![2.gif](https://luckly007.oss-cn-beijing.aliyuncs.com/image/298036237.gif)



## 三、完整代码



### 1、表单验证

```dart
import 'dart:math';

import 'package:flutter/material.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: Scaffold(
        appBar: AppBar(
          title: Text('表单验证'),
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
  final GlobalKey<FormState> _formKey = GlobalKey<FormState>();

  @override
  Widget build(BuildContext context) {
    return Padding(
      padding: EdgeInsets.all(10),
      child: Column(
        children: <Widget>[
          Form(
            key: _formKey,
            child: Column(
              children: <Widget>[
                TextFormField(
                  decoration: InputDecoration(
                    hintText: '电话号码',
                  ),
                  validator: (value) {
                    RegExp reg = new RegExp(r'^\d{11}$');
                    if (!reg.hasMatch(value)) {
                      return '请输入11位手机号码';
                    }
                    return null;
                  },
                ),
                TextFormField(
                  decoration: InputDecoration(
                    hintText: '用户名',
                  ),
                  validator: (value) {
                    if (value.isEmpty) {
                      return '请输入用户名';
                    }
                    return null;
                  },
                ),
              ],
            ),
          ),
          SizedBox(height: 10),
          Row(
            children: <Widget>[
              Expanded(
                child: RaisedButton(
                  child: Text('提交'),
                  onPressed: () {
                    if (_formKey.currentState.validate()) {
                      Scaffold.of(context).showSnackBar(SnackBar(
                        content: Text('提交成功...'),
                      ));
                    }
                  },
                ),
              )
            ],
          ),
        ],
      ),
    );
  }
}
```

输入框

```dart
import 'dart:math';

import 'package:flutter/material.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: Scaffold(
        appBar: AppBar(
          title: Text('表单聚焦'),
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
  final GlobalKey<FormState> _formKey = GlobalKey<FormState>();
  FocusNode _focusNode = FocusNode();
  @override
  Widget build(BuildContext context) {
    return Stack(
      children: <Widget>[
        Padding(
          padding: EdgeInsets.all(10),
          child: Column(
            children: <Widget>[
              Form(
                key: _formKey,
                child: Column(
                  children: <Widget>[
                    TextFormField(
                      focusNode: _focusNode,
                      decoration: InputDecoration(
                        hintText: '电话号码',
                      ),
                    ),
                    TextFormField(
                      autofocus: true,
                      decoration: InputDecoration(
                        hintText: '用户名',
                      ),
                    ),
                  ],
                ),
              ),
            ],
          ),
        ),
        Positioned(
          child: InkWell(
              child: Icon(Icons.edit),
              onTap: () => FocusScope.of(context).requestFocus(_focusNode)),
          bottom: 30,
          right: 40,
        ),
      ],
    );
  }
}
```

