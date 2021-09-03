---
title: "《在Flutter中的Sliver3》"
subtitle: ""
date: 2021-06-06T21:17:15+08:00
lastmod: 2021-06-06T21:17:15+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter"]
categories: ["Flutter"]

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



> 本文主要介绍

<!--more-->



```
import 'package:flutter/material.dart';
import 'package:flutter/rendering.dart';
import 'package:http/http.dart' as http;
import 'dart:convert' as convert;

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      theme: ThemeData(
        primarySwatch: Colors.blue,
        visualDensity: VisualDensity.adaptivePlatformDensity,
      ),
      home: MyHomePage(title: 'Flutter Demo Home Page'),
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
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: DefaultTextStyle(
        style: TextStyle(fontSize: 28, color: Colors.red),
        child: CustomScrollView(
          ///视窗 里面比外边大的一个东西哦哦、
          slivers: <Widget>[
            SliverAppBar(
              floating: true, //往下拉时第一时间显示标题 配合snap使用
              // pinned: true, //一直显示
              snap: true, //轻轻一拉就都显示
              stretch: true, //允许向下拉伸
              title: Text("标题"),
              expandedHeight: 200,
            ),

            ///用于固定元素尺寸的列表
          ],
        ),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: () async {
          final url = "https://h5.48.cn/resource/jsonp/allmembers.php?gid=10";
          final res = await http.get(url);

          if (res.statusCode != 200) {
            throw ("erroe");
          }

          print(res.body);
          final json = convert.jsonDecode(res.body);

          final members = json["rows"].map<Member>((row) {
            return Member(id: row["sid"], name: row["sname"]);
          });
          members.forEach((m) => print(m));
        },
        child: Icon(Icons.add),
      ), // This trailing comma makes auto-formatting nicer for build methods.
    );
  }
}

class Member {
  final String id;
  final String name;

  Member({@required this.id, @required this.name});

  @override
  String toString() => "$id:$name";
}

```

![image-20210807224920120](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210807224920120.png)



```
import 'package:flutter/material.dart';
import 'package:flutter/rendering.dart';
import 'package:http/http.dart' as http;
import 'dart:convert' as convert;

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      theme: ThemeData(
        primarySwatch: Colors.blue,
        visualDensity: VisualDensity.adaptivePlatformDensity,
      ),
      home: MyHomePage(title: 'Flutter Demo Home Page'),
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
  List<Member> _members = [];
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(),
      body: ListView.builder(
        itemBuilder: (BuildContext co, int index) {
          final m = _members[index];
          return ListTile(
            leading: ClipOval(
              child: CircleAvatar(
                radius: 32,
                child: Image.network(
                  m.avatarUrl,
                ),
                backgroundColor: Colors.pink,
              ),
            ),
            title: Text(
              m.id,
            ),
            subtitle: Text(
              m.name,
            ),
          );
        },
        itemCount: _members.length,
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: () async {
          final url = "https://h5.48.cn/resource/jsonp/allmembers.php?gid=10";
          final res = await http.get(url);

          if (res.statusCode != 200) {
            throw ("erroe");
          }

          print(res.body);
          final json = convert.jsonDecode(res.body);

          final members = json["rows"].map<Member>((row) {
            return Member(id: row["sid"], name: row["sname"]);
          });

          setState(() {
            _members = members.toList();
          });
          // members.forEach();
        },
        child: Icon(Icons.add),
      ), // This trailing comma makes auto-formatting nicer for build methods.
    );
  }
}

class Member {
  final String id;
  final String name;

  Member({@required this.id, @required this.name});
  String get avatarUrl => "https://www.snh48.com/images/member/zp_${id}.jpg";
  @override
  String toString() => "$id:$name";
}

```





