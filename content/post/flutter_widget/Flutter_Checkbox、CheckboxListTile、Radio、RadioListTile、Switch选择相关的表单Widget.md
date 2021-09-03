---
title: "Flutter_Checkbox、CheckboxListTile、Radio、RadioListTile、Switch选择相关的表单Widget"
date: 2021-09-01T09:01:49+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter","dart"]
categories: ["Flutter","dart"]
---

## 一、Checkbox 和 CheckboxListTile



### 1、

Flutter 实现了 `Checkbox` 的默认动画样式，支持的属性如下：

```dart
  const Checkbox({
    Key key,
    @required this.value,
    this.tristate = false,
    @required this.onChanged,
    this.activeColor,
    this.checkColor,
    this.materialTapTargetSize,
  })
```

其中 value 是 bool 类型的：

```dart
  /// Whether this checkbox is checked.
  ///
  /// This property must not be null.
  final bool value;
```

实现一个最简单 Checkbox，并且通过 state 显示文案：

```dart
Row(
  crossAxisAlignment: CrossAxisAlignment.center,
  children: <Widget>[
    Checkbox(
      activeColor: Colors.pink,
      checkColor: Colors.blue,
      value: this._flag,
      onChanged: (value) {
        setState(() {
          this._flag = value;
        });
      },
    ),
    Container(
      child: Text(this._flag ? '选中' : '未选中'),
    ),
  ],
)
```

效果：

![3.gif](https://luckly007.oss-cn-beijing.aliyuncs.com/image/2290884973.gif)



### 2、CheckboxListTile

在 APP 中我们经常遇到的选择场景往往是 ListView 形式的，因为 APP 需要点击区域够大，信息足够明确

`CheckboxListTile` 提供了类似 `ListTile` 样式的多选框，并且本身 `CheckboxListTile` 也是基于 `ListTile` 实现的

```dart
  CheckboxListTile(
    title: Text('标题'),
    subtitle: Text('二级标题'),
    activeColor: Colors.pink,
    secondary: Image.network(
      IMAGE_SRC,
      fit: BoxFit.cover,
      // color: Colors.grey[200],
      width: 60,
    ),
    value: this._flag,
    onChanged: (value) {
      setState(() {
        this._flag = value;
      });
    },
  ),
  Divider(),
  CheckboxListTile(
    title: Text('标题'),
    subtitle: Text('二级标题'),
    activeColor: Colors.pink,
    secondary: Icon(Icons.panorama),
    selected: this._flag,
    value: this._flag,
    onChanged: (value) {
      setState(() {
        this._flag = value;
      });
    },
  )
```

同样的，支持传入 value 和 onChanged，其他的属性和 ListTile 其实是差不多的

效果：

![4.gif](https://luckly007.oss-cn-beijing.aliyuncs.com/image/3912783971.gif)



## 二、Radio 和 RadioListTile



### 1、Radio

`Radio` 和 `Checkbox` 不同的地方在于，一般都是两个或以上的 `Radio` Wdiget 一起起作用

因此多个 Radio 往往绑定的是同一个值或者说是 state（同一个值得说法并不准确）

因此 Radio 又多了一个 required 的属性 `groupValue`，所谓的 `groupValue` 其实就是绑定在同一个 state 上的，当一个 Radio 被选中的时候，它的 `value` 就会传递给 `groupValue`（在 onchanged 中处理）

只要 `groupValue` 指向同一个变量，就说明这些 Radio 是同一个 group 中的，只会有一个被选中

```dart
  const Radio({
    Key key,
    @required this.value,
    @required this.groupValue,
    @required this.onChanged,
    this.activeColor,
    this.materialTapTargetSize,
  }) : super(key: key);
```

使用 Radio 实现单选：

```dart
  Row(
    crossAxisAlignment: CrossAxisAlignment.center,
    children: <Widget>[
      Radio(
        value: 1,
        activeColor: Colors.pink,
        groupValue: this._sex,
        onChanged: (value) {
          setState(() {
            this._sex = value;
          });
        },
      ),
      Text('男'),
      Radio(
        value: 2,
        activeColor: Colors.pink,
        groupValue: this._sex,
        onChanged: (value) {
          setState(() {
            this._sex = value;
          });
        },
      ),
      Text('女'),
    ],
  ),
  Row(
    children: <Widget>[
      Text('选择的值是：${this._sex} : ${this._sex == 1 ? '男' : '女'}'),
    ],
  ),
```

效果：

![6.gif](https://luckly007.oss-cn-beijing.aliyuncs.com/image/946636458.gif)



### 2、RadioListTile

和 `CheckboxListTile` 类似，也是基于 `ListTile` 实现的

同样，会多一个 `groupValue` 属性

```dart
RadioListTile(
  activeColor: Colors.pink,
  title: Text('标题'),
  subtitle: Text('二级标题'),
  secondary: Icon(Icons.person),
  selected: this._sex == 1,
  value: 1,
  groupValue: this._sex,
  onChanged: (value) {
    setState(() {
      this._sex = value;
    });
  },
),
Divider(height: 1),
RadioListTile(
  activeColor: Colors.pink,
  title: Text('标题'),
  subtitle: Text('二级标题'),
  secondary: Image.network(IMAGE_SRC, fit: BoxFit.cover),
  selected: this._sex == 2,
  value: 2,
  groupValue: this._sex,
  onChanged: (value) {
    setState(() {
      this._sex = value;
    });
  },
),
```

效果:

![7.gif](https://luckly007.oss-cn-beijing.aliyuncs.com/image/1462290761.gif)



## 三、Switch

Switch 比较简单没什么好说的，不过默认的设计风格是 Android 的设计风格，并非 iOS

```dart
Switch(
  activeColor: Colors.pink,
  value: this._flag,
  onChanged: (value) {
    setState(() {
      this._flag = value;
    });
  },
),
```

效果：

![8.gif](https://luckly007.oss-cn-beijing.aliyuncs.com/image/1036688287.gif)



## 四、完整代码：



### 1、Checkbox 和 CheckboxListTile

https://github.com/postbird/FlutterHelloWorldDemo/blob/master/demo1/lib/bak/main.40-checkbox%26CheckboxListTile.dart



### 2、Radio 和 RadioListTile

https://github.com/postbird/FlutterHelloWorldDemo/blob/master/demo1/lib/bak/main.41-Radio%26RadioListTile.dart



### 3、Switch

https://github.com/postbird/FlutterHelloWorldDemo/blob/master/demo1/lib/bak/main.42-Switch.dart
