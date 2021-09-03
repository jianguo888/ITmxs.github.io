---
title: "Flutter动画UI"
date: 2021-08-15T17:44:05+08:00
draft: false
tags: ["Flutter"]
categories: ["Flutter"]
---



1. **Hero Animation**
2. **SlideTransition**
3. **AnimatedPositioned**
4. **AnimatedContainer**

# **Hero Animation**

```dart
import 'package:flutter/material.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Animation',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: HomePage(),
    );
  }
}

class HomePage extends StatefulWidget {
  @override
  _HomePageState createState() => _HomePageState();
}

class _HomePageState extends State<HomePage> {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Flutter Animation'),
      ),
      body: InkWell(
        onTap: () {
          Navigator.push(
              context,
              MaterialPageRoute(
                builder: (context) => DetailPage(),
              ));
        },
        child: Card(
          child: ListTile(
            leading: Hero(
              tag: 'image',
              child: Image.network(
                  'https://i.picsum.photos/id/959/200/200.jpg?hmac=UDtS3dVizIhIBL6FtIOWaQsq6bJgKWm4f_7SYhWoN9o'),
            ),
            title: Hero(
              flightShuttleBuilder: Flight.flightShuttleBuilder,
              tag: 'text',
              child: Text('Animation Title'),
            ),
          ),
        ),
      ),
    );
  }
}

class DetailPage extends StatefulWidget {
  @override
  _DetailPageState createState() => _DetailPageState();
}

class _DetailPageState extends State<DetailPage> {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Card(
        child: Container(
          width: double.infinity,
          height: 300,
          child: Column(
            mainAxisAlignment: MainAxisAlignment.center,
            children: [
              Hero(
                  tag: 'image',
                  child: Image.network(
                      'https://i.picsum.photos/id/959/200/200.jpg?hmac=UDtS3dVizIhIBL6FtIOWaQsq6bJgKWm4f_7SYhWoN9o')),
              Hero(
                flightShuttleBuilder: Flight.flightShuttleBuilder,
                tag: 'text',
                child: Text('Animation Title',style: TextStyle(fontSize: 24),),
              )
            ],
          ),
        ),
      ),
    );
  }
}

class Flight {
  static Widget flightShuttleBuilder(
    BuildContext flightContext,
    Animation<double> animation,
    HeroFlightDirection flightDirection,
    BuildContext fromHeroContext,
    BuildContext toHeroContext,
  ) {
    return DefaultTextStyle(
      style: DefaultTextStyle.of(toHeroContext).style,
      child: toHeroContext.widget,
    );
  }
}
```



# **SlideTransition**



- 要了解 SlideTransition Animation，您需要了解 Animation 类和 Animation Controller。
- 动画类：这是一个我们定义你想要的动画类型的类。在这种情况下偏移。所以代码看起来像：**Animation<Offset>**
- 动画控制器：顾名思义就是动画控制器，比如你需要多少动画，持续时间等。
- 现在可以使用补间动画定义偏移动画。补间动画仅用于两个值之间的动画。像从 0 到 1 的不透明度，从 0 到 1 的偏移。例如。以下

```dart
import 'package:flutter/material.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Animation',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: HomePage(),
    );
  }
}
class HomePage extends StatefulWidget {
  @override
  _HomePageState createState() => _HomePageState();
}
class _HomePageState extends State<HomePage> with SingleTickerProviderStateMixin {
  Animation<Offset> offset;
  AnimationController controller;
  @override
  void initState() {
    //define controller and offset
    controller =
        AnimationController(vsync: this, duration: Duration(milliseconds: 700));
    offset = Tween<Offset>(begin: Offset(0.0, 1.0), end: Offset.zero)
        .animate(controller);
    super.initState();
  }
  @override
  Widget build(BuildContext context) {
    //start animation
    controller.forward();
    return Scaffold(
      appBar: AppBar(title: Text('Flutter Animation'),),
      body: Container(
        height: 250,
            child: Center(
              child: SlideTransition(
                position: offset,
                child: Card(
                  child: Column(
                    children: [
                      Image.network('https://i.picsum.photos/id/237/300/200.jpg?hmac=WSdbBEXvCVSqNN1HnCzm7ohp6DhAJfl9t3TcqBNDn_Q'),
                      SizedBox(height: 10,),
                      Text('Animated Card')
                    ],
                  ),
                ),
              ),
            ),
      ),
    );
  }
}

```



