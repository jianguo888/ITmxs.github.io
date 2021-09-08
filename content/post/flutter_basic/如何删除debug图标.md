---
title: "如何删除debug图标"
date: 2021-09-08T12:59:03+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter","dart"]
categories: ["Flutter","dart"]
---







从今天开始，将持续给大家带来一些flutter开发方面的技巧，

代码也会完全开源，只希望大家多多三连支持

# **Flutter：如何删除模拟器上的 DEBUG 横幅**

本文将向您展示如何在使用 Flutter 时摆脱位于 iOS/Android 模拟器右上角的丑陋（仅是我个人的想法）调试横幅。

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/image/Screen-Shot-2020-06-27-at-23.54.49.jpg)

解决方法很简单。您需要做的就是转到**lib/main.dart**文件并将**MaterialApp**类的**debugShowCheckedModeBanner**属性设置为**false**（默认设置为**true**）。

要将**debugShowCheckedModeBanner**设置为**false**，请添加以下行：

```dart
debugShowCheckedModeBanner: false,
```

例子：

```dart
// other code
class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      // Hide the Debug banner
      debugShowCheckedModeBanner: false,
      title: 'Flutter Demo',
      theme: ThemeData(
        primarySwatch: Colors.blue,
        visualDensity: VisualDensity.adaptivePlatformDensity,
      ),
      home: MyHomePage(title: 'Flutter Demo Home Page'),
    );
  }
}
// other code
```



你学费了吗？

# **Flutter/Dart：在最小值和最大值之间生成随机数**

在 Dart（以及 Flutter）中生成给定范围内的随机整数的几个示例。

## 示例 1：使用 Random().nextInt() 方法

编码：

```
import 'dart:math';

randomGen(min, max) {
  // the nextInt method generate a non-ngegative random integer from 0 (inclusive) to max (exclusive)
  var x = Random().nextInt(max) + min;

  // If you don't want to return an integer, just remove the floor() method
  return x.floor();
}

void main() {
  int a = randomGen(1, 10);
  print(a);


}
```

输出：

```
8 // you may get 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10)
```

您得到的结果可能包含最小值、最大值或此范围内的值。

## 示例 2：使用 Random.nextDouble() 和 floor() 方法

编码：

```
import 'dart:math';

randomGen(min, max) {
  // the nextDouble() method returns a random number between 0 (inclusive) and 1 (exclusive)
  var x = Random().nextDouble() * (max - min) + min;

  // If you don't want to return an integer, just remove the floor() method
  return x.floor();
}

// Testing
void main() {
  // with posstive min and max
  print(randomGen(10, 100));
  
  // with negative min 
  print(randomGen(-100, 0));
}
```

输出（当然，输出是随机的，每次重新执行代码时都会改变）。

```
47
-69
```

您得到的结果可能包含 min 但从不包含 max。



# 在 Dart 中合并 2 个列表

Dart的**List**类提供了**addAll**方法，可以帮助您轻松地将 2 个列表连接在一起；

例子：

```
void main() {
  List listA = [1, 2, 3];
  List listB = [4, 5, 6];
  listA.addAll(listB);
  print(listA);
  
  List<String> listC = ['Dog', 'Cat'];
  List<String> listD = ['Bear', 'Tiger'];
  listC.addAll(listD);
  print(listC);
}
```

输出：

```
[1, 2, 3, 4, 5, 6]
[Dog, Cat, Bear, Tiger]
```

# 如何在 Flutter (2021) 中制作圆形按钮

按钮是移动应用程序中不可或缺的一部分。在 Flutter 中，按钮的形状默认为矩形。但是，在某些情况下，您可能希望您的按钮是圆形的，以最好地突出并融入周围的布局。

本文将向您展示一些在 Flutter 中创建圆形按钮（也称为圆形按钮）的优雅方法。

目录



[使用 ElevatedButton + ConstrainedBox](https://www.kindacode.com/article/how-to-make-circular-buttons-in-flutter/#Using_ElevatedButton_ConstrainedBox)[使用 ElevatedButton + Container](https://www.kindacode.com/article/how-to-make-circular-buttons-in-flutter/#Using_ElevatedButton_Container)[使用 MaterialButton](https://www.kindacode.com/article/how-to-make-circular-buttons-in-flutter/#Using_MaterialButton)[使用 ClipRRect + SizedBox + ElevatedButton](https://www.kindacode.com/article/how-to-make-circular-buttons-in-flutter/#Using_ClipRRect_SizedBox_ElevatedButton)[凸起按钮的旧方法](https://www.kindacode.com/article/how-to-make-circular-buttons-in-flutter/#An_Old_Method_for_RaisedButton)[结论](https://www.kindacode.com/article/how-to-make-circular-buttons-in-flutter/#Conclusion)

## 使用 ElevatedButton + ConstrainedBox

预览：

1.00

<video class="" autoplay="" controls="" loop="" muted="" data-lazy-type="video" data-src="https://www.kindacode.com/wp-content/uploads/2020/07/Flutter-circular-button.mp4" src="https://www.kindacode.com/wp-content/uploads/2020/07/Flutter-circular-button.mp4" __idm_id__="34211841" style="margin: 0px; padding: 0px; box-sizing: border-box; width: 720px;"></video>

编码：

```
ConstrainedBox(
              constraints: BoxConstraints.tightFor(width: 200, height: 200),
              child: ElevatedButton(
                child: Text('Button', style: TextStyle(fontSize: 24),),
                onPressed: () {},
                style: ElevatedButton.styleFrom(
                  shape: CircleBorder(),
                ),
              ),
            ),
```

如果你想探索更多关于 ConstrainedBox 的信息，请查看这篇文章：[Flutter ConstrainedBox – 教程和示例](https://www.kindacode.com/article/flutter-constrainedbox-tutorial-and-examples/)。

## **使用 ElevatedButton** + Container

编码：

```
ElevatedButton(
        style: ElevatedButton.styleFrom(
           shape: CircleBorder(), 
           primary: Colors.red
        ),
        child: Container(
          width: 200,
          height: 200,
          alignment: Alignment.center,
          decoration: BoxDecoration(shape: BoxShape.circle),
          child: Text(
            'I am a button',
            style: TextStyle(fontSize: 24),
          ),
        ),
        onPressed: () {},
)
```

输出：

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/image/Screen-Shot-2020-10-22-at-23.17.49.jpg)

## **使用 MaterialButton**

编码：

```
MaterialButton(
        color: Colors.blue,
        shape: CircleBorder(),
        onPressed: () {},
        child: Padding(
          padding: const EdgeInsets.all(100),
          child: Text(
            'A circle button',
            style: TextStyle(color: Colors.white, fontSize: 24),
          ),
        ),
)
```

广告

输出：

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/image/Screen-Shot-2020-10-22-at-23.23.22.jpg)

