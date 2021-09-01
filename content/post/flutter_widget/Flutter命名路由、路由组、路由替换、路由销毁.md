---
title: "Flutter命名路由、路由组、路由替换、路由销毁"
date: 2021-09-01T09:11:22+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["flutter","dart"]
categories: ["flutter","dart"]
---

## 一、路由表 和 命名路由

之前写了一篇文章介绍了路由导航及路由传参，文章地址：

- http://www.ptbird.cn/admin/write-post.php?cid=3199

现在无论是服务端项目还是前端项目基本都是通过路由来决定资源的访问，而项目如果复杂，路由不能随便写，导航也不能直接写个 Wdiget builder

因此最终我们都是通过`路由表`和`命名路由`来进行导航



### 1、路由表

`Scaffold` Widget 中有一个构造函数的参数是 `routes`，是一个 `Map` 类型，类型声明如下：

```dart
  final Map<String, WidgetBuilder> routes;
```

一个字符串映射一个 WidgetBuilder

字符串就是路由的名称，而映射只需要返回一个 widget builder 即可,这里我们构造这个路由表：

> `SearchPage` 、 `LoginPage`、 `SignPage` 三个页面的代码不重复，文章最后能够找到，就是三个 widget

```dart
  final Map<String, WidgetBuilder> _routes = {
    '/search': (context) => SearchPage(),
    '/login': (context) => LoginPage(),
    '/sign': (context, {arguments}) => SignPage(arguments: arguments),
  };
```

上面构造出了路由表，就可以直接配置到 `Scalfold` 中



### 2、onGenerateRoute 路由拦截

Scalfold 同时支持一个 `onGenerateRoute` 的参数，接受一个 Route 工厂函数：

```dart
  /// {@macro flutter.widgets.widgetsApp.onGenerateRoute}
  final RouteFactory onGenerateRoute;
```

为什么要这么做，从上面的路由表可以看出，`'/sign': (context, {arguments}) => SignPage(arguments: arguments),` `/sign` 这个路由的构造中我们在 `builder` 中接受了第二个可选命名参数： `arguments`

如何将 arguments 传递给 widget 的构造函数，并且如何在路由跳转之前做些事情都可以在 `onGenerateRoute` 中进行

比如下面的方法：

```dart
  Route _routeGenerator(RouteSettings settings) {
    final String name = settings.name;
    final Function pageBuilder = this._routes[name];
    if (pageBuilder != null) {
      if (settings.arguments != null) {
        // 如果透传了参数
        return MaterialPageRoute(
            builder: (context) =>
                pageBuilder(context, arguments: settings.arguments));
      } else {
        // 没有透传参数
        return MaterialPageRoute(builder: (context) => pageBuilder(context));
      }
    }
    return MaterialPageRoute(builder: (context) => HomeContent());
  }
```

上面代码中，从拦截到路由之后，首先从路由表中拿到路由的 builder，如果能够拿到 builder，则判断是否存在 RouteSettings，如果存在则直接通关构造函数的 arguments 传递给页面 Page Widget。

这种条件就允许页面 Widget 构造函数中，必须有 `arguments` 这个参数才可以，比如 `/sign` 代码如下：

这样的优点在于不需要通过 `ModalRoute.of(context).settings.xxx` 拿数据。直接映射到 Widget 中

```dart
//  onGeneate
class SignPage extends StatelessWidget {
  final Map arguments;
  SignPage({Key key, this.arguments}) : super(key: key) {
    // print(this.arguments);
  }

  @override
  Widget build(BuildContext context) {
    String tmp = ModalRoute.of(context).settings.arguments.toString();
    String tmp2 = this.arguments.toString();
    return Scaffold(
      appBar: AppBar(
        title: Text(tmp),
      ),
      body: Center(child: Text(tmp2)),
    );
  }
}
```

需要注意上面的代码，两种方式我都获取了 arguments:

