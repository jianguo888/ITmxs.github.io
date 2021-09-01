---
title: "Flutter基于Hero实现页面转场动画"
date: 2021-09-01T08:41:45+08:00
draft: true
---

## 一、Hero Widget

在某些场景下我们可能需要通过图片进行页面的转场，这种场景在大众点评等APP上非常常见，Feed 流中有图片，点击之后，通过图片进行页面的转场

进行转场，就需要两个页面，一个页面的图片触发跳转，另一个页面用来承接这次的跳转

`Hero` Widget 则是在页面中用来关联两个页面的桥梁

当然，这个过程中，在页面返回的时候， 后置页面变成前置页面，`Hero` Widget 则将动画 reverse 执行一次

Hero Widget 构造函数如下：

```dart
  const Hero({
    Key key,
    @required this.tag,
    this.createRectTween,
    this.flightShuttleBuilder,
    this.placeholderBuilder,
    this.transitionOnUserGestures = false,
    @required this.child,
  })
```

其中 `tag` 和 `child` 是 @required 的属性

`tag` 是两个 Hero 的之间相同标识，两个 Hero Widget 通过 tag 关联在一起



## 二、Hero Widget 跳转页面

下面代码中，将 `Hero` 放在了 `GestureDetector` 中，点击的时候打开新的页面。

> 注意，在导航跳转的时候，fullscreenDialog 我设置的属性时 `false`（这里注释了）

`Hero` 的 tag 中使用的是 `imgHero`，这应该是一个唯一的标识，而 child 直接放了一张图片

```dart
GestureDetector(
  child: Hero(
    tag: 'imgHero',
    child: Image.network(
      IMAGE_SRC,
      fit: BoxFit.cover,
      width: 300,
    ),
  ),
  onTap: () {
    Navigator.push(
      context,
      MaterialPageRoute(
        builder: (_) => NewPage(),
        // settings: RouteSettings(isInitialRoute: true),
//                    fullscreenDialog: true,
      ),
    );
  },
)
```



## 三、Hero 承接页面

为了能够承接住之前的页面，在下一跳页面中的 Hero 组件中，也应该设置 tag 是 `imgHero`

```dart
child: GestureDetector(
  child: Hero(
    tag: 'imgHero',
    child: Image.network(
      IMAGE_SRC,
      fit: BoxFit.cover,
    ),
  ),
  onTap: () {
    Navigator.pop(context);
  },
),
```



## 四、效果

![5.gif](http://www.ptbird.cn/usr/uploads/2019/08/276233584.gif)



## 五、各种特殊情况



### 1、如果 Hero 的 tag 不匹配会怎样？

如果两个页面的 Hero 的 tag 不匹配，则无法承接住转场动画，就会变成普通的打开页面

![6.gif](http://www.ptbird.cn/usr/uploads/2019/08/2848841096.gif)



### 2、如果 Hero 的 child 不是图片会怎样？

转场动画的承接与图片是无关的，比如下面代码中使用一个 Container 也没问题

```dart
child: Hero(
  tag: 'imgHero',
  child: Container(
    width: 300,
    height: 300,
    color: Colors.pink,
  ),
)
```

![7.gif](https://luckly007.oss-cn-beijing.aliyuncs.com/image/2900848750.gif)



### 3、两个 Hero 的 child 不一致会怎样？

只要 tag 能够匹配，与 Hero 承载内容不同

效果：

![8.gif](https://luckly007.oss-cn-beijing.aliyuncs.com/image/4126294422.gif)



## 五、完整代码
