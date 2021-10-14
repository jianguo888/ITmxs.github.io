---
title: "Flutter异步延迟执行"
date: 2021-10-14T12:52:29+08:00
draft: false
tags: ["Flutter"]
categories: ["Flutter"]
---

本文是异步编程的延时策略篇章，在Flutter中实现延时操作有两种方式，一种是通过Future，另一种是通过Timer。

# 1 Future

在Flutter中实现延时 1秒的操作，使用Fluture来实现，代码如下：

```dart
    ///代码清单 1-1 
    ///方式一
    ///参数一 延时的时间
    ///参数二 延时执行的方法
    Future.delayed(Duration(milliseconds: 1000), () {
      print("延时1秒执行");
    });


```

```dart
    ///代码清单 1-2
    ///方式二
    Future.delayed(Duration(milliseconds: 1000)).whenComplete((){
      print("延时1秒执行 whenComplete ");
    });

```



```dart
    ///代码清单 1-3
    ///方式三
    Future.delayed(Duration(milliseconds:1000)).then((value){
      print("延时1秒执行 then ");
    });

```

在上述代码清单 1-1、1-2、1-3中分别展示了使用Future的延时操作的三种方式，这三种方式 是小编实现延时操作的一些思路，一般建议使用代码清单1-1中所示的第一种，对于代码清单1-2与1-3中所示的whenComplete与then函数，功能更强大，应当用到合适的位置。

Future的then函数返回值类型为一个Future对象，所以支持链式调用，组合在一起就是串行方式调用，如下代码块 1-4 中所示：

```dart
    ///代码清单 1-4
    Future.delayed(Duration(milliseconds: 1000), () {
      print("延时1秒执行");
      return Future.value("测试数据");
    }).then((value) {//函数一
      print(" then  $value");
      return Future.value("测试数据 2");
    }).then((value) {///函数二
      print(" then  $value");
      return Future.value("测试数据 2");
    }).then((value) {//函数三
      ///value 就是 函数一中回传的值
      print(" then  $value");
    });

```

假如在then函数中任何一个环节出现了异常，那么后续的函数将会被中断执行（如清单1-4中的then函数一出现了问题，then函数二与三就都不会执行）相当于是程序线程停止在这里了，对于手机界面来讲就是无响应或者是红屏显示。

在代码清单1-3中所示的whenComplete方法，并返回一个Future，类似于try-catch-finally中的finally块，所以用whenComplete来结尾多个异步操作是一个合适的解决方案，如下代码清单1-5所示

```dart
/// 代码清单 1-5
    Future.delayed(Duration(milliseconds: 1000), () {
      print("延时1秒执行");
      return Future.value("测试数据");
    }).then((value) {//函数一
      print(" then  $value");
      return Future.value("测试数据 2");
    }).then((value) {///函数二
      print(" then  $value");
      throw 'Error!';
      return Future.value("测试数据 3");
    }).then((value) {//函数三
      ///value 就是 函数一中回传的值
      print(" then  $value");
    }).catchError((err) {
      print('Caught $err'); // Handle the error.
    },test: (e){
      print('Caughte  $e'); // Han
      return e is String;
    }).whenComplete((){
      print("程序执行完成");
    });

```

在代码清单 1-5中也使用到了catchError函数，在这里，当then这几个函数中任何一个处理出现 异常，都会回调此方法，如这里在函数二中通过 throw抛出的一个异常，在catchError函数中捕捉到这个异常，然后回调test方法块，再回调catchError的参数一的函数处理，类似try-catch-finally中的catch，它的定义如下代码清单1-6所示：

```dart
/// 代码清单 1-6
  Future<T> catchError(Function onError, {bool test(Object error)});

```

# 2 Timer

通过Timer来实现延时2秒的操作，如下代码清单 2-1 所示：

```dart
    ///延时2秒
    Timer timer =  new Timer(Duration(milliseconds: 2000), (){

    });

```

相比来讲 Future中实现的延时操作也是通过Timer来实现的，在实际开发中，如果只是一个单纯的延时操作，小编建议使用Timer，如下代码清单2-2所示，在当前Widget销毁时取消延时任务，可避免内存泄漏。

```dart
class _TimerDelayedTestPageState extends State{
  ///声明
  Timer timer;
  @override
  void initState() {
    super.initState();
    ///延时2秒
     timer =  new Timer(Duration(milliseconds: 2000), (){

    });
  }
  @override
  void dispose() {
    ///取消延时任务
    timer.cancel();
    super.dispose();
   }
    ... ... 
 }

```