```
import 'package:flutter/material.dart';
import 'package:flutter/rendering.dart';
import 'package:http/http.dart' as http;
import 'dart:convert' as convert;

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      theme: ThemeData(
        primarySwatch: Colors.blue,
        visualDensity: VisualDensity.adaptivePlatformDensity,
      ),
      home: MyHomePage(title: 'Flutter Demo Home Page'),
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
  List<Member> _members = [];
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(),
      body: CustomScrollView(
        slivers: [
          SliverList(
            delegate: SliverChildBuilderDelegate((BuildContext co, int index) {
              final m = _members[index];
              return ListTile(
                leading: ClipOval(
                  child: CircleAvatar(
                    radius: 32,
                    child: Image.network(
                      m.avatarUrl,
                    ),
                    backgroundColor: Colors.pink,
                  ),
                ),
                title: Text(
                  m.id,
                ),
                subtitle: Text(
                  m.name,
                ),
              );
            }, childCount: _members.length),
          )
        ],
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: () async {
          final url = "https://h5.48.cn/resource/jsonp/allmembers.php?gid=10";
          final res = await http.get(url);

          if (res.statusCode != 200) {
            throw ("erroe");
          }

          print(res.body);
          final json = convert.jsonDecode(res.body);

          final members = json["rows"].map<Member>((row) {
            return Member(id: row["sid"], name: row["sname"]);
          });

          setState(() {
            _members = members.toList();
          });
          // members.forEach();
        },
        child: Icon(Icons.add),
      ), // This trailing comma makes auto-formatting nicer for build methods.
    );
  }
}

class Member {
  final String id;
  final String name;

  Member({@required this.id, @required this.name});
  String get avatarUrl => "https://www.snh48.com/images/member/zp_${id}.jpg";
  @override
  String toString() => "$id:$name";
}

```







```
import 'package:flutter/material.dart';
import 'package:flutter/rendering.dart';
import 'package:http/http.dart' as http;
import 'dart:convert' as convert;

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      theme: ThemeData(
        primarySwatch: Colors.blue,
        visualDensity: VisualDensity.adaptivePlatformDensity,
      ),
      home: MyHomePage(title: 'Flutter Demo Home Page'),
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
  List<Member> _members = [];
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(),
      body: CustomScrollView(
        slivers: [
          SliverList(
            delegate: SliverChildBuilderDelegate((BuildContext co, int index) {
              final m = _members[index];
              return ListTile(
                leading: ClipOval(
                  child: CircleAvatar(
                    radius: 32,
                    child: Image.network(
                      m.avatarUrl,
                    ),
                    backgroundColor: Colors.pink,
                  ),
                ),
                title: Text(
                  m.id,
                ),
                subtitle: Text(
                  m.name,
                ),
                trailing: Text(m.team),
              );
            }, childCount: _members.length),
          )
        ],
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: () async {
          final url = "https://h5.48.cn/resource/jsonp/allmembers.php?gid=10";
          final res = await http.get(url);

          if (res.statusCode != 200) {
            throw ("erroe");
          }

          print(res.body);
          final json = convert.jsonDecode(res.body);

          final members = json["rows"].map<Member>((row) {
            return Member(
              id: row["sid"],
              name: row["sname"],
              team: row["tname"],
            );
          });

          setState(() {
            _members = members.toList();
          });
          // members.forEach();
        },
        child: Icon(Icons.refresh),
      ), // This trailing comma makes auto-formatting nicer for build methods.
    );
  }
}

class Member {
  final String id;
  final String name;
  final String team;

  Member({@required this.id, @required this.name, @required this.team});
  String get avatarUrl => "https://www.snh48.com/images/member/zp_${id}.jpg";
  @override
  String toString() => "$id:$name";
}

```









