---
title: "如何发布你的第一个flutter包"
date: 2021-08-15T17:24:16+08:00
draft: false
tags: ["Flutter"]
categories: ["Flutter"]
---

长期使用 Flutter 包？
你想回馈吗？
您想发布自己的软件包并帮助社区吗？
但是不知道怎么样？
**不用担心。** **我来做你的后背。**
在这篇博客中，我将带您了解如何在**Flutter**官方网站[***pub.dev\***](http://pub.dev/)上发布您自己的**Flutter 包的**每一步

![img](https://miro.medium.com/max/1400/1*3AxRgomPxY8gZPNNYfe9vQ.png)

**先决条件：**
\- 热心帮助
\- 了解 Flutter
\- Git

# 所以我们开始吧!! 👏👏**

**Flutter 包总是有一个返回小部件的类此外，该类不得具有任何 hard-coded values.

所有的 functionalities 要提供给用户，将其创建为一个属性(变量)并创建一个构造函数来获取值

例如：
如果您希望用户设置容器的高度，则创建一个名为**double height**的属性**。**添加构造函数，然后将其传递给 Container 的 height 属性。

```
class <class_name>{
     double height;
     class_name({this.height});
     .
     .
     .
     .
     Container(
        height:height,
        .
        .
        .
     ),
}
```

创建代码后，在应用程序中使用它来测试它并获得更好的理解。

**S**那么，让我们从如何发布包的步骤开始吧！！💙💙💙💙**

## **第 1 步：首先创建新的 flutter 包！！**

- 打开安卓工作室
- 点击**创建新的 Flutter 项目**
- 点击**新建 Flutter 包**

## **第2步：**给这个**项目的名称**，你希望被命名您的包之一。因此，您的项目名称将是您的包名称。

## **第3步：**将项目放到**Github 上**。保持公开。

## **第4步：这是最重要的一步！！

编辑 Pubspec.YAML 文件。**

- **添加**包装**说明**(最少 20 个字)。发布后，这会为你在您在[pub.dev](http://pub.dev/)上获得[积分](http://pub.dev/)
- **删除作者，**因为它现在不使用。
- **添加主页**。HomePage 可以是您自己的网站，也可以提供 Github Profile Page 链接。
- **添加** **存储库**。不要在这方面做错！！如果链接出错，将不会生成[pub.dev](http://pub.dev/)上的**“问题”**链接。下面是一个例子：

```dart
正确链接 https://github.com/AbhishekDoshi26/contactus
错误链接 https://github.com/AbhishekDoshi26/contactus.git
```

- **添加版本号。**每当您更新包代码时，都必须更改版本号。

## **第5步：在dart文件中编写代码。**这是一个名为[**contactus**](https://pub.dev/packages/contactus)的包的代码示例



```
library contactus;import 'package:flutter/material.dart';
import 'package:typicons_flutter/typicons_flutter.dart';
import 'package:url_launcher/url_launcher.dart';///Class for adding contact details/profile details as a complete new page in your flutter app.
class ContactUs extends StatelessWidget {
  ///Logo of the Company/individual
  final ImageProvider logo;///Phone Number of the company/individual
  final String phoneNumber;///Website of company/individual
  final String website;///Email ID of company/individual
  final String email;///Twitter Handle of Company/Individual
  final String twitterHandle;///Linkedin URL of company/individual
  final String linkedinURL;///Github User Name of the company/individual
  final String githubUserName;///Name of the Company/individual
  final String companyName;///TagLine of the Company or Position of the individual
  final String tagLine;///Instagram User Name of the company/individual
  final String instagram;///TextColor of the text which will be displayed on the card.
  final Color textColor;///Color of the Card.
  final Color cardColor;///Color of the company/individual name displayed.
  final Color companyColor;///Color of the tagLine of the Company/Individual to be displayed.
  final Color taglineColor;///Constructor which sets all the values.
  ContactUs({
    @required this.logo,
    @required this.companyName,
    @required this.email,
    @required this.textColor,
    @required this.cardColor,
    @required this.companyColor,
    @required this.taglineColor,
    this.phoneNumber,
    this.website,
    this.twitterHandle,
    this.linkedinURL,
    this.githubUserName,
    this.tagLine,
    this.instagram,
  });@override
  Widget build(BuildContext context) {
    return SafeArea(
      child: SingleChildScrollView(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: <Widget>[
            CircleAvatar(
              radius: 50.0,
              backgroundImage: logo,
            ),
            Text(
              companyName,
              style: TextStyle(
                fontFamily: 'Pacifico',
                fontSize: 40.0,
                color: companyColor,
                fontWeight: FontWeight.bold,
              ),
            ),
            Visibility(
              visible: tagLine != null,
              child: Text(
                tagLine,
                style: TextStyle(
                  color: taglineColor,
                  fontSize: 20.0,
                  letterSpacing: 2.0,
                  fontWeight: FontWeight.bold,
                ),
              ),
            ),
            SizedBox(
              height: 10.0,
            ),
            Divider(
              color: Colors.teal[200],
              thickness: 4,
              indent: 50.0,
              endIndent: 50.0,
            ),
            SizedBox(
              height: 10.0,
            ),
            Visibility(
              visible: website != null,
              child: Card(
                margin: EdgeInsets.symmetric(
                  vertical: 10.0,
                  horizontal: 25.0,
                ),
                shape: RoundedRectangleBorder(
                  borderRadius: BorderRadius.circular(50.0),
                ),
                color: cardColor,
                child: ListTile(
                  leading: Icon(Typicons.link),
                  title: Text(
                    'Website',
                    style: TextStyle(
                      color: textColor,
                    ),
                  ),
                  onTap: () {
                    launch(website);
                  },
                ),
              ),
            ),
            Visibility(
              visible: phoneNumber != null,
              child: Card(
                margin: EdgeInsets.symmetric(
                  vertical: 10.0,
                  horizontal: 25.0,
                ),
                shape: RoundedRectangleBorder(
                  borderRadius: BorderRadius.circular(50.0),
                ),
                color: cardColor,
                child: ListTile(
                  leading: Icon(Typicons.phone),
                  title: Text(
                    'Phone Number',
                    style: TextStyle(
                      color: textColor,
                    ),
                  ),
                  onTap: () {
                    launch('tel:' + phoneNumber);
                  },
                ),
              ),
            ),
            Card(
              margin: EdgeInsets.symmetric(
                vertical: 10.0,
                horizontal: 25.0,
              ),
              shape: RoundedRectangleBorder(
                borderRadius: BorderRadius.circular(50.0),
              ),
              color: cardColor,
              child: ListTile(
                leading: Icon(Typicons.mail),
                title: Text(
                  'Email ID',
                  style: TextStyle(
                    color: textColor,
                  ),
                ),
                onTap: () {
                  launch('mailto:' + email);
                },
              ),
            ),
            Visibility(
              visible: twitterHandle != null,
              child: Card(
                margin: EdgeInsets.symmetric(
                  vertical: 10.0,
                  horizontal: 25.0,
                ),
                shape: RoundedRectangleBorder(
                  borderRadius: BorderRadius.circular(50.0),
                ),
                color: cardColor,
                child: ListTile(
                  leading: Icon(Typicons.social_twitter),
                  title: Text(
                    'Twitter',
                    style: TextStyle(
                      color: textColor,
                    ),
                  ),
                  onTap: () {
                    launch('https://twitter.com/' + twitterHandle);
                  },
                ),
              ),
            ),
            Visibility(
              visible: instagram != null,
              child: Card(
                margin: EdgeInsets.symmetric(
                  vertical: 10.0,
                  horizontal: 25.0,
                ),
                shape: RoundedRectangleBorder(
                  borderRadius: BorderRadius.circular(50.0),
                ),
                color: cardColor,
                child: ListTile(
                  leading: Icon(Typicons.social_instagram),
                  title: Text(
                    'Instagram',
                    style: TextStyle(
                      color: textColor,
                    ),
                  ),
                  onTap: () {
                    launch('https://instagram.com/' + instagram);
                  },
                ),
              ),
            ),
            Visibility(
              visible: githubUserName != null,
              child: Card(
                margin: EdgeInsets.symmetric(
                  vertical: 10.0,
                  horizontal: 25.0,
                ),
                shape: RoundedRectangleBorder(
                  borderRadius: BorderRadius.circular(50.0),
                ),
                color: cardColor,
                child: ListTile(
                  leading: Icon(Typicons.social_github),
                  title: Text(
                    'Github',
                    style: TextStyle(
                      color: textColor,
                    ),
                  ),
                  onTap: () {
                    launch('https://github.com/' + githubUserName);
                  },
                ),
              ),
            ),
            Visibility(
              visible: linkedinURL != null,
              child: Card(
                margin: EdgeInsets.symmetric(
                  vertical: 10.0,
                  horizontal: 25.0,
                ),
                shape: RoundedRectangleBorder(
                  borderRadius: BorderRadius.circular(50.0),
                ),
                color: cardColor,
                child: ListTile(
                  leading: Icon(Typicons.social_linkedin),
                  title: Text(
                    'Linkedin',
                    style: TextStyle(
                      color: textColor,
                    ),
                  ),
                  onTap: () {
                    launch(linkedinURL);
                  },
                ),
              ),
            ),
          ],
        ),
      ),
    );
  }
}///Class for adding contact details of the developer in your bottomNavigationBar in your flutter app.
class ContactUsBottomAppBar extends StatelessWidget {
  ///Color of the text which will be displayed in the bottomNavigationBar
  final Color textColor;///Color of the background of the bottomNavigationBar
  final Color backgroundColor;///Email ID Of the company/developer on which, when clicked by the user, the respective mail app will be opened.
  final String email;///Name of the company or the developer
  final String companyName;///Size of the font in bottomNavigationBar
  final double fontSize;ContactUsBottomAppBar(
      {@required this.textColor,
      @required this.backgroundColor,
      @required this.email,
      @required this.companyName,
      this.fontSize = 15.0});
  @override
  Widget build(BuildContext context) {
    return FlatButton(
      color: backgroundColor,
      highlightColor: Colors.transparent,
      splashColor: Colors.transparent,
      child: Text(
        'Designed and Developed by $companyName 💙\nWant to contact?',
        textAlign: TextAlign.center,
        style: TextStyle(color: textColor, fontSize: fontSize),
      ),
      onPressed: () {
        launch('mailto:$email');
      },
    );
  }
}
```

## **第 6 步：创建文档。**

对于文档，您需要使用**///**来提供特定属性/方法/类等的文档。***/// 将创建文档并// 将是注释。\***

```
class ContactUs extends StatelessWidget {
  ///Logo of the Company/individual
  final ImageProvider logo;
```

此外，在为您的类创建手动文档后，让我们创建预定义的文档。在包位置打开命令提示符并运行以下命令：

```
dartdoc
```

## **第7步：**打开**CHANGELOG.md**文件，在文件中写入版本号、发布日期和描述。这是相同的示例：

```
## [1.0.0] - 16/08/2021.
Added Icons for all the social networks
```

## **第 8 步：**创建文件**LICENSE.txt**并添加任何许可证，

例如**MIT License**。您可以从 Internet 获取任何许可证的模板。以下是[contactus 包](https://pub.dev/packages/contactus)的 MIT 许可证示例：

```
Copyright (c) 2020 Abhishek Doshi
Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:
The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.
THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

## **第 9 步：创建示例。**

- 在包文件夹本身内，创建一个**新的 flutter 项目，命名为 example。**(不要更改名称。名称必须是强制性**示例，**因为它将在[pub.dev 中使用](http://pub.dev/))
- 要在不发布的情况下使用示例中的包，请在**pubspec.yaml 中**添加以下行：

```
package_name:
    path: Complete path of the package on your computer.
```

- 以下是示例中如何使用[contactus 包](https://pub.dev/packages/contactus)或您自己的包的代码片段：

```
import 'package:contactus/contactus.dart';
import 'package:flutter/material.dart';void main() => runApp(MyApp());class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      home: Scaffold(
        bottomNavigationBar: ContactUsBottomAppBar(
          companyName: 'Abhishek Doshi',
          textColor: Colors.white,
          backgroundColor: Colors.teal.shade300,
          email: 'adoshi26.ad@gmail.com',
        ),
        backgroundColor: Colors.teal,
        body: Container(),
      ),
    );
  }
}
```

## **第 10 步：全部推送到 Github 存储库**

## **第 11 步：**在 repo 位置**打开命令提示符**。现在运行以下命令：

```
flutter pub publish --dry-run
```

如果遇到任何建议或警告，请进行所需的更改。

## **第 12 步：**如果一切正常，请运行以下命令发布包。

```
flutter pub publish
```



Tadaaaaaaaaaaaaaa!!!!!!!!!🌟🎉👏 您的包裹已发布！！

**现在，无论何时更改包/示例的代码/任何文件中的任何内容，都需要更新以下文件：**

- pubspec.yaml(版本号)
- CHANGELOG.md(添加新版本、日期和描述)

**每当你觉得自己什么都没有做的时候，就在开始时和自己比较！！**