---
title: "Flutter组件InheritedWidget的使用和源码分析"
date: 2021-08-27T08:34:22+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["flutter"]
categories: ["flutter"]
---

## **InheritedWidget**使用



我们先用一个Demo来看看**InheritedWidget**的使用方法。Demo如下，**InheritedWidget**子类**InfoWidget**的`number`数值变化后，底下的三个**InfoChildWidget**显示的`number`也会变化。

![demo](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/537376ec27c34e239dade57609117859~tplv-k3u1fbpfcp-watermark.awebp)

接下来我们来写代码。

- 由于**InheritedWidget**是抽象类，我们创建一个继承 自**InheritedWidget**的**InfoWidget**。

```
class InfoWidget extends InheritedWidget {
  // 1
  final int number;
    
  // 2    
  InfoWidget({Key key, @required this.number, @required child})
      : super(key: key, child: child);
    
  //3    
  @override
  bool updateShouldNotify(InfoWidget oldWidget) {
    return number != oldWidget.number;
  }
  
  // 4
  static InfoWidget of(BuildContext context) {
    return context.dependOnInheritedWidgetOfExactType(); 
  }
  
}

```

代码说明：

1. `number`就是定义的共享的数据；
2. **InfoWidget**的构造函数中，有三个参数除了`key`外，都是必传参数，`number`是外部传入的给**InfoWidget**共享的数据，`child`是**子Widget**；

> - **InheritedWidget**是**Widget**的子类，但是没有**StatefulWidget**类似的**State**，这样**InheritedWidget**的所有属性都是不可变的，所以数据是需要**父Widget**提供的。
> - `child`是**InheritedWidget**的必传参数，所以子类也得是必传参数。

1. **InheritedWidget**的子类需要重写`updateShouldNotify`方法，这个方法如果返回`true`，则会回调**StatefulElement**中**state**的`didChangeDependencies`方法；
2. `of`这个静态方法是留给**子Widget**使用的，**子Widget**可以通过它获取到**InheritedWidget**的共享数据。

> 取`of`方法名是个约定俗成，当然也可以随便取个合法的方法名。

- 建一个**Widget**，它可以显示**InfoWidget**共享的数据

```
class InfoChildWidget extends StatelessWidget {
  
  // 1
  const InfoChildWidget();

  @override
  Widget build(BuildContext context) {
    // 2
    final int number = InfoWidget.of(context).number;
    return Text("$number", style: TextStyle(color: Colors.amber, fontSize: 40));
  }
}

```

1. 使用**InfoChildWidget**的**常量构造函数**是为了解决不必要的重建和销毁。
2. `InfoWidget.of(context)`就是上面提到的给**子Widget**使用的`of`静态方法，然后取到**number**就可以直接显示了。

- 使用

```
InfoWidget(
    number: _number,
        child: Center(
            child: Column(
              mainAxisAlignment: MainAxisAlignment.center,
              children: [
                InfoChildWidget(),
                InfoChildWidget(),
                InfoChildWidget(),
              ],
            ),
        ),
    )

```

使用的时候是将**InfoChildWidget**做为**InfoWidget**的**子Widget**，我这里特意中间加了**Center**和**Column**，就是为了指出**InfoChildWidget**不一定需要是**直接子Widget**。

- 所有代码如下：

```
class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: MyHomePage(title: 'Flutter Demo'),
    );
  }
}

class MyHomePage extends StatefulWidget {
  MyHomePage({Key key, this.title}) : super(key: key);
  final String title;

  @override
  _MyHomePageState createState() => _MyHomePageState();
}

class _MyHomePageState extends State<MyHomePage> {
  int _number = 0;
  void _incrementCounter() {
    _number = Random().nextInt(100);
    setState(() {});
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text(widget.title),
      ),
      body: Center(
        child: InfoWidget(
          number: _number,
          child: Center(
            child: Column(
              mainAxisAlignment: MainAxisAlignment.center,
              children: [
                InfoChildWidget(),
                InfoChildWidget(),
                InfoChildWidget(),
              ],
            ),
          ),
        ),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: _incrementCounter,
        tooltip: 'Increment',
        child: Icon(Icons.add),
      ),
    );
  }
}

<!-- InfoWidget -->
class InfoWidget extends InheritedWidget {
  final int number;

  InfoWidget({Key key, @required this.number, @required child})
      : super(key: key, child: child);

  @override
  bool updateShouldNotify(InfoWidget oldWidget) {
    return number != oldWidget.number;
  }

  static InfoWidget of(BuildContext context) {
    return context.dependOnInheritedWidgetOfExactType();
  }
}

<!-- InfoChildWidget -->
class InfoChildWidget extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    final int number = InfoWidget.of(context).number;
    return Text("$number", style: TextStyle(color: Colors.amber, fontSize: 40));
  }
}

```

效果如下：

![效果](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/63feedb5fe8c4db1b6862495db042356~tplv-k3u1fbpfcp-watermark.awebp)

## **InheritedWidget**源码分析

##### 设置inheritedWidgets

每个**Element**都有一个**key**为**InheritedWidget**类型，值为**InheritedElement**的**Map**属性`_inheritedWidgets`。

```
Map<Type, InheritedElement>? _inheritedWidgets;

```