## **使用 ClipRRect + SizedBox + ElevatedButton**

编码：

```
ClipRRect(
            borderRadius: BorderRadius.circular(120),
            child: SizedBox(
              width: 240,
              height: 240,
              child: ElevatedButton.icon(
                icon: Icon(Icons.camera, size: 40,),
                label: Text('Camera', style: TextStyle(fontSize: 25),),
                onPressed: () {},
              ),
            ),
),
```

输出：

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/image/Screen-Shot-2020-11-28-at-06.29.23.jpg)

## **凸起按钮的旧方法**

RaisedButton 小部件已弃用，仅出现在旧应用程序中。如果您正在处理一个新的 Flutter 项目，请改用 ElevatedButton。

将**RaisedButton**小部件包装在方形**容器内**并使用**BorderRadius**类。

广告

示例代码：

```
import 'package:flutter/material.dart';

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
        // Removing the debug banner when running on simulators
        debugShowCheckedModeBanner: false,
        title: 'An Aweasome App',
        home: Scaffold(
          appBar: AppBar(
            title: Text('Home'),
          ),
          body: Center(
            child: Column(
              children: <Widget>[
                // just an empty SizedBox to add some spacing
                SizedBox(height: 30,),

                Container(
                    // set width equal to height to make a square
                    width: 200,
                    height: 200,
                    child: RaisedButton(
                      color: Colors.orange,
                      shape: RoundedRectangleBorder(
                          // set the value to a very big number like 100, 1000...
                          borderRadius: BorderRadius.circular(100)),
                      child: Text('I am a button'),
                      onPressed: () {},
                    ))
              ],
            ),
          ),
        ));
  }
}
```

输出：

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/image/Screen-Shot-2020-07-29-at-21.39.34.jpg)

## 结论

我们研究了几种在 Flutter 中实现圆形按钮的方法。

# 如何在 Flutter (2021) 中禁用横向模式

本文将向您展示如何在 Flutter 中禁用横向模式。

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/image/Screen-Shot-2020-08-10-at-18.09.56.jpg)

## 步骤

\1. 将**服务**库导入您的**main.dart**文件：

```
import 'package:flutter/services.dart';
```

请注意，**服务**库随 Flutter 一起提供，您无需安装任何第三方插件。

\2. 将下面的代码片段添加到**main.dart**文件中的**main()**函数中：

```
void main() {
  // add these lines
  WidgetsFlutterBinding.ensureInitialized();
  SystemChrome.setPreferredOrientations(
      [DeviceOrientation.portraitUp, DeviceOrientation.portraitDown]);

  ///////////// 
  runApp(MyApp());
}
```

\3. 重新启动您的应用程序（不使用**热重载**）并通过旋转 Android 和 iOS 模拟器检查结果。

## 一个完整的例子

**预览：**

1.00

<video class="" autoplay="" controls="" loop="" muted="" data-lazy-type="video" data-src="https://www.kindacode.com/wp-content/uploads/2020/08/Flutter-disable-landscape.mp4" src="https://www.kindacode.com/wp-content/uploads/2020/08/Flutter-disable-landscape.mp4" __idm_id__="582027265" style="margin: 0px; padding: 0px; box-sizing: border-box; width: 720px;"></video>

**完整代码：**

```
import 'package:flutter/material.dart';
import 'package:flutter/services.dart';

void main() {
  WidgetsFlutterBinding.ensureInitialized();
  SystemChrome.setPreferredOrientations(
      [DeviceOrientation.portraitUp, DeviceOrientation.portraitDown]);

  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      title: 'Kindacode.com',
      home: HomePage(),
    );
  }
}

class HomePage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Kindacode.com'),
      ),
      body: Center(
        child: Text(
          'Hello',
          style: TextStyle(fontSize: 100),
        ),
      ),
    );
  }
}
```

如果您想根据设备方向显示不同的内容而不是禁用横向模式，

