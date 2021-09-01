---
title: "Flutter_TextField样式控制及输入框取值"
date: 2021-09-01T08:53:17+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["flutter","dart"]
categories: ["flutter","dart"]
---

## 一、TextField 实现各种各样的输入框

`TextField` 是一个非常简单的表单输入框 Widget，基于 `EditableText` Widget 实现

支持的属性非常多：

```dart
  const TextField({
    Key key,
    this.controller,
    this.focusNode,
    this.decoration = const InputDecoration(),
    TextInputType keyboardType,
    this.textInputAction,
    this.textCapitalization = TextCapitalization.none,
    this.style,
    this.strutStyle,
    this.textAlign = TextAlign.start,
    this.textAlignVertical,
    this.textDirection,
    this.readOnly = false,
    this.showCursor,
    this.autofocus = false,
    this.obscureText = false,
    this.autocorrect = true,
    this.maxLines = 1,
    this.minLines,
    this.expands = false,
    this.maxLength,
    this.maxLengthEnforced = true,
    this.onChanged,
    this.onEditingComplete,
    this.onSubmitted,
    this.inputFormatters,
    this.enabled,
    this.cursorWidth = 2.0,
    this.cursorRadius,
    this.cursorColor,
    this.keyboardAppearance,
    this.scrollPadding = const EdgeInsets.all(20.0),
    this.dragStartBehavior = DragStartBehavior.start,
    this.enableInteractiveSelection,
    this.onTap,
    this.buildCounter,
    this.scrollController,
    this.scrollPhysics,
  })
```

支持单行和多行的文件输入及表单值的获取



### 1、普通表单

直接通过 `TextField` 可以显示一个最简单的表单：

```dart
TextField()
```

效果:

