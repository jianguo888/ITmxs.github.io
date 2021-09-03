---
title: "InheritedWidget原理"
date: 2021-09-02T20:48:05+08:00
draft: false
tags: ["Flutter"]
categories: ["Flutter"]
---

> 本文主要介绍

<!--more-->

## InheritedWidget介绍

在Flutter进行界面开发时，我们经常会遇到数据传递的问题。由于Flutter采用节点树的方式组织页面，以致于一个普通页面的节点层级会很深。此时，我们如果还是一层层传递数据，当需要获取多层父节点的数据时，会非常麻烦。 因为出现上述问题，Flutter给我我们提供一种`InheritedWidget`，`InheritedWidget`能够让节点下的所有子节点，访问该节点下的数据。 关于`Scoped Model`、`BloC`、`Provider`就是基于`InheritedWidget`实现的。

## InheritedWidget源码分析

可以看到`InheritedWidget`的源码非常简单。

```dart
/// 抽象类，继承自Proxywidget 继承路径InheritedWidget => ProxyWidget => Widget
abstract class InheritedWidget extends ProxyWidget {
  /// 构造函数
  /// 因为InheritedWidget是没有界面的Widget，所有需要传入实际的Widget	
  const InheritedWidget({ Key key, Widget child })
    : super(key: key, child: child);

  /// 重写了超类Widget createElement方法
  @override
  InheritedElement createElement() => InheritedElement(this);

  /// 父级或祖先widget中改变(updateShouldNotify返回true)时会被调用。
  @protected
  bool updateShouldNotify(covariant InheritedWidget oldWidget);
}
```

## InheritedWidget示例

```dart
import 'package:flutter/material.dart';
import 'package:flutter_code/InheritedWidget/InheritedState.dart';

class InheritedCount extends StatefulWidget {
  @override
  _InheritedCountState createState() => _InheritedCountState();
}

class _InheritedCountState extends State<InheritedCount> {

  int _count = 0;

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text("InheritedDemo"),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: () {
          setState(() {
            _count++;
          });
        },
        child: Icon(Icons.add, color: Colors.white,),
      ),
      body: Center(
        child: InheritedState(
            count: _count,
            child: Column(
              mainAxisAlignment: MainAxisAlignment.spaceAround,
              crossAxisAlignment: CrossAxisAlignment.center,
              children: [
                WidgetA(),
                WidgetB()
              ],
            )
        ),
      ),
    );
  }
}

class WidgetA extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Text("widget text");
  }
}

class WidgetB extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Text(InheritedState.of(context)?.count.toString(),
      style: TextStyle(
        color: Colors.green,
        fontSize: 50
      ),  
    );
  }
}
```





```dart
import 'package:flutter/cupertino.dart';
import 'package:flutter/material.dart';

class InheritedState extends InheritedWidget {

  /// 构造方法
  InheritedState({
    Key key,
    @required this.count,
    @required Widget child
  }): assert(count != null),
    super(key:key, child: child);

  /// 需要共享的数据
  final int count;

  /// 获取组件最近当前的InheritedWidget
  static InheritedState of(BuildContext context) {
    return context.dependOnInheritedWidgetOfExactType<InheritedState>();
  }

  /// 通知依赖该树共享数据的子widget
  @override
  bool updateShouldNotify(covariant InheritedState oldWidget) {
    return count != oldWidget.count;
  }

}
```

## InheritedWidget源码分析

在上面的计数器示例代码中，`WidgetB`和`InheritedWidget`发生关联的就是`InheritedState.of(context)?.count.toString()`，其中最关键的方式是`context.dependOnInheritedWidgetOfExactType()`，我们查看`dependOnInheritedWidgetOfExactType()`在`Element`中的源码如下：该代码是在framework.dart 第3960行

```dart
Map<Type, InheritedElement> _inheritedWidgets;

@override
  T dependOnInheritedWidgetOfExactType<T extends InheritedWidget>({Object aspect}) {
   	/// 断言，用于在调试状态下检测是否有正在使用（激活）的祖先
    assert(_debugCheckStateIsActiveForAncestorLookup());
    /// 获取到_inheritedWidgets数组数据
    final InheritedElement ancestor = _inheritedWidgets == null ? null : _inheritedWidgets[T];
    if (ancestor != null) {
      // 断言，判断当前ancestor是否是InheritedElement类型
      assert(ancestor is InheritedElement);
      // 返回并调用更新方法
      return dependOnInheritedElement(ancestor, aspect: aspect) as T;
    }
    _hadUnsatisfiedDependencies = true;
    return null;
  }
```