# **Flutter 错误：无法解析包中的“字符”...**

## **问题**

运行 Flutter 应用程序时，您可能会看到以下错误消息：

```
Could not resolve the package 'characters' in 'package:characters/characters.dart'
```

这通常发生在将 Flutter 升级到较新版本之后。

## **解决方案**

为了修复上述错误，我只需通过执行以下命令升级**pubspec.yaml**文件中列出的所有依赖项：

```
flutter pub upgrade
```

希望这可以帮助你。祝你有美好的一天![🙂](https://luckly007.oss-cn-beijing.aliyuncs.com/image/1f642.svg)

# Windows 和 Mac 上的 Flutter 系统要求（2021）

Flutter 是 Google 创建的开源 UI SDK（软件开发工具包）。Flutter 于 2017 年首次推出并迅速发展，迄今为止在 Github 上拥有超过 11 万颗星，并被谷歌、阿里巴巴、宝马、房地产经纪人、Square 等许多大公司使用。

以下是 2021 年在 Windows 和 macOS 上安装和运行 Flutter 的系统要求。

## **视窗**

**最低要求：**

- 操作系统：Windows 7 SP1 64 位、Windows 8.1 64 位、Windows 10 64 位
- 免费存储空间：1.32 GB
- 预装工具：
  - Windows PowerShell 5.0 或更新版本
  - git 2.x

**推荐规格**

构建过程消耗大量资源，尤其是 CPU。如果你的机器太旧，它会太热，你可能需要比平时付出更多的时间，并且在使用 Flutter 开发应用程序时会承受压力。

- 操作系统：Windows 10 64 位
- CPU：英特尔酷睿 i5-8400
- 内存：8 GB 内存
- 免费存储：5 GB SSD
- 工具：Windows PowerShell 5.0+、Git 2.x

## **苹果系统**

**最低要求：**

- 操作系统：macOS（64 位）
- 免费存储空间：2.8 GB
- 预装工具：bash、curl、git 2.x、mkdir、rm、unzip、which、zip

**推荐要求：**

- 操作系统：macOS 10.10 或更新版本
- CPU：Intel Core i3-8100 或同等产品
- 内存：8 GB 内存
- 免费存储：5 GB SSD
- 预装工具：bash、curl、git 2.x、mkdir、rm、unzip、which、zip

MacBook Pro 2017、Mac Mini 2018、MacBook Air 2020 或更好的 Mac 是不错的选择。

## 接下来是什么？

Flutter 可帮助您使用单个代码库创建适用于 Android、iOS 和 Web 的应用程序。学习和使用 Flutter 也比其他移动应用程序制作技术更简单。

# **Flutter 中的实时电子邮件验证**

在本文中，我们将构建一个小型 Flutter 应用程序来演示如何在用户键入时验证电子邮件。

在本教程结束时运行代码时，您将看到以下内容：

1.00

<video class="" autoplay="" controls="" loop="" muted="" data-lazy-type="video" data-src="https://www.kindacode.com/wp-content/uploads/2020/10/Flutter-Live-email-validation.mp4" src="https://www.kindacode.com/wp-content/uploads/2020/10/Flutter-Live-email-validation.mp4" __idm_id__="28840961" style="margin: 0px; padding: 0px; box-sizing: border-box; width: 720px;"></video>

## **Flutter 中的实时电子邮件验证**

1.**创建一个全新的Flutter项目**

```
flutter create email_validation_app
```

**2.安装[email_validator](https://pub.dev/packages/email_validator)包**

将此添加到**pubspec.yaml**文件中的**依赖项**块：

```
email_validator: '^1.0.5'
```

然后运行：

```
flutter pub get 
```

**注意**：如果您想在 Flutter 中使用正则表达式而不是第三方插件来验证电子邮件，请参阅[此](https://www.kindacode.com/article/how-to-validate-email-in-flutter-without-plugins/)[文章](https://www.kindacode.com/article/how-to-validate-email-in-flutter-without-plugins/)。

**3. 打开您的 /lib/main.dart 文件，删除所有默认代码并添加以下内容**：

```
import 'package:flutter/material.dart';
import 'package:flutter/services.dart';

// import the email validator package
import 'package:email_validator/email_validator.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      // Hide the debug banner
      debugShowCheckedModeBanner: false,
      
      title: 'Flutter Tutorial',
      home: MyHomePage(),
    );
  }
}

class MyHomePage extends StatefulWidget {
  @override
  _MyHomePageState createState() => _MyHomePageState();
}

class _MyHomePageState extends State<MyHomePage> {
  final emailController = TextEditingController();

  // This message will be displayed under the email input
  String message = '';

  void validateEmail(String enteredEmail){
    if(EmailValidator.validate(enteredEmail)){
      setState(() {
        message = 'Your email seems nice!';
      });
    } else {
      setState(() {
        message = 'Please enter a valid email address!';
      });
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
        appBar: AppBar(
          title: Text('Flutter Tutorial'),
        ),
        body: Container(
          padding: EdgeInsets.symmetric(vertical: 30, horizontal: 0),
          child: Column(children: [
            // Input email
            TextField(
              decoration: InputDecoration(labelText: 'Your Email'),
              keyboardType: TextInputType.emailAddress,
              controller: emailController,
              onChanged: (enteredEmail) => validateEmail(enteredEmail),
            ),

            // Just add some space
            SizedBox(height: 20),

            // Display the message
            Text(message)
          ]),
        ));
  }
}
```

**4. 试一试：**

```
flutter run 
```



24页

# 如何在 Flutter/Dart 中克隆 List 或 Map（4 种方法）

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/image/Flutter-1.png)

在 Dart 中有多种方法可以深度复制列表或地图。

目录



[使用 json.decode() 和 json.encode()](https://www.kindacode.com/article/how-to-clone-a-list-or-map-in-dart-and-flutter/#Using_jsondecode_and_jsonencode)[使用传播语法](https://www.kindacode.com/article/how-to-clone-a-list-or-map-in-dart-and-flutter/#Using_the_spread_syntax)[3. 使用 from() 方法](https://www.kindacode.com/article/how-to-clone-a-list-or-map-in-dart-and-flutter/#3_Using_the_from_method)[4. 使用 [\]..addAll()](https://www.kindacode.com/article/how-to-clone-a-list-or-map-in-dart-and-flutter/#4_Using_addAll)

## **使用 json.decode() 和 json.encode()**

这种方法适用于**任何场景**（嵌套列表、嵌套映射……）。您实际上可以在没有引用的情况下克隆多维列表和映射。

句法：

```
List newList = json.decode(json.encode(oldList));
Map newMap = json.decode(json.encode(oldList));
```

例子：

```
import 'dart:convert';
void main(){
  // Define a multi-dimensional map
  final Map oldMap = {
    "name" : {
      "first": "Joh",
      "last": "Doe"
    },
    "asset" : {
      "money" : {
        "bank": 1000,
        "cash": 100
      },
      "house": 1
    }
  };
  
  final Map newMap = json.decode(json.encode(oldMap));
  newMap["name"]["first"] = "Jesse";
  newMap["name"]["last"] = "Pinkman"; 
  newMap["asset"]["money"]["cash"] = 0; 
  
  
  print('oldMap: $oldMap');
  print('newMap: $newMap');
  
}
```

输出：

```
oldMap: {name: {first: Joh, last: Doe}, asset: {money: {bank: 1000, cash: 100}, house: 1}}
newMap: {name: {first: Jesse, last: Pinkman}, asset: {money: {bank: 1000, cash: 0}, house: 1}}
```

## **使用传播语法**

这种方法对于一维列表和地图来说既快速又有效。

**注意**：此方法适用于一维 List 或 Map。要克隆多维**（嵌套） List 或 Map**，请使用**第一种方法**。

句法：

```
List newList = [...oldList];
Map newMap = {...oldMap}
```

广告

例子：

```
void main(){
  // List
  final List myList = ['A', 'B', 'C', 'D'];
  
  final List clonedList = [...myList];
  clonedList[0] = 'Dog';
  
  print('myList: $myList');
  print('clonedList: $clonedList');
  
  // Map
  final Map myMap = {
    'name': 'John',
    'age': 37
  };
  
  final Map clonedMap = {...myMap};
  clonedMap["name"] = "Marry";
  clonedMap['age'] = 4; 
  
  print('myMap: $myMap');
  print('clonedMap: $clonedMap');
}
```

输出：

```
myList: [A, B, C, D]
clonedList: [Dog, B, C, D]
myMap: {name: John, age: 37}
clonedMap: {name: Marry, age: 4}
```

## **3. 使用 from() 方法**

与第二种方法一样，这种方法对于一维列表和地图来说既快速又有效。

**注意**：此方法适用于一维 List 或 Map。要深度复制**多维（嵌套） List 或 Map**，请使用**第一种方法**。

句法：

```
List newList = List.from(oldList);
Map newMap = Map.from(oldMap);
```

## **4. 使用 []..addAll()**

广告

这种方法仅适用于一维列表快速且有效。

句法：

```
List newList  = []..addAll(oldList);
```

例子：

```
void main(){
 List oldList = [1, 2, 3];
 List newList  = []..addAll(oldList);
 
 newList[2] = 100;
 print('oldList: $oldList');
  print('newList: $newList');
}
```

输出：

```
oldList: [1, 2, 3]
newList: [1, 2, 100]
```

# 在 Flutter 中显示下标和上标

在某些情况下，您可能希望在 Flutter 应用程序中显示一些下标和上标，例如数学公式、化学方程式或物理曲线。

要渲染下标，您可以像这样设置 Text 或 TextSpan 小部件的样式：

```
TextStyle(
  fontFeatures: [FontFeature.subscripts()]),
  /* other styles go here */
),
```

要渲染上标，您可以像这样设置 Text 或 TextSpan 小部件的样式：

```
TextStyle(
  fontFeatures: [FontFeature.superscripts()]),
  /* other styles go here */
),
```

为了更清楚，请参阅下面的示例。

## 示例 1：使用多个 Text 小部件

**截屏：**

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/image/Screen-Shot-2021-07-18-at-22.36.44.jpg)

**编码：**

```
// Don't forget to import dart:ui into your code
Wrap(
              direction: Axis.horizontal,
              children: [
                Text('x'),
                Text(
                  '2',
                  style: TextStyle(fontFeatures: [FontFeature.superscripts()]),
                ),
                Text(' + '),
                Text('y'),
                Text(
                  '5',
                  style: TextStyle(fontFeatures: [FontFeature.superscripts()]),
                ),
                Text(' + '),
                Text('z'),
                Text(
                  'n',
                  style: TextStyle(fontFeatures: [FontFeature.superscripts()]),
                ),
              ],
            ),
```

## 示例 2：使用 RichText + TextSpan

**截屏：**

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/image/Screen-Shot-2021-07-18-at-22.38.47.jpg)

**完整的源代码：**

```
// main.dart
import 'package:flutter/material.dart';
import 'dart:ui';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
        // Remove the debug banner
        debugShowCheckedModeBanner: false,
        title: 'Kindacode.com',
        theme: ThemeData(
          primarySwatch: Colors.amber,
        ),
        home: HomePage());
  }
}

class HomePage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
        appBar: AppBar(
          title: Text('Kindacode.com'),
        ),
        body: Padding(
          padding: EdgeInsets.all(30),
          child: RichText(
            text: TextSpan(children: [
              TextSpan(
                  text: 'Acid Sulfuric: ',
                  style: TextStyle(color: Colors.purple, fontSize: 30)),
              TextSpan(
                  text: 'H',
                  style: TextStyle(color: Colors.purple, fontSize: 30)),
              TextSpan(
                  text: '2',
                  style: TextStyle(
                      color: Colors.purple,
                      fontSize: 30,
                      fontFeatures: [FontFeature.subscripts()])),
              TextSpan(
                text: 'S',
                style: TextStyle(color: Colors.purple, fontSize: 30),
              ),
              TextSpan(
                text: 'O',
                style: TextStyle(color: Colors.purple, fontSize: 30),
              ),
              TextSpan(
                text: '4',
                style: TextStyle(
                    color: Colors.purple,
                    fontSize: 30,
                    fontFeatures: [FontFeature.subscripts()]),
              ),
            ]),
          ),
        ));
  }
}
```

# **Flutter：自定义Android系统导航栏**

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/image/Screen-Shot-2021-07-11-at-17.17.37.jpg)

