---
title: "Flutter制作渐进式网页应用"
date: 2021-10-04T20:24:41+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---

> 本文主要介绍具有自定义导航栏的渐进式 Web 应用程序

# 让我们准备我们的 - “Main.dart”

我们将整个页面分成几个部分，以便于制定，我建议您这样做以获得更好的编程，让我们更详细地查看这些部分，
NavigationBar()、
DashBoard()、
CalendarSpace()，
首先我们可以做导航栏部分

###### lib/Main.dart

```dart
import 'package:flutter/material.dart';
import 'package:praum_project_web_app/CalendarSpace/CalendarSpace.dart';
import 'package:praum_project_web_app/Dashboard/Dashboard.dart';
import 'package:praum_project_web_app/NavigationBar/NavigationBar.dart';


void main() {
  runApp(MaterialApp(home: MyApp()));
}


class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
          body: Container(
        height: MediaQuery.of(context).size.height,
        width: MediaQuery.of(context).size.width,
        child: Stack(
          children: [
            NavigationBar(),
            DashBoard(),
            CalendarSpace(),
          ],
        ),
      ),
    );
  }
}
```

# 深入探讨 - “导航”

制作一个名为“NavigationBar.dart”的 dart 文件，它是公司名称和导航栏的驱动程序文件。将这些项目居中对齐我们使用 Align Widget，它有助于完美地指定元素位置。

###### NavigationBar/NavigationBar.dart

```dart
import 'package:flutter/material.dart';
import 'package:flutter_vector_icons/flutter_vector_icons.dart';
import 'package:praum_project_web_app/NavigationBar/src/CompanyName.dart';
import 'package:praum_project_web_app/NavigationBar/src/NavBar.dart';
import 'package:praum_project_web_app/NavigationBar/src/NavBarItem.dart';


class NavigationBar extends StatefulWidget {
  @override
  _NavigationBarState createState() => _NavigationBarState();
}


class _NavigationBarState extends State<NavigationBar> {
  @override
  Widget build(BuildContext context) {
    return Align(
      alignment: Alignment.centerLeft,
      child: Container(
        height: MediaQuery.of(context).size.height,
        width: 100.0,
        color: Color(0xff333951),
        child: Stack(
          children: [
            CompanyName(),
            Align(
              alignment: Alignment.center,
              child: NavBar(),
            ),
            Align(
              alignment: Alignment.bottomCenter,
              child: NavBarItem(
                icon: Feather.log_out,
                active: false,
              ),
            ),
          ],
        ),
      ),
    );
  }
}
```

创建一个名为 - “src”的目录 - 并创建一个名为 - “CompanyName.dart”的文件 - 创建一个名为 CompanyName 的无状态小部件，它返回 Row() 小部件内的两个“文本”小部件。我们这样做是为了获得我们对“P”和“raum”的不同风格。

###### NavigationBar/src/CompanyName.dart

```dart
import 'package:flutter/material.dart';


class CompanyName extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Container(
      height: 70.0,
      child: Center(
        child: Row(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            Text(
              'P',
              style: TextStyle(
                fontWeight: FontWeight.w700,
                color: Colors.white,
                fontSize: 16.0,
              ),
            ),
            Text(
              'raum',
              style: TextStyle(
                fontWeight: FontWeight.w300,
                color: Colors.white70,
                fontSize: 16.0,
              ),
            ),
          ],
        ),
      ),
    );
  }
}
```

创建一个名为“NavBar.dart”的文件 - 创建一个名为 NavBar 的有状态小部件，它将“NavBar”列表作为 Column() 返回。通过创建列表“Selected”来获取每个项目的动画（如果被选中）。在“ NavBarItem ”部分之后

###### NavigationBar/src/NavBar.dart

