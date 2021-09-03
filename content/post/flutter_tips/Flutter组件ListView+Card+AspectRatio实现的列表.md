---
title: "Flutter组件ListView+Card+AspectRatio实现的列表"
date: 2021-09-01T09:15:43+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter","dart"]
categories: ["Flutter","dart"]
---

## 一、列表效果

![GIF.gif](http://www.ptbird.cn/usr/uploads/2019/07/523229494.gif)

主要使用的 Widget：

- ListView
- Card
- AspectRatio



## 二、 Widget 的构建



### 1、Card + AspectRatio 组成的子项

```dart
class CardItem2 extends StatelessWidget {
  CardItem2({
    this.title,
    this.subTitle,
    this.cover,
  });
  final String title;
  final String subTitle;
  final String cover;
  @override
  Widget build(BuildContext context) {
    return Card(
      child: Column(
        children: <Widget>[
          AspectRatio(
            child: Container(
              decoration: BoxDecoration(
                image: DecorationImage(
                  image: NetworkImage(this.cover),
                  fit: BoxFit.cover,
                ),
                borderRadius: BorderRadius.vertical(
                  top: Radius.circular(4),
                ),
              ),
            ),
            aspectRatio: 16 / 9,
          ),
          ListTile(
            title:
            Text(this.title, maxLines: 1, overflow: TextOverflow.ellipsis),
            subtitle: Text(this.subTitle,
                maxLines: 1, overflow: TextOverflow.ellipsis),
            leading: ClipOval(
              child: CircleAvatar(backgroundImage: NetworkImage(this.cover)),
            ),
          )
        ],
      ),
      margin: EdgeInsets.all(10),
    );
  }
}
```

这里的坑还是在于图片会将 Card Widget 的圆角遮盖住，因此需要特殊处理下图片的圆角：

```dart
                decoration: BoxDecoration(
                image: DecorationImage(
                  image: NetworkImage(this.cover),
                  fit: BoxFit.cover,
                ),
                borderRadius: BorderRadius.vertical(
                  top: Radius.circular(4),
                ),
```



### 2、构造列表

子项构造 OK 构造列表就简单了，这里还是使用 `ListView.builder` 构造列表：

```dart
class HomeContent extends StatelessWidget {
  Widget buildItem(BuildContext context, int index) {
    Map news = newsList.news[index];
    return CardItem2(
      title: news['title'],
      subTitle: news['docurl'],
      cover: news['imgurl'],
    );
  }

  @override
  Widget build(BuildContext context) {
    return ListView.builder(
      itemCount: newsList.news.length,
      itemBuilder: buildItem,
    );
  }
}
```



## 三、代码



### 1、AspectRatio demo

https://github.com/postbird/FlutterHelloWorldDemo/blob/master/demo1/lib/bak/main.21-AspectRatio.dart



### 2、列表

[https://github.com/postbird/FlutterHelloWorldDemo/blob/master/demo1/lib/bak/main.22-Card%20%E5%AE%9E%E7%8E%B0%E5%88%97%E8%A1%A8.dart](https://github.com/postbird/FlutterHelloWorldDemo/blob/master/demo1/lib/bak/main.22-Card 实现列表.dart)
