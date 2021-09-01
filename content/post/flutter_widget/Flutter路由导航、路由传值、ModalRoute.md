---
title: "Flutter路由导航、路由传值、ModalRoute"
date: 2021-09-01T09:12:14+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["flutter","dart"]
categories: ["flutter","dart"]
---

## 一、基本路由使用

路由是页面跳转的根本，也可以称为导航跳转，Flutter 提供的是 `Navigator` 这个 Widget，能够使我们在不同的页面之间进行切换。

Flutter 中一切都是 Widget，因此本质上不存在页面，所谓的页面也不过是在某个 Widget 封装而已，比如常见的，我们会使用 `Scalfold` 包装出一个页面。

如果写过 weex 或者是 react native ，对于 `navigator.push` 和 `navigator.pop` 应该都比较熟悉

native 开发页面都是堆栈形式的压入和弹出，因此 push 和 pop 最形象



### 1、Navigator.push

`navigator.push` 的定义如下：

```dart
  @optionalTypeArgs
  static Future<T> push<T extends Object>(BuildContext context, Route<T> route) {
    return Navigator.of(context).push(route);
  }
```

可以看出，方法接收两个参数，一个是上下文，另外一个是 Route Widget，但是 `Route` 是一个抽象类，因此在实际使用中，我们不会直接传入`Route`，而是会使用 `MaterialRoute`

`MaterialRoute` 继承自 PageRoute，这个继承关系非常复杂：

- `MaterialRoute`
- `PageRoute`
- `ModalRoute`
- `TransitionRoute` with `LocalHistoryRoute`
- `OverlayRoute`
- `Route`

因此如果要自己实现一个用于 `Navigator.push` 的 Route 就比较麻烦了

`MaterialRoute` 的构造方式非常简单，就是一个 builder，然后返回一个页面即可：

其中 `fullscreenDialog `是一种比较特殊的页面打开方式，本质上会从底部弹起类似一个 Dialog 对话框，但是是全屏的，具体效果参照手机淘宝 ios 客户端登录

```dart
  MaterialPageRoute({
    @required this.builder,
    RouteSettings settings,
    this.maintainState = true,
    bool fullscreenDialog = false,
  })
```

简单的使用 `Navigator.push` 跳转页面则是非常简单的,比如做了一个按钮，点击跳转：

```dart
FlatButton(
  child: Row(
    children: <Widget>[Icon(Icons.search), Text('搜索')],
  ),
  onPressed: () {
    Navigator.push(
      context,
      MaterialPageRoute(builder: (context) => SearchPage()),
    );
  },
  color: Theme.of(context).buttonColor,
),
```

跳转的页面 `SearchPage` 的实现就不重复了，完整代码在下面能够看到

效果：

![2.gif](https://luckly007.oss-cn-beijing.aliyuncs.com/image/1716860396.gif)



### 2、 Navigator.pop

pop 的定义就比较简单了：

```dart
  @optionalTypeArgs
  static bool pop<T extends Object>(BuildContext context, [ T result ]) {
    return Navigator.of(context).pop<T>(result);
  }
```

使用上一般也非常简单：

```dart
onPressed: () {
   Navigator.pop(context);
},
```



## 二、构造函数传值 - 基础路由传值

上面代码中实现了页面的跳转，但实际上没有办法传递参数给下一跳页面。

先看下上面的用法：

```dart
    Navigator.push(
      context,
      MaterialPageRoute(builder: (context) => SearchPage()),
    );
```

最终我们是返回了一个 `MaterialPageRoute(builder: (context) => SearchPage()),`

如果 `SearchPage` 页面需要参数，比如需要一个 `id` 呢？

首先创造一个页面，为了区分开，改了个名字：

```dart
// 登录
class LoginPage extends StatelessWidget {
  final String title;
  final String id;

  const LoginPage({Key key, this.title = '表单', this.id}) : super(key: key);

  Text _getTitle() {
    return Text(id != null ? '${this.title} - ID:${this.id}' : this.title);
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: this._getTitle(),
      ),
      body: Center(
        child: _getTitle()
      )
    );
  }
}
```

上面这个 Widget 构造函数中需要一个 title 和 id 两个参数，而这个是需要上个页面中传过来的

所以上一跳页面跳转的时候，可以通过 Widget builder 的进行参数透传：

```dart
onPressed: () {
Navigator.of(context).push(
  MaterialPageRoute(
    builder: (context) => LoginPage(
      title: 'NewName',
      id: DateTime.now().toString(),
    ),
  ),
);
```

效果如下：

![3.gif](https://luckly007.oss-cn-beijing.aliyuncs.com/image/1208053172.gif)

从上面的结果中可以发现，最终可以每次跳转将数据带过去。



## 三、ModalRoute 传值 - 基础路由传值

上面传值是通过 Widget 的构造函数传递的，这种方式就必须依赖 Widget 写死构造参数

除此之外，还有另外一种方式，从 `MaterialPageRoute` 的构造参数中 可以看到 `RouteSettings: settings` 这个参数

而 `RouteSettings` 构造函数如下：

```dart
  const RouteSettings({
    this.name,
    this.isInitialRoute = false,
    this.arguments,
  });
```

其实 `RouteSettings` 就是路由的基本信息，`arguments` 可以用来存储路由相关的参数字段：

下面是一个基本的路由跳转，页面 Widget 构造函数不接受参合参数，但是路由的 settings 中配置了一个对象

```dart
Navigator.of(context).push(
  new MaterialPageRoute(
    builder: (context) {
      return NewRouteWidget();
    },
    settings: RouteSettings(
      arguments: {'name': 'postbird'},
    ), // 传参
    fullscreenDialog: true,
  ),
);
```

`NewRouteWidget` Widget 中想要拿到配置的 `arguments` 可以通过 `ModalRoute` 来拿

`ModalRoute` 构造如下：

```dart
  /// Creates a route that blocks interaction with previous routes.
  ModalRoute({
    RouteSettings settings,
  }) : super(settings: settings);
```

所以整个新页面 Widget 使用如下：

```dart
// 新路由页面
class NewRouteWidget extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    Map args = ModalRoute.of(context).settings.arguments;
    return Scaffold(
        appBar: AppBar(title: Text('获取参数')),
        body: Center(
            child: Column(
          children: <Widget>[
            Text(args.toString()),
            FlatButton(
              child: Text('pop'),
              onPressed: () {
                Navigator.pop(context);
              },
            )
          ],
        )));
  }
}
```

效果：

![GIF.gif](https://luckly007.oss-cn-beijing.aliyuncs.com/image/3826430932.gif)



## 四、完整代码



### 2、基础路由使用 & 路由传值

[https://github.com/postbird/FlutterHelloWorldDemo/blob/master/demo1/lib/bak/main.27-%E5%9F%BA%E7%A1%80%E8%B7%AF%E7%94%B1%26%E5%9F%BA%E7%A1%80%E8%B7%AF%E7%94%B1%E4%BC%A0%E5%80%BC.dart](https://github.com/postbird/FlutterHelloWorldDemo/blob/master/demo1/lib/bak/main.27-基础路由%26基础路由传值.dart)



### 1、Modal 路由传值

https://github.com/postbird/FlutterHelloWorldDemo/blob/master/demo1/lib/bak/main.4-Route.dart
