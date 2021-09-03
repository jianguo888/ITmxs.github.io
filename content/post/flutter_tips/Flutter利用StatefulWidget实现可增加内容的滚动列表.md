---
title: "Flutter利用StatefulWidget实现可增加内容的滚动列表"
date: 2021-09-01T09:13:38+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter","dart"]
categories: ["Flutter","dart"]
---

## 一、实现的效果

ListView 中存在一个 add 按钮，能向列表中添加对象，并且 ListView 去渲染新增加的对象

![22222.gif](https://luckly007.oss-cn-beijing.aliyuncs.com/image/1380629881.gif)



## 二、实现



### 1、单条新闻 Widget

这个简单 就是 UI statelessWidget

```dart
class NewsItem extends StatelessWidget {
  NewsItem({Key key, this.title, this.subTitle, this.cover}) : super(key: key);

  final String title;
  final String subTitle;
  final String cover;

  @override
  Widget build(BuildContext context) {
    return ListTile(
      leading: Image.network(
        this.cover,
        width: 80,
        height: 80,
        fit: BoxFit.cover,
      ),
      title: Text(this.title),
      subtitle: Text(this.subTitle),
    );
  }
}
```



### 2、_newsList State

用来存储列表，默认有一个对象：

```dart
  List<Map> _newsList = [
    {
      'title': TITLE,
      'subTitle': SUB_TITLE,
      'cover': IMAGE_SRC,
    }
  ];
```



### 3、通过 _newsList 构造 ListView 的列表

```dart
  List<Widget> _buildNewsList() {
    return this._newsList.map((item) {
      return NewsItem(
        title: item['title'],
        subTitle: item['subTitle'],
        cover: item['cover'],
      );
    }).toList();
  }
```



### 4、处理按钮的点击事件

注意放在 `setState` 中处理的，因为 dart 语言的特性，不用和 javascript 处理 object 和 array 一样。

```dart
  _onPressedHandle() {
    setState(() {
      this._newsList.add({
        'title': TITLE,
        'subTitle': SUB_TITLE,
        'cover': IMAGE_SRC,
      });
    });
  }
```



### 5、完整的 ListView StatefulWidget

```dart
class NewsList extends StatefulWidget {
  NewsList({Key key}) : super(key: key);

  _NewsListState createState() => _NewsListState();
}

class _NewsListState extends State<NewsList> {
  List<Map> _newsList = [
    {
      'title': TITLE,
      'subTitle': SUB_TITLE,
      'cover': IMAGE_SRC,
    }
  ];

  List<Widget> _buildNewsList() {
    return this._newsList.map((item) {
      return NewsItem(
        title: item['title'],
        subTitle: item['subTitle'],
        cover: item['cover'],
      );
    }).toList();
  }

  _onPressedHandle() {
    setState(() {
      this._newsList.add({
        'title': TITLE,
        'subTitle': SUB_TITLE,
        'cover': IMAGE_SRC,
      });
    });
  }

  @override
  Widget build(BuildContext context) {
    return ListView(
      children: <Widget>[
        FlatButton(
          child: Text('add'),
          color: Theme.of(context).buttonColor,
          onPressed: this._onPressedHandle,
        ),
        SizedBox(height: 30),
        Column(
          children: this._buildNewsList(),
        )
      ],
    );
  }
}
```



## 三、完整代码

https://github.com/postbird/FlutterHelloWorldDemo/blob/master/demo1/lib/bak/main.25-StatefulWidget%26ListView.dart