```
import 'package:flutter/material.dart';
import 'package:flutter/rendering.dart';
import 'package:http/http.dart' as http;
import 'dart:convert' as convert;

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      theme: ThemeData(
        primarySwatch: Colors.blue,
        visualDensity: VisualDensity.adaptivePlatformDensity,
      ),
      home: MyHomePage(title: 'Flutter Demo Home Page'),
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
  List<Member> _members = [];
  SliverList _buildTeamList(String teamName) {
    final teamMembers = _members.where((m) => m.team == teamName).toList();
    return SliverList(
      delegate: SliverChildBuilderDelegate((BuildContext co, int index) {
        final m = teamMembers[index];
        return ListTile(
          leading: ClipOval(
            child: CircleAvatar(
              radius: 32,
              child: Image.network(
                m.avatarUrl,
              ),
              backgroundColor: Colors.pink,
            ),
          ),
          title: Text(
            m.id,
          ),
          subtitle: Text(
            m.name,
          ),
          trailing: Text(m.team),
        );
      }, childCount: teamMembers.length),
    );
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(),
      body: CustomScrollView(
        slivers: [
          SliverPersistentHeader(
            delegate: _MyDelegate("Team SII", Color(0xff91cdeb)),
            pinned: true,
          ),
          _buildTeamList("SII"),
          SliverPersistentHeader(
            delegate: _MyDelegate("Team NII", Color(0xffae86bb)),
            pinned: true,
          ),
          _buildTeamList("NII"),
          SliverPersistentHeader(
            delegate: _MyDelegate("Team HII", Color(0xfff39800)),
            pinned: true,
          ),
          _buildTeamList("HII"),
          SliverPersistentHeader(
            delegate: _MyDelegate("Team X", Color(0xff91cd5b)),
            pinned: true,
          ),
          _buildTeamList("X"),
        ],
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: () async {
          final url = "https://h5.48.cn/resource/jsonp/allmembers.php?gid=10";
          final res = await http.get(url);

          if (res.statusCode != 200) {
            throw ("erroe");
          }

          print(res.body);
          final json = convert.jsonDecode(res.body);

          final members = json["rows"].map<Member>((row) {
            return Member(
              id: row["sid"],
              name: row["sname"],
              team: row["tname"],
            );
          });

          setState(() {
            _members = members.toList();
          });
          // members.forEach();
        },
        child: Icon(Icons.refresh),
      ), // This trailing comma makes auto-formatting nicer for build methods.
    );
  }
}

class _MyDelegate extends SliverPersistentHeaderDelegate {
  final String title;
  final Color color;

  _MyDelegate(this.title, this.color);

  @override
  Widget build(
      BuildContext context, double shrinkOffset, bool overlapsContent) {
    return Container(
        height: 50, color: color, child: FittedBox(child: Text(title)));
  }

  @override
  double get maxExtent => 50;

  @override
  double get minExtent => 50;

  @override
  bool shouldRebuild(covariant _MyDelegate oldDelegate) {
    // return oldDelegate.title != title;
    return true;
  }
}

class Member {
  final String id;
  final String name;
  final String team;

  Member({@required this.id, @required this.name, @required this.team});
  String get avatarUrl => "https://www.snh48.com/images/member/zp_${id}.jpg";
  @override
  String toString() => "$id:$name";
}

```



![image-20210807235408071](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210807235408071.png)