![86566-69wwqzrcgac.png](https://luckly007.oss-cn-beijing.aliyuncs.com/image/748450152.png)



### 2、边框表单、label 动效

如果是 Material Design 的 UI 设计风格，我们经常会看到边框的表单，点击之后，一个 placeholder 浮动到上面去

在 TextField 中通过配置 `decoration` 属性，传入一个 `InputeDecoration` Widget 来配置具体的显示内容， `InputeDecoration` 支持的属性也非常的多，这里就不举出了。

`labelText` 是可以浮动的提示文案，而 placeholder 其实是通过 `hintText` 指定的

```dart
TextField(
  decoration: InputDecoration(
    labelText: '表单label',
    labelStyle: TextStyle(
      color: Colors.pink,
      fontSize: 12,
    ),
    helperText: 'helperText',
    hintText: 'Placeholder...',
    border: OutlineInputBorder(
      borderSide: BorderSide(
        color: Colors.pink,
      ),
    ),
  ),
)
```

效果：

![1.gif](https://luckly007.oss-cn-beijing.aliyuncs.com/image/3843257758.gif)

上面我并没有配置 focus 状态的外边框的颜色，如果要指定，则可以通过 `focusedBorder` 指定：

```dart
focusedBorder: OutlineInputBorder(
  borderSide: BorderSide(
    color: Colors.pink,
  ),
)
```

效果：

![47596-2oicvevt4mu.png](https://luckly007.oss-cn-beijing.aliyuncs.com/image/1627572783.png)



### 3、多行表单

```dart
TextField(
  maxLines: 4,
  decoration: InputDecoration(hintText: '多行文本'),
),
```

效果：

![98720-rn1s8g8y1te.png](https://luckly007.oss-cn-beijing.aliyuncs.com/image/4021439431.png)



### 4、密码框

如果要使一个表单变成密码框，只需配置 `obscureText` 属性是 true

```dart
TextField(
  obscureText: true,
  decoration: InputDecoration(hintText: '密码框'),
),
```

![75931-ea4l8x06cmr.png](https://luckly007.oss-cn-beijing.aliyuncs.com/image/2480513225.png)



### 5、icon 表单

`InputDecoration` 支持传入一个 icon 属性，传入之后，会在表单的前面显示一个 icon

```dart
TextField(
  obscureText: true,
  decoration: InputDecoration(
    hintText: '图标',
    icon: Icon(Icons.palette),
  ),
```

效果：

![77645-2b2ox8rpdxb.png](https://luckly007.oss-cn-beijing.aliyuncs.com/image/678827543.png)



## 二、TextField 获取表单值和设置初始值

如果要获取表单值和设置初始值，首先我们会使用一个 StatefulWidget，然后会监听 TextField 的 `onChanged` 事件，每次存取值。

这个逻辑在现代 Web 框架中已经很成熟了，但是初始值的设置以及表单值得绑定则不太一样。

TextField 接收一个 `controller` 属性，类型是 `TextEditingController`，从注释中可以看出，我们可以通过 `controller` 从外部控制表单的一切

类似于 Web 中拿到表单实例，然后在去控制表单一样

```dart
  /// Controls the text being edited.
  ///
  /// If null, this widget will create its own [TextEditingController].
  final TextEditingController controller;
```



### 1、设置初始值

首先我们需要定义一个 TextEditingController：

```dart
  TextEditingController _name = TextEditingController();
```

在 initState 的时候，设置初始值：

```dart
  @override
  void initState() {
    super.initState();
    this._name.text = 'ptbird'; // 设置初始值
  }
```

将 `_name` 传入给 `TextField` 组件：

```dart
TextField(
  decoration: InputDecoration(
    hintText: '账户',
    hintStyle: TextStyle(
      fontSize: 12,
      color: Colors.grey[300],
    ),
  ),
  controller: this._name,
  onChanged: (value) {
    this.setState(() {
      this._name.text = value;
    });
  },
)
```

先不关心 `onChanged` 的事件处理，效果如下：

![65110-b07u0ac3w4n.png](https://luckly007.oss-cn-beijing.aliyuncs.com/image/3936572271.png)



### 2、获取表单值，并同步渲染到页面

因为没有双向绑定，和 React 处理思想类似，都是手动 setState

```dart
  onChanged: (value) {
    this.setState(() {
      this._pass.text = value;
    });
  },
```

在 Container 中渲染两个值：

```dart
          Container(
            width: double.infinity,
            child: Text('${this._name.text} - ${this._pass.text}'),
          ),
```

最终效果：

![2.gif](https://luckly007.oss-cn-beijing.aliyuncs.com/image/4089725768.gif)



### 3、完整 TextField 设置和控制代码

### 

```dart
class _TextFieldDemoState extends State<TextFieldDemo> {
  TextEditingController _name = TextEditingController();
  TextEditingController _pass = TextEditingController();

  @override
  void initState() {
    super.initState();
    this._name.text = 'ptbird'; // 设置初始值
  }

  @override
  Widget build(BuildContext context) {
    return Padding(
      padding: EdgeInsets.all(10),
      child: Column(
        children: <Widget>[
          TextField(
            decoration: InputDecoration(
              hintText: '账户',
              hintStyle: TextStyle(
                fontSize: 12,
                color: Colors.grey[300],
              ),
            ),
            controller: this._name,
            onChanged: (value) {
              this.setState(() {
                this._name.text = value;
              });
            },
          ),
          SizedBox(height: 10),
          TextField(
            obscureText: true,
            decoration: InputDecoration(
              hintText: '密码',
              hintStyle: TextStyle(
                fontSize: 12,
                color: Colors.grey[300],
              ),
            ),
            controller: this._pass,
            onChanged: (value) {
              this.setState(() {
                this._pass.text = value;
              });
            },
          ),
          SizedBox(height: 10),
          Container(
            width: double.infinity,
            height: 40,
            child: RaisedButton(
              child: Text('Login'),
              onPressed: () {
                print(this._name);
                print(this._pass);
              },
            ),
          ),
          SizedBox(height: 10),
          Container(
            width: double.infinity,
            child: Text('${this._name.text} - ${this._pass.text}'),
          ),
        ],
      ),
    );
  }
}
```



## 三、完整代码示例

1、TextField 各种样式

```dart
import 'package:flutter/material.dart';
// import 'mock/list.dart' as newsList;

const TITLE = '标题标题标题标题标题标题标题';
const SUB_TITLE = '二级标题二级标题二级标题二级标题二级标题二级标题二级标题二级标题二';
const IMAGE_SRC =
    'http://imgcdn.ph.126.net/UQhSl0NAkp9wsn0keF-rLA==/3084402794814350820.jpg';
const IMAGE_SRC_2 =
    'http://imglf.nosdn.127.net/img/NVc1cHVseFhyWFcwdHhpdjJydFRvcWJSa0NNbGlRbGN2TXNOS3NwQVdET042YmpmemdjUm5RPT0.jpg';

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
          title: Text('表单'),
          backgroundColor: Colors.pink,
        ),
        body: HomeContent(),
      ),
    );
  }
}

class HomeContent extends StatelessWidget {
  const HomeContent({Key key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return TextFieldDemo();
  }
}

class TextFieldDemo extends StatefulWidget {
  TextFieldDemo({Key key}) : super(key: key);

  _TextFieldDemoState createState() => _TextFieldDemoState();
}

class _TextFieldDemoState extends State<TextFieldDemo> {
  @override
  Widget build(BuildContext context) {
    return Padding(
      padding: EdgeInsets.all(10),
      child: Column(
        children: <Widget>[
          TextField(),
          SizedBox(height: 10),
          TextField(
            decoration: InputDecoration(
              labelText: '表单label',
              labelStyle: TextStyle(
                color: Colors.pink,
                fontSize: 12,
              ),
              helperText: 'helperText',
              hintText: 'Placeholder...',
              border: OutlineInputBorder(
                borderSide: BorderSide(
                  color: Colors.pink,
                ),
              ),
            ),
          ),
          SizedBox(height: 10),
          TextField(
            maxLines: 4,
            decoration: InputDecoration(hintText: '多行文本'),
          ),
          SizedBox(height: 10),
          TextField(
            obscureText: true,
            decoration: InputDecoration(hintText: '密码框'),
          ),
          SizedBox(height: 10),
          TextField(
            obscureText: true,
            decoration:
                InputDecoration(hintText: '图标', icon: Icon(Icons.palette)),
          ),
        ],
      ),
    );
  }
}
```

### 2、设置表单初始值和获取表单值

```dart
import 'package:flutter/material.dart';
// import 'mock/list.dart' as newsList;

const TITLE = '标题标题标题标题标题标题标题';
const SUB_TITLE = '二级标题二级标题二级标题二级标题二级标题二级标题二级标题二级标题二';
const IMAGE_SRC =
    'http://imgcdn.ph.126.net/UQhSl0NAkp9wsn0keF-rLA==/3084402794814350820.jpg';
const IMAGE_SRC_2 =
    'http://imglf.nosdn.127.net/img/NVc1cHVseFhyWFcwdHhpdjJydFRvcWJSa0NNbGlRbGN2TXNOS3NwQVdET042YmpmemdjUm5RPT0.jpg';

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
          title: Text('表单取值设置值'),
          backgroundColor: Colors.pink,
        ),
        body: HomeContent(),
      ),
    );
  }
}

class HomeContent extends StatelessWidget {
  const HomeContent({Key key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return TextFieldDemo();
  }
}

class TextFieldDemo extends StatefulWidget {
  TextFieldDemo({Key key}) : super(key: key);

  _TextFieldDemoState createState() => _TextFieldDemoState();
}

class _TextFieldDemoState extends State<TextFieldDemo> {
  TextEditingController _name = TextEditingController();
  TextEditingController _pass = TextEditingController();

  @override
  void initState() {
    super.initState();
    this._name.text = 'ptbird'; // 设置初始值
  }

  @override
  Widget build(BuildContext context) {
    return Padding(
      padding: EdgeInsets.all(10),
      child: Column(
        children: <Widget>[
          TextField(
            decoration: InputDecoration(
              hintText: '账户',
              hintStyle: TextStyle(
                fontSize: 12,
                color: Colors.grey[300],
              ),
            ),
            controller: this._name,
            onChanged: (value) {
              this.setState(() {
                this._name.text = value;
              });
            },
          ),
          SizedBox(height: 10),
          TextField(
            obscureText: true,
            decoration: InputDecoration(
              hintText: '密码',
              hintStyle: TextStyle(
                fontSize: 12,
                color: Colors.grey[300],
              ),
            ),
            controller: this._pass,
            onChanged: (value) {
              this.setState(() {
                this._pass.text = value;
              });
            },
          ),
          SizedBox(height: 10),
          Container(
            width: double.infinity,
            height: 40,
            child: RaisedButton(
              child: Text('Login'),
              onPressed: () {
                print(this._name);
                print(this._pass);
              },
            ),
          ),
          SizedBox(height: 10),
          Container(
            width: double.infinity,
            child: Text('${this._name.text} - ${this._pass.text}'),
          ),
        ],
      ),
    );
  }
}
```