每个**Widget**生成的**Element**挂载到**Element Tree**上的时候都会调用`mount`方法：

```
<!-- Element -->
void mount(Element? parent, dynamic newSlot) {
    _updateInheritance();
}

```

`mount`方法会调用`_updateInheritance`方法：

```
<!-- Element -->
void _updateInheritance() {
    _inheritedWidgets = _parent?._inheritedWidgets;
}

```

如果不是**InheritedElement**,则`_inheritedWidgets`都指向父**Element**的`_inheritedWidgets`。

```
<!-- InheritedElement -->
void _updateInheritance() {
    final Map<Type, InheritedElement>? incomingWidgets = _parent?._inheritedWidgets;
    if (incomingWidgets != null)
      _inheritedWidgets = HashMap<Type, InheritedElement>.from(incomingWidgets);
    else
      _inheritedWidgets = HashMap<Type, InheritedElement>();
    _inheritedWidgets![widget.runtimeType] = this;
}

```

如果是**InheritedElement**,先拷贝一份父节点的`_inheritedWidgets`, 然后添加或者替换key为**widget.runtimeType**，值为**InheritedElement**的键值对。

> 注意：这里如果父类有相同的**widget.runtimeType**，则会被替换，也就是说如果有多个相同的**InheritedWidget**，子节点的**Element**只能找到离它最近的那个。

![inheritedWidgets](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ac49979e388245608e6a27082461858d~tplv-k3u1fbpfcp-watermark.awebp)

##### **子Element**对**InheritedElement**并添加依赖

我们来看看`of`类方法调用的`dependOnInheritedWidgetOfExactType`方法：

```
<!-- Element -->
Set<InheritedElement>? _dependencies;

T? dependOnInheritedWidgetOfExactType<T extends InheritedWidget>({Object? aspect}) {
    // 1
    final InheritedElement? ancestor = _inheritedWidgets == null ? null : _inheritedWidgets![T];
    if (ancestor != null) {
      // 2
      return dependOnInheritedElement(ancestor, aspect: aspect) as T;
    }
    _hadUnsatisfiedDependencies = true;
    return null;
}

InheritedWidget dependOnInheritedElement(InheritedElement ancestor, { Object? aspect }) {
    _dependencies ??= HashSet<InheritedElement>();
    // 3
    _dependencies!.add(ancestor);
    // 3
    ancestor.updateDependencies(this, aspect);
    return ancestor.widget;
}

```

1. 从`_inheritedWidgets`这个**Map**中找到类型对应的**InheritedElement**；
2. 如果找到则调用`dependOnInheritedElement`方法；`dependOnInheritedElement`方法主要是将**InheritedElement**加入到`_dependencies`这个**Set**中，然后**InheritedElement**调用`updateDependencies`方法把**子Element**加入到`_dependents`中。

```
<!-- InheritedElement -->
void updateDependencies(Element dependent, Object? aspect) {
    setDependencies(dependent, null);
}

void setDependencies(Element dependent, Object? value) {
    _dependents[dependent] = value;
}

final Map<Element, Object?> _dependents = HashMap<Element, Object?>();

```

![updateDependencies](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b5cff7b71cec418f9e184a4792e981e8~tplv-k3u1fbpfcp-watermark.awebp)

##### **InheritedElement**数据变化调用**StatefulElement**的`didChangeDependencies`方法：

**InheritedWidget**调用`build`方法的时候，会调用`notifyClients`方法：

```
void updated(InheritedWidget oldWidget) {
    if (widget.updateShouldNotify(oldWidget))
      super.updated(oldWidget);
}
  
void updated(covariant ProxyWidget oldWidget) {
    notifyClients(oldWidget);
}

```

`notifyClients`方法会对`_dependents`中的每个**子Element**调用`notifyDependent`方法，**子Element**会调用`didChangeDependencies`方法：

```
void notifyClients(InheritedWidget oldWidget) {
    for (final Element dependent in _dependents.keys) {
      notifyDependent(oldWidget, dependent);
    }
}

void notifyDependent(covariant InheritedWidget oldWidget, Element dependent) {
    dependent.didChangeDependencies();
}

```

**子Element**调用`didChangeDependencies`最后会重新构建：

```
void didChangeDependencies() {
    markNeedsBuild();
}

```

当**子Element**为**StateFulElement**时，会将`_didChangeDependencies`置为true；

```
void didChangeDependencies() {
    super.didChangeDependencies();
    _didChangeDependencies = true;
}

```

当重新构建时，**StateFulElement**会调用**state**的`didChangeDependencies`方法。

```
void performRebuild() {
    if (_didChangeDependencies) {
      state.didChangeDependencies();
      _didChangeDependencies = false;
    }
    super.performRebuild();
}

```

![didChangeDependencies](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/12ce9975de014d328fa7e8c7399b7d03~tplv-k3u1fbpfcp-watermark.awebp)

## 总结

**InheritedWidget**传递参数的方案只是把传参从**Constructor**变成了**BuildContext**。但是它还是有些的不完善的地方：

1. 某个类型的**InheritedWidget**只能获取到最近的那一个；
2. 重新构建没法只重构依赖**InheritedWidget**的**子Widget**，性能上不是太好。


