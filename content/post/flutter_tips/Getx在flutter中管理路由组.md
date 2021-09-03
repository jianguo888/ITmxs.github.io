---
title: "Getx在flutter中管理路由组"
date: 2021-08-28T14:31:07+08:00
draft: false
tags: ["Flutter"]
categories: ["Flutter"]
---

> 本文主要介绍

<!--more-->

## 路由

### 普通路由导航

打开到新的页面：



```dart
Get.to(NextScreen());
```

对应原生路由：



```dart
Navigator.push(context, MaterialPageRoute<void>(
  builder: (BuildContext context) {
    return NextScreen();
  },
));
```

返回：



```dart
Get.back();
```

对应原生路由：



```dart
Navigator.pop(context);
```

打开新页面，并且用新页面替换旧页面（删除旧页面）：



```dart
Get.off(NextScreen());
```

对应原生路由：



```dart
 Navigator.pushReplacement(context, MaterialPageRoute<void>(
      builder: (BuildContext context) {
        return NextScreen();
      },
    ));
```

打开新页面并删除之前的所有路由：



```dart
Get.offAll(NextScreen());
```

对应原生路由：



```dart
Navigator.pushAndRemoveUntil(
  context,
  MaterialPageRoute<void>(
    builder: (BuildContext context) {
      return NextScreen();
    },
  ),
  (Route<dynamic> route) => false,
);
```

导航到新页面，在返回时接收返回数据：



```dart
var data = await Get.to(NextScreen());
```

对应原生路由：



```dart
var data = await  Navigator.push(context, MaterialPageRoute<void>(
  builder: (BuildContext context) {
    return NextScreen();
  },
));
```

带返回值返回前一个路由，配合上面使用：



```dart
Get.back(result: 'success');
```

对应原生路由：



```dart
 Navigator.pop(context, 'success');
```

### 别名路由导航

1. 声明别名：



```dart
abstract class Routes {
  static const Initial = '/';
  static const NextScreen = '/NextScreen';

}
```

1. 注册路由表：



```dart
abstract class AppPages {
  static final pages = [
    GetPage(
      name: Routes.Initial,
      page: () => HomePage(),
    ),
    GetPage(
      name: Routes.NextScreen,
      page: () => NextScreen(),
    ),
  ];
}
```

1. 替换MaterialApp为GetMaterialApp：



```cpp
void main() {
  runApp(GetMaterialApp(
    debugShowCheckedModeBanner: false,
    initialRoute: Routes.INITIAL,
    theme: appThemeData,
    defaultTransition: Transition.fade,
    getPages: AppPages.pages,
    home: HomePage(),
  ));
}
```

#### 使用

导航到下一个页面：



```css
Get.toNamed(Routes.NextScreen);
```

导航到下一个页面并删除前一个页面：



```css
Get.offNamed(Routes.NextScreen);
```

导航到下一个页面并删除以前所有的页面：



```css
Get.offAllNamed(Routes.NextScreen);
```

#### 发送数据到别名路由：

Get在这里接受任何东西，无论是一个字符串，一个Map，一个List，甚至一个类的实例。



```tsx
Get.toNamed(Routes.NextScreen, arguments: '新垣结衣');
```

#### 获取参数：



```dart
String name=Get.arguments;
```

#### 动态网页链接:

像web一样携带参数，适合前端开发的风格。



```bash
Get.offAllNamed("/NextScreen?device=phone&id=354&name=Enzo");
```

获取参数：



```dart
int id = Get.parameters['id'];
// out: 354
String name=Get.parameters['name'];
```

还可以这样定义路由别名：



```jsx
GetPage(
name: '/profile/:user',
page: () => UserProfile(),
),
```

导航：



```bash
Get.toNamed("/profile/34954");
```

在第二个页面上，通过参数获取数据



```dart
print(Get.parameters['user']);
// out: 34954
```

### 中间件

在跳转前做些事情，比如判断是否登录，可以使用routingCallback来实现：



```csharp
GetMaterialApp(
  routingCallback: (routing) {
    if(routing.current == '/second'){
     // 如果登录。。。
    }
  }
)
```

#### GetMiddleware

有些页面需要登录验证，有些不需要，这时候可以不使用 `routingCallback`，单独写一个验证的`GetMiddleware`：



```dart
class AuthMiddleware extends GetMiddleware {
  @override
  int? priority = 2;

  bool isAuthenticated = false;

  @override
  RouteSettings? redirect(String? route) {
    debugPrint('=======AuthMiddleware.redirect:$route');
    if (!isAuthenticated) {
      return RouteSettings(name: Routes.LOGIN);
    }
    return super.redirect(route);
  }

  @override
  GetPage? onPageCalled(GetPage? page) {
    debugPrint('=======AuthMiddleware.onPageCalled:$page');
    return super.onPageCalled(page);
  }
}
```

然后在 GetPage里使用：



```jsx
  GetPage(
      name: Routes.MIDDLEWARE_PAGE,
      page: () => MiddlewarePagePage(),
      binding: MiddlewarePageBinding(),
      middlewares: [AuthMiddleware()],
    ),
```

这样跳转的时候如果没有登录，就重定向到`Routes.LOGIN`这个路由。

### 小部件导航

#### SnackBars

弹出：



```bash
Get.snackbar('Hi', 'i am a modern snackbar');
```

对应原生写法：



```dart
final snackBar = SnackBar(
  content: Text('Hi!'),
  action: SnackBarAction(
    label: 'I am a old and ugly snackbar',
    onPressed: (){}
  ),
);

//用Flutter创建一个简单的SnackBar，你必须获得Scaffold的context，或者你必须使用一个GlobalKey附加到你的Scaffold上。

Scaffold.of(context).showSnackBar(snackBar);
```

#### Dialogs

打开一个默认的Dialog：



```dart
Get.defaultDialog(
  onConfirm: () => print("Ok"),
  middleText: "Dialog made in 3 lines of code"
);
```

打开自定义的Dialog：



```css
Get.dialog(YourDialogWidget());
```

#### BottomSheets

Get.bottomSheet类似于showModalBottomSheet，但不需要context：



```tsx
Get.bottomSheet(
  Container(
    child: Wrap(
      children: <Widget>[
        ListTile(
          leading: Icon(Icons.music_note),
          title: Text('Music'),
          onTap: () => {}
        ),
        ListTile(
          leading: Icon(Icons.videocam),
          title: Text('Video'),
          onTap: () => {},
        ),
      ],
    ),
  )
);
```