```dart
import 'package:flutter/material.dart';
import 'package:flutter_vector_icons/flutter_vector_icons.dart';
import 'package:praum_project_web_app/NavigationBar/src/NavBarItem.dart';


class NavBar extends StatefulWidget {
  @override
  _NavBarState createState() => _NavBarState();
}


class _NavBarState extends State<NavBar> {
  List<bool> selected = [true, false, false, false, false];
  void select(int n) {
    for (int i = 0; i < 5; i++) {
      if (i != n) {
        selected[i] = false;
      } else {
        selected[i] = true;
      }
    }
  }


  @override
  Widget build(BuildContext context) {
    return Container(
      height: 350.0,
      child: Column(
        children: [
          NavBarItem(
            icon: Feather.home,
            active: selected[0],
            touched: () {
              setState(() {
                select(0);
              });
            },
          ),
          NavBarItem(
            icon: Feather.list,
            active: selected[1],
            touched: () {
              setState(() {
                select(1);
              });
            },
          ),
          NavBarItem(
            icon: Feather.folder,
            active: selected[2],
            touched: () {
              setState(() {
                select(2);
              });
            },
          ),
          NavBarItem(
            icon: Feather.message_square,
            active: selected[3],
            touched: () {
              setState(() {
                select(3);
              });
            },
          ),
          NavBarItem(
            icon: Feather.settings,
            active: selected[4],
            touched: () {
              setState(() {
                select(4);
              });
            },
          ),
        ],
      ),
    );
  }
}
```

正如我们在上面的程序中看到的，我们可以假设，这些是按钮，所以我们可以使用 - “InkWell” - 小部件，它具有 Ontap() 函数，它被包裹着 - “材料”小部件，并取消材料小部件的默认颜色我们手动使颜色透明。当构造函数获得活动的 bool 变量值时，我们可以使用它来为主体 - “AnimatedContainer”设置动画，就像我在下面的代码中所做的那样。

###### NavigationBar/src/NavBarItems.dart

```dart
import 'package:flutter/material.dart';


class NavBarItem extends StatefulWidget {
  final IconData icon;
  final Function touched;
  final bool active;
  NavBarItem({
    this.icon,
    this.touched,
    this.active,
  });
  @override
  _NavBarItemState createState() => _NavBarItemState();
}


class _NavBarItemState extends State<NavBarItem> {
  @override
  Widget build(BuildContext context) {
    return Material(
      color: Colors.transparent,
      child: InkWell(
        onTap: () {
          print(widget.icon);
          widget.touched();
        },
        splashColor: Colors.white,
        hoverColor: Colors.white12,
        child: Container(
          padding: EdgeInsets.symmetric(vertical: 3.0),
          child: Row(
            children: [
              Container(
                height: 60.0,
                width: 80.0,
                child: Row(
                  children: [
                    AnimatedContainer(
                      duration: Duration(milliseconds: 475),
                      height: 35.0,
                      width: 5.0,
                      decoration: BoxDecoration(
                        color: widget.active ? Colors.white : Colors.transparent,
                        borderRadius: BorderRadius.only(
                          topRight: Radius.circular(10.0),
                          bottomRight: Radius.circular(10.0),
                        ),
                      ),
                    ),
                    Padding(
                      padding: EdgeInsets.only(left: 30.0),
                      child: Icon(
                        widget.icon,
                        color: widget.active ? Colors.white : Colors.white54,
                        size: 19.0,
                      ),
                    ),
                  ],
                ),
              ),
            ],
          ),
        ),
      ),
    );
  }
}
```

# 让我们留意 - “仪表板”

此状态窗口小部件可帮助您维护宽高比，并充当标签，SharedFilesItem，ProjectStatisticScards的驱动程序小部件。

###### Dashboard/Dashboard.dart