我们不难看出，每一个`Element`实例都会持有一个`_inheritedWidgets`，调用次用该方法时会从改集合对象中取出相关类型的`InheritedElement`实例，那么在这个方法中我们没有看到设置`_inheritedWidgets`的方法，我们来查看一下`_inheritedWidgets`是如何赋值的。

```dart
// Element  
void _updateInheritance() {
    assert(_active);
    _inheritedWidgets = _parent?._inheritedWidgets;
  }
```

我们找到赋值是在`_updateInheritance`方法中，首先断言当前节点是否激活，然后通过父节点的`_inheritedWidgets`进行赋值，我们继续来看_updateInheritance什么情况下会调用：

```dart
 @mustCallSuper
  void mount(Element parent, dynamic newSlot) {
   	......
    _updateInheritance();
		......
  }

 @mustCallSuper
  void activate() {
    ......
    _updateInheritance();
    ......
  }
```

我们可以看到在`Element`中它在`mount`和`activate`函数执行了调用，也就是说`element`每次挂载和重新时，会调用该方法。那么当该方法执行的时候，`element`就会从上层中拿到所有的`InheritedElement`。而`InheritedElement`他最终继承了`Element`，并可以看到`InheritedElement`重写了`_updateInheritance`方法：

```dart
 @override
  void _updateInheritance() {
    assert(_active);
    final Map<Type, InheritedElement> incomingWidgets = _parent?._inheritedWidgets;
    if (incomingWidgets != null)
      _inheritedWidgets = HashMap<Type, InheritedElement>.from(incomingWidgets);
    else
      _inheritedWidgets = HashMap<Type, InheritedElement>();
    _inheritedWidgets[widget.runtimeType] = this;
  }

```

## InheritedWidget是如何进行刷新的

前面我们分析到`InheritedElement`会拿到父类的所有的`InheritedElment`并向下传递，而`InheritedWidget`正是通过这种方法才能让下面的子`Widget`能访问的上层中所有的`InheritedWidget`，那么它是如何进行刷新的呢？我们在`Element`的`dependOnInheritedWidgetOfExactType`方法中调用了`dependOnInheritedElement`方法，代码如下：

```dart
Set<InheritedElement> _dependencies;

@override
InheritedWidget dependOnInheritedElement(InheritedElement ancestor, { Object aspect }) {
  assert(ancestor != null);
  _dependencies ??= HashSet<InheritedElement>();
  _dependencies.add(ancestor);
  ancestor.updateDependencies(this, aspect);
  return ancestor.widget;
}

@protected
void updateDependencies(Element dependent, Object aspect) {
  setDependencies(dependent, null);
}

@protected
void setDependencies(Element dependent, Object value) {
  _dependents[dependent] = value;
}
```

可以看到`InheritedElement`实例调用自己的`updateDependencies`方法并将当前的`Element`实例传递过去

```dart
 /// Called during build when the [widget] has changed.
  ///
  /// By default, calls [notifyClients]. Subclasses may override this method to
  /// avoid calling [notifyClients] unnecessarily (e.g. if the old and new
  /// widgets are equivalent).
  @protected
  void updated(covariant ProxyWidget oldWidget) {
    notifyClients(oldWidget);
  }

  @override
  void notifyClients(InheritedWidget oldWidget) {
    assert(_debugCheckOwnerBuildTargetExists('notifyClients'));
    for (final Element dependent in _dependents.keys) {
      assert(() {
        // check that it really is our descendant
        Element ancestor = dependent._parent;
        while (ancestor != this && ancestor != null)
          ancestor = ancestor._parent;
        return ancestor == this;
      }());
      // check that it really depends on us
      assert(dependent._dependencies.contains(this));
      notifyDependent(oldWidget, dependent);
    }
  }
}

  @protected
  void notifyDependent(covariant InheritedWidget oldWidget, Element dependent) {
    dependent.didChangeDependencies();
  }
```

由于当`InheritedElement`更新时，会执行`updated`方法，然后继续调用`notifyClients`，遍历所有的`element`并调用`didChangeDependencies`方法。