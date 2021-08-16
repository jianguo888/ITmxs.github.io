---
title: "使用flutter创建响应式下拉导航栏"
date: 2021-08-16T22:15:47+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---



### 让我们创建一个带有自定义悬停效果的导航菜单来查找页面视图。



在这里，我创建了一个仅包含文本的页面，该页面指示我当前移动的页面。为了实现这个设置，我们需要创建一个包含文本列表的列表（在这个程序中创建的字母）。容器必须具有屏幕宽度和高度的大小，创建一个 Text() 小部件并使其居中。创建一个需要在当前创建的 Text() 小部件中使用的文本的构造函数。

PageContainer 

# 实现 NavBar-items 动画：

要实现波浪动画，我们需要 onEnter() 和 onExit() 状态的不同时间线，创建一个应包含 onEnter 和 onExit 状态的鼠标区域容器，并遵循每个状态的持续时间和延迟的代码。

CustomNavigationButton





# 最终设置

让我们设置 MyApp 小部件，现在创建一个包含“主页”、“关于”、“博客”、“程序”、“支持”、“联系方式”的列表。这个 MyApp 小部件返回包含两个 Widget 的堆栈，CustomNavigationButton 作为 Columned 容器内的 List 和 PageContainer 作为 PageView 列表。

**PageView:**
Make the paramater like i mentioned below
scrollDirection: **Axis.vertical**,
controller: **pagecontroller**,

声明页面控制器和上面作为i的代码做了一些变量，用来初始化所述的PageController在INITSTATE（）。

**Open and Close Functionality:** 制作一个定位小部件并将其放置在右角，大小为“7 个列表项”的高度总和（高度：360）。将手势容器作为定位小部件的子项并设置 onTapFunction 像这样

**setState(() {
height == 0 ? height = 300 : height = 0;
isPlaying = !isPlaying;
isPlaying
? controller.forward()
: controller.reverse ();
});

**Animated Icon -setup:**

progress: controller,
color: Colors.white,
icon: AnimatedIcons.menu_close,这些控制器由我们上面创建的 onTap() 函数控制。
最后让那些 poistioned 容器返回一个**CutsomNavigationButton()**列表。



```dart
import 'package:flutter/material.dart';
import 'package:google_fonts/google_fonts.dart';

class DropdownAPP  extends StatefulWidget {
  @override
  _MyAppState createState() => _MyAppState();
}

List navBarItems = ["Home", "About", "Blog", "Programs", "Support", "Contact"];

class _MyAppState extends State<DropdownAPP> with TickerProviderStateMixin {
  PageController pagecontroller;
  AnimationController controller;
  int duration;
  Color color;
  double height = 0;
  bool isPlaying = false;

  @override
  void initState() {
    super.initState();
    pagecontroller = PageController(initialPage: 0);
    controller =
        AnimationController(vsync: this, duration: Duration(milliseconds: 450));
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      backgroundColor: Colors.white,
      body: Container(
        child: Stack(
          children: [
            Container(
              height: MediaQuery.of(context).size.height,
              width: MediaQuery.of(context).size.width,
              child: PageView(
                scrollDirection: Axis.vertical,
                controller: pagecontroller,
                children: navBarItems.map((e) {
                  return PageContainer(e);
                }).toList(),
              ),
            ),
            Positioned(
              right: 80,
              top: 50,
              child: Container(
                height: 360,
                child: Stack(
                  children: [
                    GestureDetector(
                      onTap: () {
                        setState(() {
                          height == 0 ? height = 300 : height = 0;
                          isPlaying = !isPlaying;
                          isPlaying
                              ? controller.forward()
                              : controller.reverse();
                        });
                      },
                      child: Container(
                        height: 50,
                        width: 200,
                        color: Colors.blue,
                        child: Row(
                          children: [
                            AnimatedContainer(
                              duration: Duration(milliseconds: 250),
                              width: height == 300 ? 95 : 0,
                            ),
                            Padding(
                              padding: EdgeInsets.only(left: 15.0),
                              child: Text(
                                height == 300 ? "Close" : "Menu",
                                style: GoogleFonts.nunito(
                                    color: Colors.white,
                                    fontWeight: FontWeight.w700,
                                    fontSize: 21.0),
                              ),
                            ),
                            Spacer(),
                            Align(
                              alignment: Alignment.centerRight,
                              child: AnimatedIcon(
                                progress: controller,
                                color: Colors.white,
                                icon: AnimatedIcons.menu_close,
                              ),
                            ),
                            SizedBox(
                              width: 10.0,
                            ),
                          ],
                        ),
                      ),
                    ),
                    Positioned(
                        top: 50.0,
                        child: AnimatedContainer(
                          duration: Duration(milliseconds: 300),
                          curve: Curves.easeInOut,
                          height: height,
                          width: 200,
                          child: SingleChildScrollView(
                            physics: NeverScrollableScrollPhysics(),
                            child: Column(
                              children: navBarItems.map((e) {
                                return CustomNavigationButton(
                                    text: e,
                                    fun: () {
                                      setState(() {
                                        pagecontroller.animateToPage(
                                            navBarItems.indexOf(e),
                                            duration:
                                                Duration(milliseconds: 300),
                                            curve: Curves.easeInOut);
                                      });
                                    });
                              }).toList(),
                            ),
                          ),
                        ))
                  ],
                ),
              ),
            )
          ],
        ),
      ),
    );
  }
}

class PageContainer extends StatefulWidget {
  final String text;
  PageContainer(this.text);
  @override
  _PageContainerState createState() => _PageContainerState();
}

class _PageContainerState extends State<PageContainer> {
  @override
  Widget build(BuildContext context) {
    return Container(
      height: MediaQuery.of(context).size.height,
      width: MediaQuery.of(context).size.width,
      child: Center(
        child: Text(
          widget.text.toUpperCase(),
          style: GoogleFonts.roboto(
            fontSize: 150,
            fontWeight: FontWeight.w900,
            color: Colors.black26,
          ),
        ),
      ),
    );
  }
}

class CustomNavigationButton extends StatefulWidget {
  final String text;
  final Function fun;
  CustomNavigationButton({this.fun, this.text});
  @override
  _CustomNavigationButtonState createState() => _CustomNavigationButtonState();
}

class _CustomNavigationButtonState extends State<CustomNavigationButton> {
  int duration;
  Color color;
  @override
  Widget build(BuildContext context) {
    return GestureDetector(
      onTap: () {
        widget.fun();
      },
      child: MouseRegion(
        onEnter: (val) {
          setState(() {
            duration = 100;
            color = Colors.blue;
          });
        },
        onExit: (val) {
          setState(() {
            duration = 1200;
            color = Colors.black;
          });
        },
        child: AnimatedContainer(
          duration: Duration(milliseconds: duration ?? 100),
          color: color ?? Colors.black,
          height: 50,
          width: 200,
          child: Center(
            child: Row(
              children: [
                Padding(
                  padding: EdgeInsets.only(left: 15.0),
                  child: Text(
                    widget.text,
                    style: GoogleFonts.nunito(
                        color: Colors.white,
                        fontWeight: FontWeight.w700,
                        fontSize: 21.0),
                  ),
                ),
                Spacer(),
              ],
            ),
          ),
        ),
      ),
    );
  }
}
```