```dart
import 'package:flutter/material.dart';
import 'package:flutter_vector_icons/flutter_vector_icons.dart';
import 'package:google_fonts/google_fonts.dart';
import 'package:percent_indicator/circular_percent_indicator.dart';
import 'package:praum_project_web_app/Dashboard/src/ProjectProgressCard.dart';
import 'package:praum_project_web_app/Dashboard/src/ProjectStatisticsCards.dart';
import 'package:praum_project_web_app/Dashboard/src/SharedFilesItem.dart';
import 'package:praum_project_web_app/Dashboard/src/SubHeader.dart';
import 'package:praum_project_web_app/Dashboard/src/Tabs.dart';


class DashBoard extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Positioned(
      left: 100.0,
      child: Container(
        height: MediaQuery.of(context).size.height,
        width: MediaQuery.of(context).size.width * 0.63,
        color: Colors.white,
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: [
            Container(
              margin: EdgeInsets.only(left: 30.0, top: 25.0, bottom: 10.0),
              child: Text(
                'Ongoing Projects',
                style: GoogleFonts.quicksand(
                  fontWeight: FontWeight.bold,
                  fontSize: 20.0,
                ),
              ),
            ),
            Tabs(),
            Container(
              margin: EdgeInsets.only(top: 5.0),
              height: 200.0,
              width: MediaQuery.of(context).size.width * 0.62,
              child: Row(
                mainAxisAlignment: MainAxisAlignment.spaceEvenly,
                crossAxisAlignment: CrossAxisAlignment.center,
                children: [
                  ProjectProgressCard(
                    color: Color(0xffFF4C60),
                    projectName: 'The Maptrix',
                    percentComplete: '34%',
                    progressIndicatorColor: Colors.redAccent[100],
                    icon: Feather.moon,
                  ),
                  ProjectProgressCard(
                    color: Color(0xff6C6CE5),
                    projectName: 'Delivery Club',
                    percentComplete: '78%',
                    progressIndicatorColor: Colors.blue[200],
                    icon: Feather.truck,
                  ),
                  ProjectProgressCard(
                    color: Color(0xffFAAA1E),
                    projectName: 'Travel Comrode',
                    percentComplete: '82%',
                    progressIndicatorColor: Colors.amber[200],
                    icon: Icons.local_airport,
                  ),
                ],
              ),
            ),
            SubHeader(
              title: 'Shared Files',
            ),
            SharedFilesItem(
              color: Colors.blue,
              sharedFileName: 'Company Guidelines',
              members: '28 members',
              et: '10 Oct 2019',
              fileSize: '2.3 MB',
            ),
            SharedFilesItem(
              color: Colors.amber,
              sharedFileName: 'Company Policy',
              members: '30 members',
              et: '27 Sep 2019',
              fileSize: '4.2 MB',
            ),
            SharedFilesItem(
              color: Colors.red,
              sharedFileName: 'Wireframes',
              members: '14 members',
              et: '08 Oct 2019',
              fileSize: '1.6 MB',
            ),
            SubHeader(
              title: 'Project Statistics',
            ),
            ProjectStatisticsCards(),
          ],
        ),
      ),
    );
  }
}
```

我们正在使用这个小部件来获取具有不同颜色的不同项目名称的进度条。我们使用 - “MouseRegion” - 小部件来更新小部件大小以使其看起来更好。

###### Dashboard/src/ProjectProgressCard.dart