本文将向您展示如何在使用 Flutter 编写的 Android 应用程序中自定义系统导航栏，例如更改背景颜色和图标亮度，在应用程序运行时隐藏或显示导航栏。

目录



[概述](https://www.kindacode.com/article/flutter-customize-the-android-system-navigation-bar/#Overview)[完整示例](https://www.kindacode.com/article/flutter-customize-the-android-system-navigation-bar/#The_Complete_Example)[预览](https://www.kindacode.com/article/flutter-customize-the-android-system-navigation-bar/#Preview)[编码](https://www.kindacode.com/article/flutter-customize-the-android-system-navigation-bar/#The_code)[结论](https://www.kindacode.com/article/flutter-customize-the-android-system-navigation-bar/#Conclusion)

## 概述

Android 系统导航栏位于屏幕底部。它包含 3 个按钮来处理导航的关键方面：后退按钮、主页按钮和概览按钮（用于打开您最近使用过的应用程序和 Chrome 标签页的缩略图列表）。

一般情况下，系统导航栏有黑色背景色和浅色图标按钮：

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/image/Screen-Shot-2021-07-11-at-17.11.32.jpg)

或带有深色图标的白色背景：

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/image/Screen-Shot-2021-07-11-at-17.10.38.jpg)

在 Flutter 中，您可以使用**SystemChrome** API来控制系统栏。

