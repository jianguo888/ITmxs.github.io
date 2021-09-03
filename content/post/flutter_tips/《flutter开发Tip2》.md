---
title: "《Flutter开发Tip2》"
subtitle: ""
date: 2021-07-30T22:43:19+08:00
lastmod: 2021-07-30T22:43:19+08:00
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



> 本文主要介绍flutter开发小技巧

<!--more-->

## 代码静态分析

在提交代码时为了提高代码质量，保持团队的代码风格一致，需要进行代码静态分析，一般通过下面2种方法来进行

### flutter analyze

使用`flutter analyze`进行代码静态分析,此命令会根据`analysis_options.yaml`定义的规则进行静态分析

### dartfmt

使用`dartfmt ./ -w`对当前目录以及子目录的dart代码进行代码,`-w`选项会自动重写文件使其符合规范。

使用`dartfmt ./ -n`显示当前目录以及子目录的dart代码格式可以修改的文件但是不做修改，可以配合ci分析代码格式问题。

更多选项请使用`dartfmt --help`查看

## Push & present & Pop

```
// Push by route
Navigator.pushNamed(context, '/b')
  
// push 
Navigator.of(context).push(MaterialPageRoute(builder: (BuildContext context) => MyPage()));

// present
Navigator.of(context).push(MaterialPageRoute(builder: (BuildContext context) => MyPage(),fullscreenDialog: true));

// pop
Navigator.pop(context)
  
// pop + push
Navigator.of(context)
	..pop()
	..pop()
	..pushNamed('/settings');
```

API

```
// push
Future push(BuildContext context, Route route)

// pop
bool pop(BuildContext context, [ result ])
  
// 下面两种是等效的
Navigator.push(BuildContext context, Route route)
Navigator.of(context).push(Route route)
```

## 获取widget的位置和宽高

```
final RenderBox box = keyContext.findRenderObject();
final size = box.size;
final topLeftPosition = box.localToGlobal(Offset.zero);
```

See more

- [How to get a height of a Widget?](https://stackoverflow.com/questions/49307677/how-to-get-a-height-of-a-widget)
- [Flutter : Widget Size and Position](https://medium.com/@diegoveloper/flutter-widget-size-and-position-b0a9ffed9407)

## Model To JSON

### json_serializable

1. 引入

   ```
   dependencies:
     # Your other regular dependencies here
     json_annotation: ^2.0.0
   
   dev_dependencies:
     # Your other dev_dependencies here
     build_runner: ^1.0.0
     json_serializable: ^2.0.0
   ```

2. 使用

   ```
   import 'package:json_annotation/json_annotation.dart';
   
   // user.g.dart 将在我们运行生成命令后自动生成
   part 'user.g.dart';
   
   ///这个标注是告诉生成器，这个类是需要生成Model类的
   @JsonSerializable()
   
   class User{
     User(this.name, this.email);
   
     String name;
     String email;
     //不同的类使用不同的mixin即可
     factory User.fromJson(Map<String, dynamic> json) => _$UserFromJson(json);
     Map<String, dynamic> toJson() => _$UserToJson(this);
   }
   ```

1. 单次解析：`flutter packages pub run build_runner build`
2. 持续集成：`flutter packages pub run build_runner watch`
3. [json_serializable 在线json转dart model工具](https://caijinglong.github.io/json2dart/index_ch.html)

### Built value

1. 在线json转build value 模板工具 https://charafau.github.io/json2builtvalue/

2. [VSCode built value 插件](https://marketplace.visualstudio.com/items?itemName=GiancarloCode.built-value-snippets)

3. Sample code

   ```
   // interface model
   import 'package:built_value/built_value.dart';
   
   part 'user.g.dart';
   
   abstract class User implements Built<User, UserBuilder> {
     String get name;
     @nullable
     String get nickname;
     User._();
     factory User([updates(UserBuilder b)]) = _$User;
   }
   
   // init
   var user1 = new User((b) => b
       ..name = 'John Smith'
       ..nickname = 'Joe');
   
   // update
   var user2 = user.rebuild((b) => b
       ..nickname = 'Jojo');
   
   // update
   var updatedStructuredData = structuredData.rebuild((b) => b
       ..user.update((b) => b
           ..name = 'Johnathan Smith')
       ..credentials.phone.update((b) => b
           ..country = Country.switzerland
           ..number = '555 01234 555'));
   
   
   // nested builders
   abstract class Node implements Built<Node, NodeBuilder> {
     @nullable
     String get label;
     @nullable
     Node get left; 
     @nullable
     Node get right;
     Node._();
     factory Node([updates(NodeBuilder b)]) = _$Node;
   }
   
   // new or update
   var node = new Node((b) => b
     ..left.left.left.right.left.right.label = 'I’m a leaf!'
     ..left.left.right.right.label = 'I’m also a leaf!');
   var updatedNode = node.rebuild((b) => b
     ..left.left.right.right.label = 'I’m not a leaf any more!'
     ..left.left.right.right.right.label = 'I’m the leaf now!');
   ```

## 参考

- [3 cool Dart patterns](https://medium.com/dartlang/3-cool-dart-patterns-6d8d9d3d8fb8)
- [built_collection - pub.dev](https://pub.dev/packages/built_collection)
- [Dart’s built_collection for Immutable Collections](https://medium.com/dartlang/darts-built-collection-for-immutable-collections-db662f705eff)
- [dartfmt](https://dart.dev/tools/dartfmt)
- [Customizing static analysis](https://dart.dev/guides/language/analysis-options)