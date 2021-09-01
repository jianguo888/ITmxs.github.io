---
title: "Flutter_FloatingActionButton及仿闲鱼的发布按钮"
date: 2021-09-01T09:03:43+08:00
draft: true
---

## 一、FloatingActionButton

运行 Flutter 默认的 Demo 可以发现右下角的有一个加号按钮

这个加号按钮正好在屏幕的右下角的那个位置，就是通过 FloatingActionButton 实现的

`Scalfold` 组件中有下面三个属性：

```dart
    this.floatingActionButton,
    this.floatingActionButtonLocation,
    this.floatingActionButtonAnimator,
```

通过 `floatingActionButton` 可以指定一个 Widget，然后通过 `floatingActionButtonLocation` 可以指定 floatingActionButton 的位置

一个 FloatingActionButton 的简单使用：

```dart
floatingActionButton: FloatingActionButton(
  elevation: 0,
  child: Icon(
    Icons.add,
    size: 30,
  ),
  onPressed: () {},
),
floatingActionButtonLocation: FloatingActionButtonLocation.endFloat,
```

效果：

![42859-vtqyuaxxxlf.png](https://luckly007.oss-cn-beijing.aliyuncs.com/image/2856904662.png)

上面这个示例就是类似于 Flutter Demo 的按钮示例



## 二、模仿闲鱼的发布按钮

> 闲鱼应该是目前国内使用 Flutter 最大的团队吧

![18652-j1dlmp4mekb.png](https://luckly007.oss-cn-beijing.aliyuncs.com/image/635145302.png)

我们只是使用 floatingActionButton 模拟一个这样的发布按钮，闲鱼上，这个 Widget 或者是绘制应该是自己封装的或者是 Native 注册的

首先分析下，实现这样的按钮需要做什么：

- 需要一个 BottomNavigatrionBar
- 需要一个 FloatingActionButton
- 需要将 FloatingActionButton 盖在最中间的 BottomNavigationBar 上



### 1、配置 BottomNavigationBar

```dart
bottomNavigationBar: BottomNavigationBar(
  selectedFontSize: 12,
  unselectedFontSize: 12,
  unselectedItemColor: Colors.black,
  selectedItemColor: Colors.blue,
  items: [
    BottomNavigationBarItem(icon: Icon(Icons.home), title: Text('首页')),
    BottomNavigationBarItem(icon: Icon(Icons.add), title: Text('发布')),
    BottomNavigationBarItem(icon: Icon(Icons.settings), title: Text('设置')),
  ],
),
```

效果：

![70659-u45i6wj66sq.png](https://luckly007.oss-cn-beijing.aliyuncs.com/image/1592574526.png)



### 2、创建一个符合样式的 FloatingActionButton

简单模拟了一下：

- 黄色背景
- 白色边框
- 黑色按钮

```dart
class FloatingActionButtonDemo extends StatelessWidget {
  const FloatingActionButtonDemo({Key key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Container(
      padding: EdgeInsets.all(4),
      margin: EdgeInsets.only(top: 5),
      height: 50,
      width: 50,
      decoration: BoxDecoration(
        borderRadius: BorderRadius.circular(60),
        color: Colors.white,
      ),
      child: FloatingActionButton(
        elevation: 0,
        backgroundColor: Colors.yellow,
        child: Icon(
          Icons.add,
          color: Colors.black,
          size: 30,
        ),
        onPressed: () {},
      ),
    );
  }
}
```

按钮的样式效果：

![37903-7yy8sdm4wox.png](https://luckly007.oss-cn-beijing.aliyuncs.com/image/3043682520.png)



### 3、将 floatingActionButton 放在 bottomNavigationBar 的中间靠上位置

```dart
floatingActionButton: FloatingActionButtonDemo(),
floatingActionButtonLocation: FloatingActionButtonLocation.centerDocked,
```



### 4、效果：

![45830-an32l0l24lq.png](https://luckly007.oss-cn-beijing.aliyuncs.com/image/1425307523.png)



### 5、FloatingActionButton Widget 组件的必要性

从 `Scalfold.floatingActionButton` 支持 Widget 就可以看出，可以配置任何我们需要的 Widget，并且放在需要的位置

比如这个 发布按钮 其实并不一定需要使用 FloatingActionButton ，比如我完全可以使用 Container 直接画出来

```dart
  Widget build(BuildContext context) {
    return Container(
      padding: EdgeInsets.all(4),
      margin: EdgeInsets.only(top: 5),
      height: 50,
      width: 50,
      decoration: BoxDecoration(
        borderRadius: BorderRadius.circular(60),
        color: Colors.yellow,
        border: Border.all(color: Colors.white, width: 2.0)
      ),
      child: Icon(Icons.add, color: Colors.black, size: 30),
    );
  }
```

效果其实一些样：

![57440-5uj4xajnvvn.png](https://luckly007.oss-cn-beijing.aliyuncs.com/image/918624371.png)

如果需要点击，则直接在 Container 外面加一个可点击的 Widget 即可，可点击 Wdiget 可以选择 `InkWell`



## 三、完整代码：

[https://github.com/postbird/FlutterHelloWorldDemo/blob/master/demo1/lib/bak/main.37-FloatingActionButton%26%E9%97%B2%E9%B1%BC.dart](https://github.com/postbird/FlutterHelloWorldDemo/blob/master/demo1/lib/bak/main.37-FloatingActionButton%26闲鱼.dart)