```dart
import 'package:flutter/material.dart';
import 'package:flutter_vector_icons/flutter_vector_icons.dart';
import 'package:google_fonts/google_fonts.dart';


class ProjectProgressCard extends StatefulWidget {
  final Color color;
  final Color progressIndicatorColor;
  final String projectName;
  final String percentComplete;
  final IconData icon;
  ProjectProgressCard({
    this.color,
    this.progressIndicatorColor,
    this.percentComplete,
    this.projectName,
    this.icon,
  });
  @override
  _ProjectProgressCardState createState() => _ProjectProgressCardState();
}


class _ProjectProgressCardState extends State<ProjectProgressCard> {
  bool hovered = false;
  @override
  Widget build(BuildContext context) {
    return MouseRegion(
      onEnter: (value) {
        setState(() {
          hovered = true;
        });
      },
      onExit: (value) {
        setState(() {
          hovered = false;
        });
      },
      child: AnimatedContainer(
        duration: Duration(milliseconds: 275),
        height: hovered ? 160.0 : 155.0,
        width: hovered ? 200.0 : 195.0,
        decoration: BoxDecoration(
            color: hovered ? widget.color : Colors.white,
            borderRadius: BorderRadius.circular(15.0),
            boxShadow: [
              BoxShadow(
                color: Colors.black12,
                blurRadius: 20.0,
                spreadRadius: 5.0,
              ),
            ]),
        child: Center(
          child: Column(
            children: [
              SizedBox(
                height: 20.0,
              ),
              Row(
                children: [
                  SizedBox(
                    width: 18.0,
                  ),
                  Container(
                    height: 30.0,
                    width: 30.0,
                    child: Icon(
                      widget.icon,
                      color: !hovered ? Colors.white : Colors.black,
                      size: 16.0,
                    ),
                    decoration: BoxDecoration(
                      borderRadius: BorderRadius.circular(30.0),
                      color: hovered ? Colors.white : Colors.black,
                    ),
                  ),
                  SizedBox(
                    width: 13.0,
                  ),
                  Container(
                    child: Text(
                      widget.projectName,
                      style: GoogleFonts.quicksand(
                        fontWeight: FontWeight.w500,
                        fontSize: 14.0,
                        color: hovered ? Colors.white : Colors.black,
                      ),
                    ),
                  ),
                ],
              ),
              SizedBox(
                height: 15.0,
              ),
              Row(
                children: [
                  SizedBox(
                    width: 18.0,
                  ),
                  Container(
                    height: 13.0,
                    width: 13.0,
                    child: Icon(
                      Feather.user,
                      size: 13.0,
                      color: hovered ? Colors.white : Colors.black,
                    ),
                  ),
                  SizedBox(
                    width: 8.0,
                  ),
                  Container(
                    child: Text(
                      '5 members',
                      style: GoogleFonts.quicksand(
                        fontWeight: FontWeight.w500,
                        fontSize: 10.0,
                        color: hovered ? Colors.white : Colors.black,
                      ),
                    ),
                  ),
                ],
              ),
              SizedBox(
                height: 5.0,
              ),
              Row(
                children: [
                  SizedBox(
                    width: 18.0,
                  ),
                  Container(
                    height: 13.0,
                    width: 13.0,
                    child: Icon(
                      Feather.clock,
                      size: 13.0,
                      color: hovered ? Colors.white : Colors.black,
                    ),
                  ),
                  SizedBox(
                    width: 8.0,
                  ),
                  Container(
                    child: Text(
                      '15 Nov 2019',
                      style: GoogleFonts.quicksand(
                        fontWeight: FontWeight.w500,
                        fontSize: 10.0,
                        color: hovered ? Colors.white : Colors.black,
                      ),
                    ),
                  ),
                ],
              ),
              Container(
                margin: EdgeInsets.only(top: 8.0, left: 135.0),
                child: Text(
                  widget.percentComplete,
                  style: GoogleFonts.quicksand(
                    fontWeight: FontWeight.w500,
                    fontSize: 12.5,
                    color: hovered ? Colors.white : Colors.black,
                  ),
                ),
              ),
              AnimatedContainer(
                duration: Duration(milliseconds: 275),
                margin: EdgeInsets.only(top: 5.0),
                height: 6.0,
                width: 160.0,
                decoration: BoxDecoration(
                  color: hovered
                      ? widget.progressIndicatorColor
                      : Color(0xffF5F6FA),
                  borderRadius: BorderRadius.circular(20.0),
                ),
                child: Align(
                  alignment: Alignment.centerLeft,
                  child: AnimatedContainer(
                    duration: Duration(milliseconds: 275),
                    height: 6.0,
                    width:
                        (double.parse(widget.percentComplete.substring(0, 1)) /
                                10) *
                            160.0,
                    decoration: BoxDecoration(
                      color: hovered ? Colors.white : widget.color,
                      borderRadius: BorderRadius.circular(20.0),
                    ),
                  ),
                ),
              ),
            ],
          ),
        ),
      ),
    );
  }
}
```

这是共享文件部分，它在 Row() 中包含图标、文本和其他一些数据，正如您在给定代码中看到的那样，这非常简单

###### Dashboard/src/SharedFileItem.dart

