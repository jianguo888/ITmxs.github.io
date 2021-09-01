---
title: "Flutter组件GridView.count及GridView"
date: 2021-09-01T09:17:57+08:00
draft: true
---

## 一、说明

Flutter 中列表一般有两种表现形式，一种是 ListView 的列表形式，另一种就是 GridView 的宫格列表形式。

当然 GridView 不一定只能做列表，做九宫格的操作菜单也是 OK 的。

目前手机淘宝或者是小红书类的种草类内容基本都会选择 **双列流** 的显示形式，双列流对于内容在聚合页的曝光有很大的作用，Flutter 中可以借助 GridView 实现双列流或者多列流的数据。

`GridView` 有五个可用构造函数：

- `GridView`
- `GridView.count`
- `GridView.builder`
- `GridView.custom`
- `GridView.extent`

这里主要实践 `GridView.count` 和 `GridView.builder` 使用比较多的两个命名构造。



## 二、`GridView.count`



### 1、常用构造函数的命名参数

`GridView.count` 接收下面的命名参数:

其中 `crossAxisCount` 是必传的，用来控制**横轴上子项的个数**，

`mainAxisSpacing` 用来指定横轴上两列的宽度间隙

`mainAxisSpacing` 用来指定纵轴上两行的高度间隙

`childAspectRatio` 则是用来指定卡片的宽高比例

`children` 是一个 `List<Widget>` 类型数据，也是整个 GridView 的列表，使用 `GridView.count` 需要先构造好这个列表

```dart
    Key key,
    Axis scrollDirection = Axis.vertical,
    bool reverse = false,
    ScrollController controller,
    bool primary,
    ScrollPhysics physics,
    bool shrinkWrap = false,
    EdgeInsetsGeometry padding,
    @required int crossAxisCount,
    double mainAxisSpacing = 0.0,
    double crossAxisSpacing = 0.0,
    double childAspectRatio = 1.0,
    bool addAutomaticKeepAlives = true,
    bool addRepaintBoundaries = true,
    bool addSemanticIndexes = true,
    double cacheExtent,
    List<Widget> children = const <Widget>[],
    int semanticChildCount,
    DragStartBehavior dragStartBehavior = DragStartBehavior.start,
```



### 2、构造 GridView.count 的 children 列表

上面提到：

> `children` 是一个 `List<Widget>` 类型数据，也是整个 GridView 的列表，使用 `GridView.count` 需要先构造好这个列表

这里 mock 了一个列表，然后通过一个方法 `_getGridList` 去构造 `List<Widget>` 列表，

```dart
  List _list = List.generate(90, (index) {
    return index;
  });

  // 生成一个列表
  List<Widget> _getGridList() {
    return _list.map((item) {
      return GridViewItem();
    }).toList();
  }
```



### 3、单个卡片内容

`GridViewItem` 是单个卡片内容，这里就随便写个简单的就可以：

```dart
class GridViewItem extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Container(
      child: Text('文本'),
      color: Colors.pink[200],
    );
  }
}
```



### 4、 完整的 GridView 列表内容

> 不列出 GridViewItem 组件代码和页面框架代码，完整代码可以在文章最后找到

```dart
class HomeContent extends StatelessWidget {
  List _list = List.generate(90, (index) {
    return index;
  });

  // 生成一个列表
  List<Widget> _getGridList() {
    return _list.map((item) {
      return GridViewItem();
    }).toList();
  }

  @override
  Widget build(BuildContext context) {
    return GridView.count(
      children: _getGridList(),
      crossAxisCount: 2,
      padding: EdgeInsets.all(10),
      crossAxisSpacing: 20,
      // 水平距离
      mainAxisSpacing: 20,
      // 垂直距离
      childAspectRatio: 3.5/4, // 宽高比例
    );
  }
}
```

最终效果：