```
import 'package:flutter/material.dart';
import 'package:flutter/rendering.dart';
import 'package:http/http.dart' as http;
import 'dart:convert' as convert;

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      theme: ThemeData(
        primarySwatch: Colors.blue,
        visualDensity: VisualDensity.adaptivePlatformDensity,
      ),
      home: MyHomePage(title: 'Flutter Demo Home Page'),
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
  List<Member> _members = [];
  SliverGrid _buildTeamList(String teamName) {
    final teamMembers = _members.where((m) => m.team == teamName).toList();
    return SliverGrid(
      delegate: SliverChildBuilderDelegate((BuildContext co, int index) {
        final m = teamMembers[index];
        return InkWell(
          onTap: () {},
          child: Container(
            alignment: Alignment.center,
            child: Column(children: [
              ClipOval(
                child: CircleAvatar(
                  radius: 32,
                  child: Image.network(
                    m.avatarUrl,
                  ),
                  backgroundColor: Colors.pink,
                ),
              ),
              Text(m.name)
            ]),
          ),
        );
      }, childCount: teamMembers.length),
      gridDelegate:
          SliverGridDelegateWithFixedCrossAxisCount(crossAxisCount: 4),
    );
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(),
      body: RefreshIndicator(
        onRefresh: () async {
          setState(() => _members.clear());

          final url = "https://h5.48.cn/resource/jsonp/allmembers.php?gid=10";
          final res = await http.get(url);

          if (res.statusCode != 200) {
            throw ("erroe");
          }

          print(res.body);
          final json = convert.jsonDecode(res.body);

          final members = json["rows"].map<Member>((row) {
            return Member(
              id: row["sid"],
              name: row["sname"],
              team: row["tname"],
            );
          });

          setState(() {
            _members = members.toList();
          });
        },
        child: Scrollbar(
          child: CustomScrollView(
            slivers: [
              SliverToBoxAdapter(),
              SliverPersistentHeader(
                delegate: _MyDelegate("Team SII", Color(0xff91cdeb)),
                pinned: true,
              ),
              _buildTeamList("SII"),
              SliverPersistentHeader(
                delegate: _MyDelegate("Team NII", Color(0xffae86bb)),
                pinned: true,
              ),
              _buildTeamList("NII"),
              SliverPersistentHeader(
                delegate: _MyDelegate("Team HII", Color(0xfff39800)),
                pinned: true,
              ),
              _buildTeamList("HII"),
              SliverPersistentHeader(
                delegate: _MyDelegate("Team X", Color(0xff91cd5b)),
                pinned: true,
              ),
              _buildTeamList("X"),
            ],
          ),
        ),
      ),
    );
  }
}

class _MyDelegate extends SliverPersistentHeaderDelegate {
  final String title;
  final Color color;

  _MyDelegate(this.title, this.color);

  @override
  Widget build(
      BuildContext context, double shrinkOffset, bool overlapsContent) {
    return Container(
        height: 50, color: color, child: FittedBox(child: Text(title)));
  }

  @override
  double get maxExtent => 50;

  @override
  double get minExtent => 50;

  @override
  bool shouldRebuild(covariant _MyDelegate oldDelegate) {
    // return oldDelegate.title != title;
    return true;
  }
}

class Member {
  final String id;
  final String name;
  final String team;

  Member({@required this.id, @required this.name, @required this.team});
  String get avatarUrl => "https://www.snh48.com/images/member/zp_${id}.jpg";
  @override
  String toString() => "$id:$name";
}

```



![image-20210808090619595](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210808090619595.png)