```dart
    String tmp = ModalRoute.of(context).settings.arguments.toString();
    String tmp2 = this.arguments.toString();
```



### 3、命名路由导航 `Navigator.of(context).pushName`

创建好了路由表和拦截就可以进行路由导航：

```dart
Navigator.of(context).pushNamed('/search');
```

此时如果要传值，根据 路由拦截 的方法，可以如下传递：

```dart
Navigator.of(context).pushNamed('/login', arguments: {
  "title": "透传title",
  "name": 'postbird',
  'passw': '123456'
});
```



### 4、效果：

![11111.gif](https://luckly007.oss-cn-beijing.aliyuncs.com/image/1365211518.gif)



## 二、路由替换 Navigator.of(context).pushReplacementNamed

正常我们跳转页面，是通过 `Navigator.push` 或者 `pushNamed` 实现的，每次都是把页面压入堆栈，在回退 的时候会回退到上一跳页面

某些场景下我们期望不返回上一条，在跳转吓一跳的时候替换本页面，将上一个页面从堆栈中移除，这时候就需要 `Navigator.of(context).pushReplacementNamed` 这个方法

```dart
FlatButton(
  child: Text('SignPage Replacement'),
  color: Theme.of(context).buttonColor,
  onPressed: () {
    Navigator.of(context).pushReplacementNamed('/sign');
  },
),
```

本质上使用和 `pushNamed` 是一样的，只是效果会不一样：

![22222.gif](https://luckly007.oss-cn-beijing.aliyuncs.com/image/603967248.gif)

**从上面可以发现，从 home -> login -> sign，然后在 sign 中点击返回按钮的时候，中间不会经过 login，直接就返回 home 了**



## 三、根路由 `pushNamedAndRemoveUntil`

与上面 pushReplacementNamed 差不多思想，但是场景更激进，比如我需要直接返回首页，之前的所有路由全部干掉，堆栈清空

这种场景下，可以借助 `Navigator.of(context).pushNamedAndRemoveUntil`

从名字上可以看出来，可以有条件的做一些事情

```dart
FlatButton(
  child: Text('Return '),
  color: Theme.of(context).buttonColor,
  onPressed: () {
    // 根
    Navigator.of(context).pushNamedAndRemoveUntil(
      '/',
      (route) => route == null,
    );
  },
)
```

`pushNamedAndRemoveUntil` 接受两个参数，第一个是路由名，（这里我们使用了根路由）第二个则是对堆栈中的 route 的处理：

下面的处理中我们清空了 route 全部置为 null，则会路由到 '/' 根路由前的所有 route 都被干掉。

```dart
    Navigator.of(context).pushNamedAndRemoveUntil(
      '/',
      (route) => route == null,
    );
```

最终效果：

![33333.gif](https://luckly007.oss-cn-beijing.aliyuncs.com/image/2559029506.gif)

**上面效果中能够发现， home -> login -> sign1 -> sign2 -> sign3** 然后在 sign3 中点击 return 的时候，直接返回的 home，中间的路由都被干掉了。



## 四、完整代码：



### 1、命名路由和路由组

[https://github.com/postbird/FlutterHelloWorldDemo/blob/master/demo1/lib/bak/main.28%E5%91%BD%E5%90%8D%E8%B7%AF%E7%94%B1-routes%E8%A1%A8%E5%92%8ConGenerateRoute.dart](https://github.com/postbird/FlutterHelloWorldDemo/blob/master/demo1/lib/bak/main.28命名路由-routes表和onGenerateRoute.dart)



### 2、路由替换和路由销毁

[https://github.com/postbird/FlutterHelloWorldDemo/blob/master/demo1/lib/bak/main.29-%E6%9B%BF%E6%8D%A2%E8%B7%AF%E7%94%B1%26%E8%BF%94%E5%9B%9E%E6%A0%B9.dart](https://github.com/postbird/FlutterHelloWorldDemo/blob/master/demo1/lib/bak/main.29-替换路由%26返回根.dart)