```dart
import 'package:flutter/material.dart';
import 'package:google_fonts/google_fonts.dart';


class SharedFilesItem extends StatefulWidget {
  final String sharedFileName;
  final Color color;
  final String members;
  final String et;
  final String fileSize;


  SharedFilesItem({
    this.color,
    this.et,
    this.fileSize,
    this.members,
    this.sharedFileName,
  });


  @override
  _SharedFilesItemState createState() => _SharedFilesItemState();
}


class _SharedFilesItemState extends State<SharedFilesItem> {
  bool hovered = false;
  @override
  Widget build(BuildContext context) {
    return MouseRegion(
      onEnter: (value) {
        setState(() {
          hovered = true;
        });
      },
      onExit: (value) {
        setState(() {
          hovered = false;
        });
      },
      child: AnimatedContainer(
        duration: Duration(milliseconds: 275),
        margin: EdgeInsets.only(bottom: 10.0, left: 40.0, right: 15.0),
        padding: EdgeInsets.all(10.0),
        decoration: BoxDecoration(
            color: Colors.white,
            borderRadius: BorderRadius.circular(10.0),
            boxShadow: hovered
                ? [
                    BoxShadow(
                      color: Colors.black12,
                      blurRadius: 13.0,
                      spreadRadius: 0.0,
                    ),
                  ]
                : []),
        child: Column(
          children: [
            Container(
              child: Row(
                mainAxisAlignment: MainAxisAlignment.spaceBetween,
                children: [
                  Row(
                    children: [
                      SizedBox(
                        width: 15.0,
                      ),
                      Container(
                        height: 28.0,
                        width: 28.0,
                        decoration: BoxDecoration(
                          color: widget.color.withOpacity(0.2),
                          borderRadius: BorderRadius.circular(5.0),
                        ),
                        child: Center(
                          child: Icon(
                            Icons.folder,
                            color: widget.color,
                            size: 15.0,
                          ),
                        ),
                      ),
                      SizedBox(
                        width: 15.0,
                      ),
                      Text(
                        widget.sharedFileName,
                        style: GoogleFonts.quicksand(
                          fontWeight: FontWeight.bold,
                          fontSize: 12.0,
                        ),
                      ),
                    ],
                  ),
                  Row(
                    mainAxisAlignment: MainAxisAlignment.spaceAround,
                    children: [
                      Padding(
                        padding: EdgeInsets.symmetric(horizontal: 30.0),
                        child: Text(
                          widget.members,
                          style: GoogleFonts.quicksand(
                            fontWeight: FontWeight.bold,
                            fontSize: 11.0,
                            color: Colors.black45,
                          ),
                        ),
                      ),
                      Padding(
                        padding: EdgeInsets.symmetric(horizontal: 30.0),
                        child: Text(
                          widget.et,
                          style: GoogleFonts.quicksand(
                            fontWeight: FontWeight.bold,
                            fontSize: 11.0,
                            color: Colors.black45,
                          ),
                        ),
                      ),
                      Padding(
                        padding: EdgeInsets.symmetric(horizontal: 30.0),
                        child: Text(
                          widget.fileSize,
                          style: GoogleFonts.quicksand(
                            fontWeight: FontWeight.bold,
                            fontSize: 11.0,
                            color: Colors.black87,
                          ),
                        ),
                      ),
                    ],
                  ),
                ],
              ),
            ),
          ],
        ),
      ),
    );
  }
}
```

共享文件下方的下一个是项目统计信息，它可以由圆角矩形容器创建，并创建一个包含文本 1、2、3 的列，并用 Row() 将其包裹起来，并添加如下所示的 ProgressIndicator，

###### Dashboard/src/ProjectStatisticsCards.dart

```dart
import 'package:flutter/material.dart';
import 'package:google_fonts/google_fonts.dart';
import 'package:percent_indicator/circular_percent_indicator.dart';


class ProjectStatisticsCards extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Row(
      children: [
        ProjectStatisticsCard(
          count: '125',
          name: 'All finished projects',
          descriptions: '2 projects out of time',
          progress: 0.75,
          progressString: '75%',
          color: Color(0xffFAAA1E),
        ),
        ProjectStatisticsCard(
          color: Color(0xff6C6CE5),
          count: '1105',
          name: 'Customer interest',
          descriptions: '+ 576 new clients',
          progress: 0.68,
          progressString: '68%',
        ),
      ],
    );
  }
}


class ProjectStatisticsCard extends StatelessWidget {
  final String count;
  final String name;
  final String descriptions;
  final double progress;
  final String progressString;
  final Color color;


  ProjectStatisticsCard({
    this.count,
    this.descriptions,
    this.name,
    this.progress,
    this.progressString,
    this.color,
  });


  @override
  Widget build(BuildContext context) {
    return Expanded(
      child: Container(
        margin: EdgeInsets.only(left: 40.0, right: 20.0),
        padding: EdgeInsets.symmetric(horizontal: 20.0),
        height: 85.0,
        decoration: BoxDecoration(
          color: color,
          borderRadius: BorderRadius.circular(8.0),
        ),
        child: Row(
          mainAxisAlignment: MainAxisAlignment.spaceBetween,
          children: [
            Container(
              child: Column(
                crossAxisAlignment: CrossAxisAlignment.start,
                mainAxisAlignment: MainAxisAlignment.center,
                children: [
                  Text(
                    count,
                    style: GoogleFonts.quicksand(
                      fontSize: 20.0,
                      fontWeight: FontWeight.bold,
                      color: Colors.white,
                    ),
                  ),
                  Text(
                    name,
                    style: GoogleFonts.quicksand(
                      fontSize: 13.0,
                      fontWeight: FontWeight.w500,
                      color: Colors.white,
                    ),
                  ),
                  SizedBox(
                    height: 8.0,
                  ),
                  Text(
                    descriptions,
                    style: GoogleFonts.quicksand(
                      fontSize: 10.0,
                      color: Colors.white,
                    ),
                  ),
                ],
              ),
            ),
            CircularPercentIndicator(
              radius: 55.0,
              lineWidth: 4.5,
              percent: progress,
              circularStrokeCap: CircularStrokeCap.round,
              center: Text(
                progressString,
                style: GoogleFonts.quicksand(
                  fontSize: 13.0,
                  fontWeight: FontWeight.w700,
                  color: Colors.white,
                ),
              ),
              progressColor: Colors.white,
              startAngle: 270,
              backgroundColor: Colors.white54,
            ),
          ],
        ),
      ),
    );
  }
}
```