```
import 'package:flutter/material.dart';
import 'package:flutter/rendering.dart';
import 'package:http/http.dart' as http;
import 'dart:convert' as convert;

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      theme: ThemeData(
        primarySwatch: Colors.blue,
        visualDensity: VisualDensity.adaptivePlatformDensity,
      ),
      home: MyHomePage(title: 'Flutter Demo Home Page'),
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
  List<Member> _members = [];
  SliverGrid _buildTeamList(String teamName) {
    final teamMembers = _members.where((m) => m.team == teamName).toList();
    return SliverGrid(
      delegate: SliverChildBuilderDelegate((BuildContext co, int index) {
        final m = teamMembers[index];
        return InkWell(
          onTap: () {
            Navigator.of(context).push(MaterialPageRoute(
                builder: (_) => DetailPage(
                      member: m,
                    )));
          },
          child: Container(
            alignment: Alignment.center,
            child:
                Column(mainAxisAlignment: MainAxisAlignment.center, children: [
              ClipOval(
                child: CircleAvatar(
                  radius: 32,
                  child: Image.network(
                    m.avatarUrl,
                  ),
                  backgroundColor: Colors.pink,
                ),
              ),
              Text(m.name)
            ]),
          ),
        );
      }, childCount: teamMembers.length),
      gridDelegate:
          SliverGridDelegateWithFixedCrossAxisCount(crossAxisCount: 4),
    );
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(),
      body: RefreshIndicator(
        onRefresh: () async {
          setState(() => _members.clear());

          final url = "https://h5.48.cn/resource/jsonp/allmembers.php?gid=10";
          final res = await http.get(url);

          if (res.statusCode != 200) {
            throw ("erroe");
          }

          print(res.body);
          final json = convert.jsonDecode(res.body);

          final members = json["rows"].map<Member>((row) {
            return Member(
              id: row["sid"],
              name: row["sname"],
              tid: row["tid"],
              pid: row["pid"],
              pname: row["pname"],
              team: row["tname"],
              pinyin: row["pinyin"],
              abbr: row["abbr"],
              nickname: row["nickname"],
              company: row["company"],
              join_day: row["join_day"],
              height: row["height"],
              birth_day: row["birth_day"],
              star_sign_12: row["star_sign_12"],
              star_sign_48: row["star_sign_48"],
              birth_place: row["birth_place"],
              speciality: row[" speciality"],
              hobby: row["hobby"],
            );
          });

          setState(() {
            _members = members.toList();
          });
        },
        child: Scrollbar(
          child: CustomScrollView(
            slivers: [
              SliverToBoxAdapter(),
              SliverPersistentHeader(
                delegate: _MyDelegate("Team SII", Color(0xff91cdeb)),
                pinned: true,
              ),
              _buildTeamList("SII"),
              SliverPersistentHeader(
                delegate: _MyDelegate("Team NII", Color(0xffae86bb)),
                pinned: true,
              ),
              _buildTeamList("NII"),
              SliverPersistentHeader(
                delegate: _MyDelegate("Team HII", Color(0xfff39800)),
                pinned: true,
              ),
              _buildTeamList("HII"),
              SliverPersistentHeader(
                delegate: _MyDelegate("Team X", Color(0xff91cd5b)),
                pinned: true,
              ),
              _buildTeamList("X"),
            ],
          ),
        ),
      ),
    );
  }
}

class _MyDelegate extends SliverPersistentHeaderDelegate {
  final String title;
  final Color color;

  _MyDelegate(this.title, this.color);

  @override
  Widget build(
      BuildContext context, double shrinkOffset, bool overlapsContent) {
    return Container(
        height: 50, color: color, child: FittedBox(child: Text(title)));
  }

  @override
  double get maxExtent => 50;

  @override
  double get minExtent => 50;

  @override
  bool shouldRebuild(covariant _MyDelegate oldDelegate) {
    // return oldDelegate.title != title;
    return true;
  }
}

class Member {
  final String id;
  final String name;
  final String team; // 多光标快捷键  alt+shift

  final String pinyin;
  final String abbr;

  final String nickname;
  final String company;
  final String join_day;
  final String height;
  final String birth_day;
  final String star_sign_12;
  final String star_sign_48;
  final String birth_place;
  final String speciality;
  final String hobby;
  final String tid;
  final String pid;
  final String pname;

  Member({
    @required this.id,
    @required this.name,
    @required this.team,
    this.pinyin,
    this.abbr,
    this.nickname,
    this.company,
    this.join_day,
    this.height,
    this.birth_day,
    this.star_sign_12,
    this.star_sign_48,
    this.birth_place,
    this.speciality,
    this.hobby,
    this.tid,
    this.pid,
    this.pname,
  });
  String get avatarUrl => "https://www.snh48.com/images/member/zp_${id}.jpg";
  @override
  String toString() => "$id:$name";
}

class DetailPage extends StatelessWidget {
  final Member member;

  const DetailPage({Key key, this.member}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text(member.name),
      ),
      body: Column(
        children: [
          _buildInfo("pinyin", member.pinyin),
          _buildInfo("加入所属", member.pname),

          _buildInfo("昵称", member.nickname),
          _buildInfo("公司", member.company),
          _buildInfo("加入日期", member.join_day),
          _buildInfo("身高", "${member.height}cm"),
          _buildInfo("生日", member.birth_day),
          _buildInfo("星座", member.star_sign_12), //zodic sign

          _buildInfo("爱好", member.hobby),
          // Text(member.speciality),
        ],
      ),
    );
  }

  _buildInfo(String title, String text) {
    return Card(
      child: Padding(
        padding: EdgeInsets.all(10),
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.stretch,
          children: [
            Text(title),
            Text(
              text,
              textAlign: TextAlign.end,
            ),
          ],
        ),
      ),
    );
  }
}

```







