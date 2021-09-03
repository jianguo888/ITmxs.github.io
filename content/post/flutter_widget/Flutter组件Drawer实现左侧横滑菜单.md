---
title: "Flutter组件Drawer实现左侧横滑菜单"
date: 2021-09-01T09:06:18+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter","dart"]
categories: ["Flutter","dart"]
---

## 一、Scalfold 的 Drawer

Scalfold 是 Flutter MaterialApp 常用的布局 Widget，他接受一个 `drawer` 属性，支持配置 Drawer

Drawer 是 Flutter 提供的能够实现左侧横滑的 Widget，构造非常简单：

```dart
  const Drawer({
    Key key,
    this.elevation = 16.0,
    this.child,
    this.semanticLabel,
  })
```

Drawer Widget 的 child 支持传入别的 Widget 实现布局，一般我们都会传入一个 ListView 或者 Column，进行纵向布局

目标是实现如下的效果：

![GIF.gif](https://luckly007.oss-cn-beijing.aliyuncs.com/image/2897426136.gif)

整个左侧滑入的 View 都是 Drawer 提供的基本能力，我们需要做的，其实就是去进行立面的 Widget 布局

UI 布局这里不说太多，没什么意义，贴一下顶部的头像背景 Header 区域布局实现:

```dart
class DrawerHeaderDemo extends StatelessWidget {
  const DrawerHeaderDemo({Key key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return DrawerHeader(
      child: Column(
        children: <Widget>[
          ClipOval(
            child: Image.network(
              IMAGE_SRC,
              width: 65,
              height: 65,
              fit: BoxFit.cover,
            ),
          ),
          SizedBox(height: 20),
          Center(
            child: Text(
              'Postbird',
              style: TextStyle(color: Colors.white),
            ),
          ),
          SizedBox(height: 10),
          Center(
            child: Text(
              '没有任何描述~',
              style: TextStyle(color: Colors.white),
            ),
          ),
        ],
      ),
      decoration: BoxDecoration(
        color: Colors.grey[350],
        image: DecorationImage(
          image: NetworkImage(IMAGE_SRC_2),
          fit: BoxFit.cover,
        ),
      ),
    );
  }
}
```

最终效果：

![98165-tksgumcvu2j.png](https://luckly007.oss-cn-beijing.aliyuncs.com/image/1182051144.png)

完整的代码在文章最下面可以找到。



## 二、UserAccountsDrawerHeader Flutter 提供的快速布局实现

上面实现用户信息的代码中可以发现实现布局比较复杂，如果对布局没有特别高的要求，可以直接使用 Flutter 提供的 `UserAccountsDrawerHeader` 这个 Widget

提供了默认的用户信息 Header，样式如下：

![89843-y5yvqcwrpgp.png](https://luckly007.oss-cn-beijing.aliyuncs.com/image/2372576705.png)

UserAccountsDrawerHeader 构造函数声明如下：

```dart
  const UserAccountsDrawerHeader({
    Key key,
    this.decoration,
    this.margin = const EdgeInsets.only(bottom: 8.0),
    this.currentAccountPicture,
    this.otherAccountsPictures,
    @required this.accountName,
    @required this.accountEmail,
    this.onDetailsPressed,
  })
```

借助 Widget 直接实现布局：

```dart
return UserAccountsDrawerHeader(
  accountName: Text('postbird'),
  accountEmail: Text('没有任何描述~'),
  currentAccountPicture: CircleAvatar(
    backgroundImage: NetworkImage(IMAGE_SRC),
    backgroundColor: Colors.grey[350],
  ),
  otherAccountsPictures: <Widget>[
    CircleAvatar(
      backgroundImage: NetworkImage(IMAGE_SRC),
      backgroundColor: Colors.grey[350],
    ),
  ],
  decoration: BoxDecoration(
    image: DecorationImage(
      image: NetworkImage(IMAGE_SRC_2),
      fit: BoxFit.cover,
    ),
    color: Colors.grey[350],
  ),
);
```

如果要在 `Drawer` 中实现跳转，只需要使用 Navigator 做两个事情：

```dart
Navigator.of(context).pop();
            Navigator.of(context)
                .push(MaterialPageRoute(builder: (context) => UserPage()));
```

比如我一个实现如下：

```dart
class DrawerHeaderDemo extends StatelessWidget {
  const DrawerHeaderDemo({Key key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return UserAccountsDrawerHeader(
      accountName: Text('postbird'),
      accountEmail: Text('没有任何描述~'),
      currentAccountPicture: CircleAvatar(
        backgroundImage: NetworkImage(IMAGE_SRC),
        backgroundColor: Colors.grey[350],
      ),
      otherAccountsPictures: <Widget>[
        CircleAvatar(
          backgroundImage: NetworkImage(IMAGE_SRC),
          backgroundColor: Colors.grey[350],
        ),
      ],
      decoration: BoxDecoration(
        image: DecorationImage(
          image: NetworkImage(IMAGE_SRC_2),
          fit: BoxFit.cover,
        ),
        color: Colors.grey[350],
      ),
    );
  }
}

class ListItem extends StatelessWidget {
  const ListItem({Key key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Container(
      child: ListTile(
          leading: CircleAvatar(
            backgroundColor: Colors.pink,
            child: Icon(Icons.home),
          ),
          title: Text('首页'),
          onTap: () {
            Navigator.of(context).pop();
            Navigator.of(context)
                .push(MaterialPageRoute(builder: (context) => UserPage()));
          }),
    );
  }
}
```

最终效果：

![2.gif](https://luckly007.oss-cn-beijing.aliyuncs.com/image/4104252211.gif)



## 三、完整代码



### 1、Dawer

[https://github.com/postbird/FlutterHelloWorldDemo/blob/dev1/demo1/lib/bak/main.33-Drawer%20DrawerHeader.dart](https://github.com/postbird/FlutterHelloWorldDemo/blob/dev1/demo1/lib/bak/main.33-Drawer DrawerHeader.dart)



### 2、UserAccountsDrawerHeader 实践代码

https://github.com/postbird/FlutterHelloWorldDemo/blob/dev1/demo1/lib/bak/main.34-UserAccountsDrawerHeader.dart