我们已经放下了将在 SubHeader() 组件中完成的每个部分的标题。我们需要用两个 Text 小部件实现一个 Row()。

**注意：**
我们需要使 mainAxisAlignment - SpacedBetween 和 CrossAxisAlignment - Center

###### Dashboard/src/SubHeader.dart

```dart
import 'package:flutter/material.dart';
import 'package:google_fonts/google_fonts.dart';


class SubHeader extends StatelessWidget {
  final String title;
  SubHeader({
    this.title,
  });
  @override
  Widget build(BuildContext context) {
    return Container(
      margin: EdgeInsets.only(
        left: 30.0,
        right: 30.0,
        top: 5.0,
        bottom: 15.0,
      ),
      child: Row(
        mainAxisAlignment: MainAxisAlignment.spaceBetween,
        crossAxisAlignment: CrossAxisAlignment.center,
        children: [
          Text(
            title,
            style: GoogleFonts.quicksand(
              fontWeight: FontWeight.bold,
              fontSize: 14.0,
            ),
          ),
          Text(
            'View All',
            style: GoogleFonts.quicksand(
              fontWeight: FontWeight.bold,
              fontSize: 10.0,
              color: Colors.black45,
            ),
          ),
        ],
      ),
    );
  }
}
```

最后，我们可以完成中间部分或 DashBoard ，标签命名为 - “全部”、“当前”、“待定”和“已分类”。这与往常一样带有 4 个文本小部件的行。

###### Dashboard/src/Tabs.dart

```dart
import 'package:flutter/material.dart';
import 'package:google_fonts/google_fonts.dart';


class Tabs extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Container(
      margin: EdgeInsets.only(left: 32.0),
      child: Row(
        children: [
          Text(
            'All',
            style: GoogleFonts.quicksand(
              fontSize: 12.0,
            ),
          ),
          SizedBox(
            width: 15.0,
          ),
          Container(
            height: 25.0,
            width: 70.0,
            decoration: BoxDecoration(
              color: Colors.black,
              borderRadius: BorderRadius.circular(20.0),
            ),
            child: Center(
              child: Text(
                'Current',
                style: GoogleFonts.quicksand(
                  fontSize: 12.0,
                  color: Colors.white,
                ),
              ),
            ),
          ),
          SizedBox(
            width: 15.0,
          ),
          Text(
            'Pending',
            style: GoogleFonts.quicksand(
              fontSize: 12.0,
            ),
          ),
          SizedBox(
            width: 15.0,
          ),
          Text(
            'Categorized',
            style: GoogleFonts.quicksand(
              fontSize: 12.0,
            ),
          ),
        ],
      ),
    );
  }
}
```

# 使用“CalenderSpace”完成 webApp

让我们为日历部分创建一个驱动程序代码，其中一个 Column 包含三个有状态小部件，即
TopContainer()
CalendarSection()、
MeetingsSection()、

###### CalenderSpace/CalenderSpace.dart

```dart
import 'package:flutter/material.dart';
import 'package:praum_project_web_app/CalendarSpace/src/CalendarSection.dart';
import 'package:praum_project_web_app/CalendarSpace/src/MeetingsSection.dart';
import 'package:praum_project_web_app/CalendarSpace/src/TopContainer.dart';


class CalendarSpace extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Align(
      alignment: Alignment.centerRight,
      child: Container(
        color: Color(0xffF7F7FF),
        height: MediaQuery.of(context).size.height,
        width: MediaQuery.of(context).size.width * 0.28,
        child: Column(
          children: [
            SizedBox(
              height: 30.0,
            ),
            TopContainer(),
            CalendarSection(),
            MeetingsSection(),
            ClipRRect(
              child: Image.asset('assets/image.png',height: 300.0,width: 400.0,),
            ),
          ],
        ),
      ),
    );
  }
}
```

像我一样为周名和日期创建一个两个列表，然后创建一个容器（四舍五入），将这两个列表保存为一个列（）。使阴影看起来不错