- **setSystemUIOverlayStyle**方法可用于指定用于可见的系统覆盖（如果有）的样式。
- **setEnabledSystemUIOverlays**方法可用于确定底部系统栏是否可见。

为了更清楚，请参见下面的示例。

## 完整示例

### 预览

此示例应用程序有一个自定义底部导航栏，带有橙色背景颜色和灯光图标。屏幕中央有 2 个按钮。紫色的用于隐藏导航栏，绿色的用于显示导航栏。

这是它的工作原理：

1.00

<video class="" autoplay="" controls="" loop="" muted="" data-lazy-type="video" data-src="https://www.kindacode.com/wp-content/uploads/2021/07/Flutter-Android-navigation-bar.mp4" playsinline="" src="https://www.kindacode.com/wp-content/uploads/2021/07/Flutter-Android-navigation-bar.mp4" __idm_id__="699284482" style="margin: 0px; padding: 0px; box-sizing: border-box; width: 720px;"></video>

### 编码

广告

不要忘记导入**服务**库：

```
import 'package:flutter/services.dart';
```

**main.dart**文件中的完整代码：

```
// main.dart
import 'package:flutter/material.dart';
import 'package:flutter/services.dart';
import 'dart:io';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
        // Remove the debug banner
        debugShowCheckedModeBanner: false,
        title: 'Kindacode.com',
        theme: ThemeData(
          primarySwatch: Colors.green,
        ),
        home: HomePage());
  }
}

class HomePage extends StatefulWidget {
  const HomePage({Key? key}) : super(key: key);

  @override
  _HomePageState createState() => _HomePageState();
}

class _HomePageState extends State<HomePage> {
  @override
  void initState() {
    super.initState();
    if (Platform.isAndroid) {
      SystemChrome.setSystemUIOverlayStyle(SystemUiOverlayStyle(
          systemNavigationBarColor: Colors.deepOrange,
          systemNavigationBarIconBrightness: Brightness.light));
    }
  }

  void _hide() {
    // This will hide the bottom system navigation bar
    // Only the status bar on the top will show up
    SystemChrome.setEnabledSystemUIOverlays([SystemUiOverlay.top]);
  }

  void _show() {
    // This will show both the top status bar and the bottom navigation bar
    SystemChrome.setEnabledSystemUIOverlays(
        [SystemUiOverlay.top, SystemUiOverlay.bottom]);
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Kindacode.com'),
      ),
      body: Center(
        child: Row(
          mainAxisAlignment: MainAxisAlignment.spaceAround,
          children: [
            ElevatedButton(
              child: Text('Hide The System Bar'),
              onPressed: _hide,
              style: ElevatedButton.styleFrom(primary: Colors.purple),
            ),
            ElevatedButton(
              child: Text('Show The System Bar'),
              onPressed: _show,
            ),
          ],
        ),
      ),
    );
  }
}
```