![1.gif](https://luckly007.oss-cn-beijing.aliyuncs.com/image/2490855435.gif)



## 三、`GridView.builder` 及 新闻双列流页面



### 1、GridView.builder 构造函数

```dart
  /// Creates a scrollable, 2D array of widgets that are created on demand.
  ///
  /// This constructor is appropriate for grid views with a large (or infinite)
  /// number of children because the builder is called only for those children
  /// that are actually visible.
```

从上面的注释可以看出，和 `LiewView.builder` 一样，`GridView.builder` 能够做到在子项 Widget 显示的时候再去创建，从而提高性能。

与 `GridView.count` 不同的是，`GridView.builder` 控制卡片间隔、比例的几个参数不是直接通过构造函数传参，而是通过 `gridDelegate` 参数传递给 Widget

`final SliverGridDelegate gridDelegate;` `gridDelegate` 是 `SliverGridDelegate `类型，主要是用来控制 GridView 的 子 Widget 的样式啥的

同样的，除了 `gridDelegate` 是 required 之外，`itemBuilder` 也是 required 的，其他的构造参数如下：

```dart
    Key key,
    Axis scrollDirection = Axis.vertical,
    bool reverse = false,
    ScrollController controller,
    bool primary,
    ScrollPhysics physics,
    bool shrinkWrap = false,
    EdgeInsetsGeometry padding,
    @required this.gridDelegate,
    @required IndexedWidgetBuilder itemBuilder,
    int itemCount,
    bool addAutomaticKeepAlives = true,
    bool addRepaintBoundaries = true,
    bool addSemanticIndexes = true,
    double cacheExtent,
    int semanticChildCount,
```



### 2、itemBuilder() 方法

`itemBuilder` 和 ListView.builder 一样，也是一个 `IndexedWidgetBuilder`，接受 context 和 index 两个参数，然后返回一个 Widget 即可

这里使用了 mock 的新闻数据，来自 网易新闻的接口，可以在 https://github.com/postbird/FlutterHelloWorldDemo/blob/master/demo1/lib/mock/list.dart 找到列表数据

完整代码可以在文章最后找到。

```dart
  // builder
  Widget _itemBuilderFunc(BuildContext context, int index) {
    final Map news = newsList.news[index];
    return GridViewItem(
      cover: news['imgurl'],
      title: news['title'],
      time: news['time'].toString(),
    );
  }
```



### 3、单张卡片 Widget

`GridViewItem` 是构造的比较常见的双列流卡片，封面图+标题 的样式

`GridViewItem` 组成如下：

```dart
|- GridView
  |- Container
    |- Column
      |- GridViewTitleCover  // 封面图 Widget
      |- SizedBox 
      |- GridViewItemTitle    // 标题 Widget
      |- GridViewItemTime    // 时间 Widget
```

`GridViewItem` 接收三个参数，`cover` 、 `title` 、 `time`：

```dart
class GridViewItem extends StatelessWidget {
  GridViewItem({this.cover, this.title, this.time});

  String cover;
  String title;
  String time;

  @override
  Widget build(BuildContext context) {
    return Container(
      child: Column(
        children: <Widget>[
          GridViewTitleCover(this.cover),
          SizedBox(height: 10),
          GridViewItemTitle(title: this.title),
          GridViewItemTime(time: this.time)
        ],
      ),
      decoration: BoxDecoration(
          borderRadius: BorderRadius.all(Radius.circular(6)),
          color: Colors.grey[200]),
    );
  }
}
```



#### 1、封面图 Widget

这里有个坑， 上面可以看到，卡片是四边圆角 6，Container 的圆角控制的，但是一旦 Image Widget 覆盖上去了，就会盖住下面的 Container 的圆角，因此 Image 也必须是圆角的，而设置圆角的 Image，这里借助 Container+Image (BoxDecoration) 实现圆角图片

外层卡片的 Container 实现的是四边圆角，而我们的图片只需要上面两个角设置圆角即可： `borderRadius: BorderRadius.vertical(top: Radius.circular(6)),`

```dart
class GridViewTitleCover extends StatelessWidget {
  GridViewTitleCover(this.cover);

  final String cover;

  @override
  Widget build(BuildContext context) {
    return Container(
        height: 150,
        decoration: BoxDecoration(
          borderRadius: BorderRadius.vertical(top: Radius.circular(6)),
          image: DecorationImage(
              image: NetworkImage(this.cover), fit: BoxFit.cover),
        ));
  }
}
```



#### 2、标题 Widget

```dart
class GridViewItemTitle extends StatelessWidget {
  GridViewItemTitle({this.title});

  final String title;

  @override
  Widget build(BuildContext context) {
    return Container(
      child: Text(
        this.title,
        maxLines: 2,
        overflow: TextOverflow.ellipsis,
        style: TextStyle(fontSize: 12),
      ),
      padding: EdgeInsets.fromLTRB(5, 0, 5, 0),
    );
  }
}
```



#### 3、时间 Widget

```dart
class GridViewItemTime extends StatelessWidget {
  GridViewItemTime({this.time});

  final String time;

  @override
  Widget build(BuildContext context) {
    return Container(
      child: Text(
        this.time,
        maxLines: 1,
        style: TextStyle(fontSize: 10, color: Colors.black38),
      ),
      alignment: Alignment.bottomRight,
      padding: EdgeInsets.fromLTRB(5, 5, 5, 0),
    );
  }
}
```



### 4、GridView 列表构造的代码：

```dart
class HomeContent extends StatelessWidget {
  // builder
  Widget _itemBuilderFunc(BuildContext context, int index) {
    final Map news = newsList.news[index];
    return GridViewItem(
      cover: news['imgurl'],
      title: news['title'],
      time: news['time'].toString(),
    );
  }

  // GridView.builder
  @override
  Widget build(BuildContext context) {
    return GridView.builder(
      itemCount: newsList.news.length,
      itemBuilder: this._itemBuilderFunc,
      padding: EdgeInsets.fromLTRB(12, 12, 12, 0),
      gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
          crossAxisCount: 2,
          crossAxisSpacing: 10,
          mainAxisSpacing: 10,
          childAspectRatio: 0.75),
    );
  }
}
```



### 5、双列流新闻列表

![2.gif](http://www.ptbird.cn/usr/uploads/2019/07/1711827857.gif)



## 四、完整代码

GridView.count 实践代码：

- https://github.com/postbird/FlutterHelloWorldDemo/blob/master/demo1/lib/bak/main.13-GridView.dart

GridView.builder 实践代码：

- [https://github.com/postbird/FlutterHelloWorldDemo/blob/master/demo1/lib/bak/main.14-GridView.builder%26%E5%8F%8C%E5%88%97%E6%B5%81.dart](https://github.com/postbird/FlutterHelloWorldDemo/blob/master/demo1/lib/bak/main.14-GridView.builder%26双列流.dart)