最终版



```
import 'package:flutter/material.dart';
import 'package:flutter/rendering.dart';
import 'package:http/http.dart' as http;
import 'dart:convert' as convert;

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      theme: ThemeData(
        primarySwatch: Colors.blue,
        visualDensity: VisualDensity.adaptivePlatformDensity,
      ),
      home: MyHomePage(title: 'Flutter Demo Home Page'),
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
  List<Member> _members = [];
  SliverGrid _buildTeamList(String teamName) {
    final teamMembers = _members.where((m) => m.team == teamName).toList();
    return SliverGrid(
      delegate: SliverChildBuilderDelegate((BuildContext co, int index) {
        final m = teamMembers[index];
        return InkWell(
          onTap: () {
            Navigator.of(context).push(MaterialPageRoute(
                builder: (_) => DetailPage(
                      member: m,
                    )));
          },
          child: Container(
            alignment: Alignment.center,
            child:
                Column(mainAxisAlignment: MainAxisAlignment.center, children: [
              Hero(
                tag: m.avatarUrl,
                child: ClipOval(
                  child: CircleAvatar(
                    radius: 32,
                    child: Image.network(
                      m.avatarUrl,
                    ),
                    backgroundColor: Colors.pink,
                  ),
                ),
              ),
              Text(m.name)
            ]),
          ),
        );
      }, childCount: teamMembers.length),
      gridDelegate:
          SliverGridDelegateWithFixedCrossAxisCount(crossAxisCount: 4),
    );
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(),
      body: RefreshIndicator(
        onRefresh: () async {
          setState(() => _members.clear());

          final url = "https://h5.48.cn/resource/jsonp/allmembers.php?gid=10";
          final res = await http.get(url);

          if (res.statusCode != 200) {
            throw ("erroe");
          }

          print(res.body);
          final json = convert.jsonDecode(res.body);

          final members = json["rows"].map<Member>((row) {
            return Member(
              id: row["sid"],
              name: row["sname"],
              tid: row["tid"],
              pid: row["pid"],
              pname: row["pname"],
              team: row["tname"],
              pinyin: row["pinyin"],
              abbr: row["abbr"],
              nickname: row["nickname"],
              company: row["company"],
              join_day: row["join_day"],
              height: row["height"],
              birth_day: row["birth_day"],
              star_sign_12: row["star_sign_12"],
              star_sign_48: row["star_sign_48"],
              birth_place: row["birth_place"],
              speciality: row[" speciality"],
              hobby: row["hobby"],
            );
          });

          setState(() {
            _members = members.toList();
          });
        },
        child: Scrollbar(
          child: CustomScrollView(
            slivers: [
              SliverToBoxAdapter(),
              SliverPersistentHeader(
                delegate: _MyDelegate("Team SII", Color(0xff91cdeb)),
                pinned: true,
              ),
              _buildTeamList("SII"),
              SliverPersistentHeader(
                delegate: _MyDelegate("Team NII", Color(0xffae86bb)),
                pinned: true,
              ),
              _buildTeamList("NII"),
              SliverPersistentHeader(
                delegate: _MyDelegate("Team HII", Color(0xfff39800)),
                pinned: true,
              ),
              _buildTeamList("HII"),
              SliverPersistentHeader(
                delegate: _MyDelegate("Team X", Color(0xff91cd5b)),
                pinned: true,
              ),
              _buildTeamList("X"),
              SliverPersistentHeader(
                delegate: _MyDelegate("预备生", Colors.green),
                pinned: true,
              ),
              _buildTeamList("预备生"),
            ],
          ),
        ),
      ),
    );
  }
}

class _MyDelegate extends SliverPersistentHeaderDelegate {
  final String title;
  final Color color;

  _MyDelegate(this.title, this.color);

  @override
  Widget build(
      BuildContext context, double shrinkOffset, bool overlapsContent) {
    return Container(
        height: 50, color: color, child: FittedBox(child: Text(title)));
  }

  @override
  double get maxExtent => 50;

  @override
  double get minExtent => 50;

  @override
  bool shouldRebuild(covariant _MyDelegate oldDelegate) {
    // return oldDelegate.title != title;
    return true;
  }
}

class Member {
  final String id;
  final String name;
  final String team; // 多光标快捷键  alt+shift

  final String pinyin;
  final String abbr;

  final String nickname;
  final String company;
  final String join_day;
  final String height;
  final String birth_day;
  final String star_sign_12;
  final String star_sign_48;
  final String birth_place;
  final String speciality;
  final String hobby;
  final String tid;
  final String pid;
  final String pname;

  Member({
    @required this.id,
    @required this.name,
    @required this.team,
    this.pinyin,
    this.abbr,
    this.nickname,
    this.company,
    this.join_day,
    this.height,
    this.birth_day,
    this.star_sign_12,
    this.star_sign_48,
    this.birth_place,
    this.speciality,
    this.hobby,
    this.tid,
    this.pid,
    this.pname,
  });
  String get avatarUrl => "https://www.snh48.com/images/member/zp_${id}.jpg";
  @override
  String toString() => "$id:$name";
}

class DetailPage extends StatelessWidget {
  final Member member;

  const DetailPage({Key key, this.member}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Scaffold(
        body: CustomScrollView(
      slivers: [
        SliverAppBar(
          leading: BackButton(
            color: Colors.black,
          ),
          pinned: true,
          // stretch: true,
          backgroundColor: Colors.pink[200],
          expandedHeight: 300,
          flexibleSpace: FlexibleSpaceBar(
            title: Text(
              "SNH48-${member.name}",
              style: TextStyle(color: Colors.black),
            ),
            background: Stack(
              children: [
                Column(
                  children: [
                    Expanded(
                      child: Image.network(
                        "https://png.pngtree.com/thumb_back/fh260/back_our/20190628/ourmid/pngtree-white-lines-pink-background-image_269454.jpg",
                        fit: BoxFit.cover,
                      ),
                    ),
                    Container(
                      height: 5,
                      color: Colors.pink[400],
                    ),
                    Expanded(child: SizedBox()),
                  ],
                  crossAxisAlignment: CrossAxisAlignment.stretch,
                ),
                Center(
                  child: Padding(
                    padding: const EdgeInsets.all(100.0),
                    child: AspectRatio(
                      aspectRatio: 1 / 1,
                      child: Hero(
                        tag: member.avatarUrl,
                        child: Material(
                          shape: CircleBorder(),
                          elevation: 4.0,
                          child: ClipOval(
                            child: Image.network(
                              member.avatarUrl,
                              fit: BoxFit.cover,
                            ),
                          ),
                        ),
                      ),
                    ),
                  ),
                ),
              ],
            ),
            collapseMode: CollapseMode.pin,
          ),
        ),
        SliverList(
            delegate: SliverChildListDelegate([
          _buildInfo("pinyin", member.pinyin),
          _buildInfo("加入所属", member.pname),

          _buildInfo("昵称", member.nickname),
          _buildInfo("公司", member.company),
          _buildInfo("加入日期", member.join_day),
          _buildInfo("身高", "${member.height}cm"),
          _buildInfo("生日", member.birth_day),
          _buildInfo("星座", member.star_sign_12), //zodic sign

          _buildInfo("爱好", member.hobby), _buildInfo("昵称", member.nickname),
          _buildInfo("公司", member.company),
          _buildInfo("加入日期", member.join_day),
          _buildInfo("身高", "${member.height}cm"),
          _buildInfo("生日", member.birth_day),
          _buildInfo("星座", member.star_sign_12), //zodic sign

          _buildInfo("爱好", member.hobby),
          // Text(member.speciality),
        ]))
      ],
    ));
  }

  _buildInfo(String title, String text) {
    return Card(
      child: Padding(
        padding: EdgeInsets.all(10),
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.stretch,
          children: [
            Text(title),
            Text(
              text,
              textAlign: TextAlign.end,
            ),
          ],
        ),
      ),
    );
  }
}

```