## 结论

您已经学习了如何在使用 Flutter 构建的 Android 应用程序中配置和控制系统导航栏。



# 使用sqflite数据库时No implementation found for method getDatabasesPath on channel com.tekartik.sqflite

我终于找到了我的答案。**我必须在 MainActivity 中添加 Sqflite 插件：`SqflitePlugin.registerWith(registrarFor("com.tekartik.sqflite.SqflitePlugin"))`**

我的 MainActivity 更改如下：

```dart
import android.os.Bundle
import io.flutter.app.FlutterActivity
import io.flutter.plugins.imagepicker.ImagePickerPlugin
import com.tekartik.sqflite.SqflitePlugin


class MainActivity : FlutterActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        ImagePickerPlugin.registerWith(
                registrarFor("io.flutter.plugins.imagepicker.ImagePickerPlugin"))
        SqflitePlugin.registerWith(registrarFor("com.tekartik.sqflite.SqflitePlugin"))

    }

}
```

# 由 ScaffoldMessenger 管理的 SnackBars



##  变更说明

先前的方法要求`Scaffold`显示`SnackBar`.

```dart
Scaffold(
  key: scaffoldKey,
  body: Builder(
    builder: (BuildContext context) {
      return GestureDetector(
        onTap: () {
          Scaffold.of(context).showSnackBar(SnackBar(
            content: const Text('snack'),
            duration: const Duration(seconds: 1),
            action: SnackBarAction(
              label: 'ACTION',
              onPressed: () { },
            ),
          ));
        },
        child: const Text('SHOW SNACK'),
      );
    },
  )
);
```

新方法要求`ScaffoldMessenger`显示`SnackBar`. 在这种情况下，`Builder`不再需要提供一个新的作用域，`BuildContext`它在`Scaffold`.

```dart
Scaffold(
  key: scaffoldKey,
  body: GestureDetector(
    onTap: () {
      ScaffoldMessenger.of(context).showSnackBar(SnackBar(
        content: const Text('snack'),
        duration: const Duration(seconds: 1),
        action: SnackBarAction(
          label: 'ACTION',
          onPressed: () { },
        ),
      ));
    },
    child: const Text('SHOW SNACK'),
  ),
);
```

`SnackBar`在过渡期间呈现 a时，`SnackBar`完成一个`Hero`动画，平滑地移动到下一页。

在`ScaffoldMessenger`创建中，所有后代一个范围 `Scaffold`S寄存器接收`SnackBar`s，这是他们在这些转变是如何坚持。当使用由`ScaffoldMessenger`提供 的根时`MaterialApp`，所有后代`Scaffold`s 都会接收`SnackBar`s，除非`ScaffoldMessenger`在树的更下方创建新的作用域。通过实例化您自己的`ScaffoldMessenger`，您可以控制哪些`Scaffold`s 接收`SnackBar`s，哪些不基于您的应用程序的上下文。

该方法`debugCheckHasScaffoldMessenger`可用于断言给定的上下文具有`ScaffoldMessenger`祖先。尝试在`SnackBar`没有`ScaffoldMessenger`祖先的情况下呈现 a 会导致如下断言：

```dart
No ScaffoldMessenger widget found.
Scaffold widgets require a ScaffoldMessenger widget ancestor.
Typically, the ScaffoldMessenger widget is introduced by the MaterialApp
at the top of your application widget tree.
```

## 迁移指南

迁移前的代码：

```dart
// The ScaffoldState of the current context was used for managing SnackBars.
Scaffold.of(context).showSnackBar(mySnackBar);
Scaffold.of(context).hideCurrentSnackBar(mySnackBar);
Scaffold.of(context).removeCurrentSnackBar(mySnackBar);

// If a Scaffold.key is specified, the ScaffoldState can be directly
// accessed without first obtaining it from a BuildContext via
// Scaffold.of. From the key, use the GlobalKey.currentState
// getter. This was previously used to manage SnackBars.
final GlobalKey<ScaffoldState> scaffoldKey = GlobalKey<ScaffoldState>();
Scaffold(
  key: scaffoldKey,
  body: ...,
);

scaffoldKey.currentState.showSnackBar(mySnackBar);
scaffoldKey.currentState.hideCurrentSnackBar(mySnackBar);
scaffoldKey.currentState.removeCurrentSnackBar(mySnackBar);
```

迁移后的代码：

