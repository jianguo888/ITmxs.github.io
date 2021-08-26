---
title: "《如何根据函数返回的值对dart中的List进行排序》"
subtitle: ""
date: 2021-08-20T10:47:09+08:00
lastmod: 2021-08-20T10:47:09+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["flutter"]
categories: ["flutter"]

hiddenFromHomePage: false
hiddenFromSearch: false
twemoji: false

fontawesome: true
linkToMarkdown: true
rssFullText: false


featuredImage: "https://luckly007.oss-cn-beijing.aliyuncs.com/img/90c6cc12-742e-4c9f-b318-b912f163b8d0.png"
featuredImagePreview: "https://luckly007.oss-cn-beijing.aliyuncs.com/img/90c6cc12-742e-4c9f-b318-b912f163b8d0.png"

toc:
  enable: true
  auto: true
math:
  enable: false
lightgallery: false
license: ""
share:
  enable: true
comment: true
mapbox:
  accessToken: ""
---



> 本文主要介绍flutter中的开发技巧1

<!--more-->

# 关于排序：如何根据函数返回的值对dart中的List进行排序

```dart

void main(){
  List<POJO> pojo = [POJO(5), POJO(3),POJO(7),POJO(1)];
// fill list

pojo..sort((a, b) => a.id.compareTo(b.id));
  for(var i in pojo){
    print(i.id);      // prints list in sorted order i.e 1 3 5 7
  }

}

class POJO {
  int id;

  POJO(this.id);
}
```

# 关于android：Flutter TabBarView不断调用构建器

我的tabBar有一个StatelessWidget小部件，其中包含2个statefulWidgets。事实是，当单击管理器以查看我的所有选项卡时(默认情况下在我的第一个选项卡上登陆)，tab1小部件生成器一直被调用。

我已经尝试了这两种方法，但是它们没有用：

扑朔迷离的多标签页/页面视图

将Flutter切换到选项卡将重新加载小部件并运行FutureBuilder

这真的很烦人，因为在某些小部件中，我需要发出一些http请求，并且它们也一直被调用。

这是我的tab1页面，它是一个stateFulWidget



```dart
body:  TabBarView(
        children: <Widget>[
          Tab1Page(),
          Tab2Page(),
```

这是我的调试器的打印记录：

```
Widget build(BuildContext context) {
// TODO: implement build
print("tab1: Builder");
return ScopedModelDescendant<MainModel>(
  builder: (BuildContext context, Widget child, MainModel model) {
    List<SolicitudDto> listadoSolicitudesAprobadas =
        model.obtenerSolicitudesPendientes();

    return Scaffold(
      body: ListView(
        children: <Widget>[
          _buildCards(context, listadoSolicitudesAprobadas)
        ],
      ),
    );
  },
);
```





![enter](https://luckly007.oss-cn-beijing.aliyuncs.com/img/c1eGb.png)

 [相关讨论](javascript:void(0))



------

如果要在TabBarView中保留屏幕状态，可以在State类中使用名为AutomaticKeepAliveClientMixin的mixin类。

此后，您必须重写wantKeepAlive方法并返回true。

我在这里写了一篇关于这个的帖子：https://medium.com/@diegoveloper/flutter-persistent-tab-bars-a26220d322bc

更新

您可以尝试通过这种方式来避免每次切换选项卡时都请求数据

```dart
 //global variable at your state class

  List<SolicitudDto> listadoSolicitudesAprobadas;


  Widget build(BuildContext context) {
  // TODO: implement build
  print("tab1: Builder");
  return ScopedModelDescendant<MainModel>(
    builder: (BuildContext context, Widget child, MainModel model) {
         if (listadoSolicitudesAprobadas == null){
          listadoSolicitudesAprobadas =   model.obtenerSolicitudesPendientes();
        }

      return Scaffold(
        body: ListView(
          children: <Widget>[
            _buildCards(context, listadoSolicitudesAprobadas)
          ],
        ),
      );
    },
  );
```

ttps://medium.com/@diegoveloper/flutter-persistent-tab-bars-a26220d322bc





# Flutter使垂直appBar

在应用程序中，我想在不同页面中创建一个具有自定义标题的垂直appBar：

您可以使用RotatedBox旋转AppBar，但是您将无法使用Scaffold中的AppBar参数，因为该参数需要水平的。



```dart
class VerticalAppBar extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return RotatedBox(
      quarterTurns: 3,
      child: AppBar(
        primary: false,
        title: Text('My Bar'),
      ),
    );
  }
}
```

# Flutter中的文本小部件下的黄线？

https://www.codenong.com/47114639/

# Flutter中GridTile中图像上方的InkVell波纹

我认为这是在图像上显示波纹效果的更好方法。

```dart
Ink.image(
    image: AssetImage('sample.jpg'),
    fit: BoxFit.cover,
    child: InkWell(
        onTap: () {},
    ),
),
```

使用Stack，我们可以将Material和InkWell带到图像上。要拉伸材质，我们将使用Positioned.fill小部件。

```dart
Stack(
  children: <Widget>[
    Image( ... ),
    Positioned.fill(
      child: Material(
        color: Colors.transparent,
        child: InkWell(
          onTap: () { ... },
        ),
      ),
    ),
  ],
);
```



我们创建了这个简单的小部件，以在任何给定孩子的上方绘制墨水反应。

```dart
class InkWrapper extends StatelessWidget {
  final Color splashColor;
  final Widget child;
  final VoidCallback onTap;

  InkWrapper({
    this.splashColor,
    @required this.child,
    @required this.onTap,
  });

  @override
  Widget build(BuildContext context) {
    return Stack(
      children: <Widget>[
        child,
        Positioned.fill(
          child: Material(
            color: Colors.transparent,
            child: InkWell(
              splashColor: splashColor,
              onTap: onTap,
            ),
          ),
        ),
      ],
    );
  }
}
```

- 优秀的方法。即使在AspectRatio下也可以使用。如果可以使用FadeInImage会更好。

```dart
SizedBox(
  height: 200,
  child: Ink(
    decoration: BoxDecoration(
      image: DecorationImage(
        image: ExactAssetImage("chocolate_image"),
        fit: BoxFit.cover,
      ),
    ),
    child: InkWell(
      onTap: () {},
      splashColor: Colors.brown.withOpacity(0.5),
    ),
  ),
)
```



```dart
Material(  
  child : InkWell(          
      child : YourWidget    
  )      
)
```