# **AnimatedPositioned**

动画位置需要 Stack 父小部件，它主要用于基于位置的动画小部件。

```dart
import 'package:flutter/material.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Animation',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: HomePage(),
    );
  }
}
class HomePage extends StatefulWidget {
  @override
  _HomePageState createState() => _HomePageState();
}
class _HomePageState extends State<HomePage> {
  double arrowAnimation = -40;

  @override
  void initState() {
    Future.delayed(Duration(milliseconds: 400)).then((value) => setState(() {
      setState(() {
        arrowAnimation = 10;
      });
    }));
    super.initState();
  }
  @override
  Widget build(BuildContext context) {
    //start animation
    return Scaffold(
      appBar: AppBar(title: Text('Flutter Animation'),),
      body: Container(
        height: 250,
            child:  Stack(
              children: [
                AnimatedPositioned(
                  top:40,
                  left: arrowAnimation,
                  duration: Duration(milliseconds: 700),
                  child: Container(
                    child: IconButton(
                        onPressed: () {

                        },
                        icon: Icon(Icons.arrow_back_outlined,size: 30,color: Colors.black,)),
                  ),
                ),

              ],
            ),
      ),
    );
  }
}
```



# **AnimatedContainer**

AnimatedContainer 是 Flutter 应用中常用的动画。

在这个例子中，我们将看到如何为大小设置动画。我有三个动画容器，初始大小为 0，它将动画每个容器大小从 0 到 200,300,400，如下所示。

```dart
import 'package:flutter/material.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Animation',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: HomePage(),
    );
  }
}
class HomePage extends StatefulWidget {
  @override
  _HomePageState createState() => _HomePageState();
}
class _HomePageState extends State<HomePage> {
  double curve1Height = 0;
  double curve2Height = 0;
  double curve3Height = 0;
  bool show = false;
  @override
  Widget build(BuildContext context) {
    if(show){
      curve1Height = 400;
      curve2Height = 300;
      curve3Height = 200;
    }else{
      curve1Height = 0;
      curve2Height = 0;
      curve3Height = 0;
    }
    //start animation
    return Scaffold(
      appBar: AppBar(title: GestureDetector(
          onTap: () {
            show=!show;
            setState(() {});
          },
          child: Text('Flutter Animation')),),
      body: Container(
            child:  Stack(
              children: [
                AnimatedContainer(
                  height: curve1Height,
                  duration: Duration(milliseconds: 500),
                  child: ClipRRect(
                    borderRadius: BorderRadius.only(bottomLeft: Radius.circular(100),bottomRight: Radius.circular(100)),
                    child: Container(
                      color: Colors.green,
                    ),
                  ),
                ),
                AnimatedContainer(
                  height: curve2Height,
                  duration: Duration(milliseconds: 500),
                  child: ClipRRect(
                    borderRadius: BorderRadius.only(bottomLeft: Radius.circular(100),bottomRight: Radius.circular(100)),
                    child: Container(
                      color: Colors.red,
                    ),
                  ),
                ),
                AnimatedContainer(
                  height: curve3Height,
                  duration: Duration(milliseconds: 500),
                  child: ClipRRect(
                    borderRadius: BorderRadius.only(bottomLeft: Radius.circular(100),bottomRight: Radius.circular(100)),
                    child: Container(
                      color: Colors.brown,
                    ),
                  ),
                )
              ],
            ),
      ),
    );
  }
}
```