###### CalenderSpace/src/CalendarPellet .dart

```dart
import 'package:flutter/material.dart';
import 'package:google_fonts/google_fonts.dart';


List<int> dates = [21, 22, 23, 24, 25, 26, 27];
List<String> days = ['M', 'T', 'W', 'T', 'F', 'S', 'S'];


class CalendarPellet extends StatelessWidget {
  final int date;
  final String day;


  CalendarPellet({
    this.date,
    this.day,
  });


  @override
  Widget build(BuildContext context) {
    return Container(
      height: 45.0,
      width: 25.0,
      margin: EdgeInsets.symmetric(horizontal: 5.0),
      decoration: BoxDecoration(
          color: (date == 24) ? Colors.amber : Colors.white,
          borderRadius: BorderRadius.circular(20.0),
          boxShadow: [
            BoxShadow(
              blurRadius: 7.5,
              spreadRadius: 1.0,
              color: Colors.black12,
            ),
          ]),
      child: Column(
        mainAxisAlignment: MainAxisAlignment.center,
        children: [
          Text(
            date.toString(),
            style: GoogleFonts.quicksand(
              fontSize: 11.0,
              fontWeight: FontWeight.w600,
              color: (date == 24) ? Colors.white : Colors.black,
            ),
          ),
          SizedBox(
            height: 2.0,
          ),
          Text(
            day,
            style: GoogleFonts.quicksand(
              fontSize: 11.0,
              fontWeight: FontWeight.w600,
              color: (date == 24) ? Colors.white : Colors.black38,
            ),
          ),
        ],
      ),
    );
  }
}
```

将压延颗粒导入一排容器中。

###### CalenderSpace/src/CalendarSection.dart

```dart
import 'package:flutter/material.dart';
import 'package:google_fonts/google_fonts.dart';
import 'package:praum_project_web_app/CalendarSpace/src/CalendarPellet.dart';


class CalendarSection extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Container(
      child: Column(
        children: [
          Container(
              margin: EdgeInsets.only(top: 30.0, left: 30.0, right: 30.0),
              child: Row(
                mainAxisAlignment: MainAxisAlignment.spaceBetween,
                children: [
                  Text(
                    'Oct 2019',
                    style: GoogleFonts.quicksand(
                      fontSize: 12.0,
                      fontWeight: FontWeight.w600,
                    ),
                  ),
                  Row(
                    children: [
                      Container(
                        height: 18.0,
                        width: 18.0,
                        decoration: BoxDecoration(
                          color: Colors.black26,
                          borderRadius: BorderRadius.circular(10.0),
                        ),
                        child: Center(
                          child: Icon(
                            Icons.chevron_left,
                            size: 14.0,
                            color: Colors.white,
                          ),
                        ),
                      ),
                      SizedBox(
                        width: 5.0,
                      ),
                      Container(
                        height: 18.0,
                        width: 18.0,
                        decoration: BoxDecoration(
                          color: Colors.black26,
                          borderRadius: BorderRadius.circular(10.0),
                        ),
                        child: Center(
                          child: Icon(
                            Icons.chevron_right,
                            size: 14.0,
                            color: Colors.white,
                          ),
                        ),
                      ),
                    ],
                  ),
                ],
              ),
            ),
            Container(
              padding: EdgeInsets.only(
                  left: 25.0, top: 15.0, right: 25.0, bottom: 25.0),
              child: Row(
                children: dates
                    .map(
                      (e) => CalendarPellet(
                        date: e,
                        day: days[dates.indexOf(e)],
                      ),
                    )
                    .toList(),
              ),
            ),
        ],
      ),
    );
  }
}
```

在这个会议部分中，我们需要实现一个 Stack 来使三个轮廓圆形容器一个在另一个之上，我们可以使用 Positioned 小部件将它们一个个放在另一个之上。

###### CalenderSpace/src/MeetingsSection.dart