```dart
// The ScaffoldMessengerState of the current context is used for managing SnackBars.
ScaffoldMessenger.of(context).showSnackBar(mySnackBar);
ScaffoldMessenger.of(context).hideCurrentSnackBar(mySnackBar);
ScaffoldMessenger.of(context).removeCurrentSnackBar(mySnackBar);

// If a ScaffoldMessenger.key is specified, the ScaffoldMessengerState can be directly
// accessed without first obtaining it from a BuildContext via
// ScaffoldMessenger.of. From the key, use the GlobalKey.currentState
// getter. This is used to manage SnackBars.
final GlobalKey<ScaffoldMessengerState> scaffoldMessengerKey = GlobalKey<ScaffoldMessengerState>();
ScaffoldMessenger(
  key: scaffoldMessengerKey,
  child: ...
)

scaffoldMessengerKey.currentState.showSnackBar(mySnackBar);
scaffoldMessengerKey.currentState.hideCurrentSnackBar(mySnackBar);
scaffoldMessengerKey.currentState.removeCurrentSnackBar(mySnackBar);

// The root ScaffoldMessenger can also be accessed by providing a key to 
// MaterialApp.scaffoldMessengerKey. This way, the ScaffoldMessengerState can be directly accessed
// without first obtaining it from a BuildContext via ScaffoldMessenger.of. From the key, use
// the GlobalKey.currentState getter.
final GlobalKey<ScaffoldMessengerState> rootScaffoldMessengerKey = GlobalKey<ScaffoldMessengerState>();
MaterialApp(
  scaffoldMessengerKey: rootScaffoldMessengerKey,
  home: ...
)

rootScaffoldMessengerKey.currentState.showSnackBar(mySnackBar);
rootScaffoldMessengerKey.currentState.hideCurrentSnackBar(mySnackBar);
rootScaffoldMessengerKey.currentState.removeCurrentSnackBar(mySnackBar);
```





要经常看的https://flutter.dev/docs/release/breaking-changes

# Flutter 和 SQLite：CRUD 示例（2021）

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/image/Flutter-SQLite.jpg)

SQLite 是一种快速关系数据库，可用于为移动应用程序离线存储数据。CRUD 表示创建、读取、更新和删除，这是持久化存储的四个基本操作。

在本文中，我们将构建一个使用 SQLite 持久化数据的小型 Flutter 应用程序。



## 先决条件

为了完全理解这个例子，你应该：

- Flutter 基础知识
- 知道什么是 SQLite。如果没有，请先查看[官方文档](https://www.sqlite.org/index.html)。
- 一台安装了 Flutter 的电脑，一个代码编辑器（VS Code 很棒）。

## 应用预览

我们将要制作的应用程序是一个离线日记，可以让用户记录他们白天所做的活动。为简单起见，我们将这些活动中的每一个都称为“日志”或“项目”。

在本教程的范围内，“活动”、“项目”和“日记”可以互换。

该应用程序有一个浮动按钮，可用于显示底部工作表。该底部工作表包含 2 个对应于“标题”和“描述”的文本字段。这些文本字段用于创建新的“项目”或更新现有的“项目”。

保存的“项目”从 SQLite 数据库中获取并以列表视图显示。每个“项目”都有一个更新按钮和一个删除按钮。

一个演示胜过千言万语：

1.00

<video class="" autoplay="" controls="" loop="" muted="" data-lazy-type="video" data-src="https://www.kindacode.com/wp-content/uploads/2021/07/Flutter-SQLite.mp4" src="https://www.kindacode.com/wp-content/uploads/2021/07/Flutter-SQLite.mp4" __idm_id__="34991105" style="margin: 0px; padding: 0px; box-sizing: border-box; width: 720px;"></video>

## 数据库结构

广告

我们将创建一个名为**kindacode.db**的 SQLite 数据库。它只有一个名为**items 的**表。下面是表的结构：

| 柱子   | 类型   | 描述                                   |
| ------ | ------ | -------------------------------------- |
| ID     | 整数   | 一个活动的id                           |
| 标题   | 文本   | 活动名称                               |
| 描述   | 文本   | 活动详情                               |
| 创建于 | 时间戳 | 项目的创建时间。它将由 SQLite 自动添加 |

## 编码

\1. 创建一个新的 Flutter 项目。在**lib**文件夹中，添加一个名为**sql_helper.dart**的新文件。

项目结构：

```
.
├── main.dart
└── sql_helper.dart
```

安装[sqflite](https://pub.dev/packages/sqflite/install)插件（注意名字有一个“f”）：

```
flutter pub add sqflite
```

\2. **sql_helper.dar** t 中的完整代码：

```
import 'package:sqflite/sqflite.dart' as sql;

class SQLHelper {
  static Future<void> createTables(sql.Database database) async {
    await database.execute("""CREATE TABLE items(
        id INTEGER PRIMARY KEY AUTOINCREMENT NOT NULL,
        title TEXT,
        description TEXT,
        createdAt TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP
      )
      """);
  }
// id: the id of a item
// title, description: name and description of your activity
// created_at: the time that the item was created. It will be automatically handled by SQLite

  static Future<sql.Database> db() async {
    return sql.openDatabase(
      'kindacode.db',
      version: 1,
      onCreate: (sql.Database database, int version) async {
        await createTables(database);
      },
    );
  }

  // Create new item (journal)
  static Future<int> createItem(String title, String? descrption) async {
    final db = await SQLHelper.db();

    final data = {'title': title, 'description': descrption};
    final id = await db.insert('items', data,
        conflictAlgorithm: sql.ConflictAlgorithm.replace);
    return id;
  }

  // Read all items (journals)
  static Future<List<Map<String, dynamic>>> getItems() async {
    final db = await SQLHelper.db();
    return db.query('items', orderBy: "id");
  }

  // Read a single item by id
  // The app doesn't use this method but I put here in case you want to see it
  static Future<List<Map<String, dynamic>>> getItem(int id) async {
    final db = await SQLHelper.db();
    return db.query('items', where: "id = ?", whereArgs: [id], limit: 1);
  }

  // Update an item by id
  static Future<int> updateItem(
      int id, String title, String? descrption) async {
    final db = await SQLHelper.db();

    final data = {
      'title': title,
      'description': descrption,
      'createdAt': DateTime.now().toString()
    };

    final result =
        await db.update('items', data, where: "id = ?", whereArgs: [id]);
    return result;
  }

  // Delete
  static Future<void> deleteItem(int id) async {
    final db = await SQLHelper.db();
    try {
      await db.delete("items", where: "id = ?", whereArgs: [id]);
    } catch (err) {
      print("Something went wrong when deleting an item: $err");
    }
  }
}
```

广告

\3. **main.dart 中的**完整代码：

```
// main.dart
import 'package:flutter/material.dart';

import 'sql_helper.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
        // Remove the debug banner
        debugShowCheckedModeBanner: false,
        title: 'Kindacode.com',
        theme: ThemeData(
          primarySwatch: Colors.orange,
        ),
        home: HomePage());
  }
}

class HomePage extends StatefulWidget {
  const HomePage({Key? key}) : super(key: key);

  @override
  _HomePageState createState() => _HomePageState();
}

class _HomePageState extends State<HomePage> {
  // All journals
  List<Map<String, dynamic>> _journals = [];

  bool _isLoading = true;
  // This function is used to fetch all data from the database
  void _refreshJournals() async {
    final data = await SQLHelper.getItems();
    setState(() {
      _journals = data;
      _isLoading = false;
    });
  }

  @override
  void initState() {
    super.initState();
    _refreshJournals(); // Loading the diary when the app starts
  }

  TextEditingController _titleController = new TextEditingController();
  TextEditingController _descriptionController = new TextEditingController();

  // This function will be triggered when the floating button is pressed
  // It will also be triggered when you want to update an item
  void _showForm(int? id) async {
    if (id != null) {
      // id == null -> create new item
      // id != null -> update an existing item
      final existingJournal =
          _journals.firstWhere((element) => element['id'] == id);
      _titleController.text = existingJournal['title'];
      _descriptionController.text = existingJournal['description'];
    }

    showModalBottomSheet(
        context: context,
        elevation: 5,
        builder: (_) => Container(
              padding: EdgeInsets.all(15),
              width: double.infinity,
              height: 300,
              child: Column(
                crossAxisAlignment: CrossAxisAlignment.end,
                children: [
                  TextField(
                    controller: _titleController,
                    decoration: InputDecoration(hintText: 'Title'),
                  ),
                  SizedBox(
                    height: 10,
                  ),
                  TextField(
                    controller: _descriptionController,
                    decoration: InputDecoration(hintText: 'Description'),
                  ),
                  SizedBox(
                    height: 20,
                  ),
                  ElevatedButton(
                    onPressed: () async {
                      // Save new journal
                      if (id == null) {
                        await _addItem();
                      }

                      if (id != null) {
                        await _updateItem(id);
                      }

                      // Clear the text fields
                      _titleController.text = '';
                      _descriptionController.text = '';

                      // Close the bottom sheet
                      Navigator.of(context).pop();
                    },
                    child: Text(id == null ? 'Create New' : 'Update'),
                  )
                ],
              ),
            ));
  }

// Insert a new journal to the database
  Future<void> _addItem() async {
    await SQLHelper.createItem(
        _titleController.text, _descriptionController.text);
    _refreshJournals();
  }

  // Update an existing journal
  Future<void> _updateItem(int id) async {
    await SQLHelper.updateItem(
        id, _titleController.text, _descriptionController.text);
    _refreshJournals();
  }

  // Delete an item
  void _deleteItem(int id) async {
    await SQLHelper.deleteItem(id);
    ScaffoldMessenger.of(context).showSnackBar(SnackBar(
      content: Text('Successfully deleted a journal!'),
    ));
    _refreshJournals();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Kindacode.com'),
      ),
      body: _isLoading
          ? Center(
              child: CircularProgressIndicator(),
            )
          : ListView.builder(
              itemCount: _journals.length,
              itemBuilder: (context, index) => Card(
                color: Colors.orange[200],
                margin: EdgeInsets.all(15),
                child: ListTile(
                    title: Text(_journals[index]['title']),
                    subtitle: Text(_journals[index]['description']),
                    trailing: SizedBox(
                      width: 100,
                      child: Row(
                        children: [
                          IconButton(
                            icon: Icon(Icons.edit),
                            onPressed: () => _showForm(_journals[index]['id']),
                          ),
                          IconButton(
                            icon: Icon(Icons.delete),
                            onPressed: () =>
                                _deleteItem(_journals[index]['id']),
                          ),
                        ],
                      ),
                    )),
              ),
            ),
      floatingActionButton: FloatingActionButton(
        child: Icon(Icons.add),
        onPressed: () => _showForm(null),
      ),
    );
  }
}
```

## 结论

您已经学习了 SQLite 的基础知识，并了解了在 Flutter 应用程序中使用 SQLite 的端到端示例。从这里，您可以构建更复杂的应用程序，离线存储大量数据。