```dart
import 'package:flutter/material.dart';
import 'package:google_fonts/google_fonts.dart';
import 'package:praum_project_web_app/Dashboard/src/SubHeader.dart';


class MeetingsSection extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Container(
      child: Column(
        children: [
          SubHeader(
            title: 'Meetings',
          ),
          Container(
            height: 100.0,
            width: double.infinity,
            margin: EdgeInsets.symmetric(horizontal: 30.0),
            decoration: BoxDecoration(
              color: Colors.white,
              borderRadius: BorderRadius.circular(10.0),
            ),
            child: Row(
              children: [
                Container(
                  width: 10.0,
                  decoration: BoxDecoration(
                    color: Colors.red.withOpacity(0.8),
                    borderRadius: BorderRadius.only(
                      topLeft: Radius.circular(10.0),
                      bottomLeft: Radius.circular(10.0),
                    ),
                  ),
                ),
                Container(
                  width: MediaQuery.of(context).size.width * 0.27 - 60.0,
                  padding: EdgeInsets.only(left: 15.0, top: 10.0, right: 15.0),
                  child: Column(
                    crossAxisAlignment: CrossAxisAlignment.start,
                    children: [
                      Row(
                        mainAxisAlignment: MainAxisAlignment.spaceBetween,
                        children: [
                          Text(
                            'Projects Overview',
                            style: GoogleFonts.quicksand(
                              fontSize: 13.0,
                              fontWeight: FontWeight.bold,
                            ),
                          ),
                          Icon(
                            Icons.more_horiz,
                            size: 20.0,
                            color: Colors.black26,
                          ),
                        ],
                      ),
                      SizedBox(width: 5.0),
                      Text(
                        '09 AM - 10 AM',
                        style: GoogleFonts.quicksand(
                          fontSize: 9.0,
                        ),
                      ),
                      Container(
                        padding: EdgeInsets.only(top: 13.0),
                        height: 50.0,
                        child: Stack(
                          children: [
                            Positioned(
                              left: 0.0,
                              child: Container(
                                height: 30.0,
                                width: 30.0,
                                decoration: BoxDecoration(
                                  color: Colors.red,
                                  borderRadius: BorderRadius.circular(20.0),
                                  border: Border.all(
                                    color: Colors.white,
                                  ),
                                ),
                              ),
                            ),
                            Positioned(
                              left: 20.0,
                              child: Container(
                                height: 30.0,
                                width: 30.0,
                                decoration: BoxDecoration(
                                  color: Colors.red,
                                  borderRadius: BorderRadius.circular(20.0),
                                  border: Border.all(
                                    color: Colors.white,
                                  ),
                                ),
                              ),
                            ),
                            Positioned(
                              left: 40.0,
                              child: Container(
                                height: 30.0,
                                width: 30.0,
                                decoration: BoxDecoration(
                                  color: Colors.grey[800],
                                  borderRadius: BorderRadius.circular(20.0),
                                  border: Border.all(
                                    color: Colors.white,
                                  ),
                                ),
                                child: Icon(
                                  Icons.add,
                                  size: 15.0,
                                  color: Colors.white,
                                ),
                              ),
                            ),
                          ],
                        ),
                      ),
                    ],
                  ),
                ),
              ],
            ),
          ),
        ],
      ),
    );
  }
}
```

最后，我们需要制作日历部分的顶部容器，其中包含圆形头像、搜索图标和铃铛图标。

###### CalenderSpace/src/TopContainer.dart

```dart
import 'package:flutter/material.dart';
import 'package:flutter_vector_icons/flutter_vector_icons.dart';


class TopContainer extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Container(
      padding: EdgeInsets.symmetric(horizontal: 30.0),
      child: Row(
        mainAxisAlignment: MainAxisAlignment.spaceBetween,
        children: [
          Row(
            children: [
              Icon(
                Feather.search,
                color: Colors.black54,
                size: 20.0,
              ),
              SizedBox(
                width: 5.0,
              ),
              Container(
                height: 20.0,
                width: 20.0,
                child: Stack(
                  children: [
                    Icon(
                      Icons.notifications,
                      color: Colors.black54,
                      size: 20.0,
                    ),
                    Align(
                      alignment: Alignment(0.7,-0.5),
                                          child: Container(
                        height: 5.0,
                        width: 5.0,
                        decoration: BoxDecoration(
                          color: Colors.red,
                          borderRadius: BorderRadius.circular(5.0),
                        ),
                      ),
                    ),
                  ],
                ),
              ),
            ],
          ),
          ClipRRect(
            borderRadius: BorderRadius.circular(20.0),
            child: Image.network(
              'https://bit.ly/3sCmkky',
              height: 30.0,
              width: 30.0,
              fit: BoxFit.cover,
            ),
          ),
        ],
      ),
    );
  }
```

现在你有一个带有启动画面和基于 URL 的导航系统的网页。祝你有美好的一天...... :) 伙计。