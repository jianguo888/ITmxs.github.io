---
title: "如何在Flutter中使Expanded中的文本可滚动"
date: 2021-09-02T12:36:16+08:00
draft: false
tags: ["Flutter"]
categories: ["Flutter"]
---

我正在忙着用闪存卡构建一个应用程序，其中包含有关它们的信息，但我遇到了一个我似乎无法弄清楚的愚蠢问题。

为了给出上下文，卡片小部件目前如下所示:

![img](https://i.stack.imgur.com/7Bmrc.png)

如您所见，文本被切断了。我试过将 Expanded 更改为 SingleChildScrollView，但最终我遇到了这种情况:

![img](https://i.stack.imgur.com/nj2jC.png)

我知道解决方案可能很简单，但我已经摆弄了几个小时

```dart
  @override
  Widget build(BuildContext context) {
    // TODO: implement build
    return Container(
      padding: EdgeInsets.symmetric(vertical: 15, horizontal: 10),
      decoration: BoxDecoration(
        color: Colors.indigo,
        borderRadius: BorderRadius.circular(20),
      ),
      child: Column(
        mainAxisAlignment: MainAxisAlignment.spaceBetween,
        children: <Widget>[
          Row(
            mainAxisAlignment: MainAxisAlignment.start,
            children: <Widget>[
              Image.asset(
                getCategoryImage(),
                width: 50
              ),
              Container(width: 10),
              Text(
                title,
                style: TextStyle(
                  fontFamily: "Estherilla",
                  fontSize: 30,
                  color: Colors.white
                ),
              )
            ],
          ),
          SingleChildScrollView(
            child: Padding(
              padding: const EdgeInsets.symmetric(vertical: 25),
              child: Text(
                content,
                style: TextStyle(
                  fontSize: 25,
                  color: Colors.white
                ),
              ),
            )
          ),
          Row(
            mainAxisAlignment: MainAxisAlignment.end,
            children: <Widget>[
              Text(
                "#$author",
                style: TextStyle(
                  fontFamily: "Estherilla",
                  color: Colors.white,
                  fontSize: 20
                ),
              )
            ],
          )
        ],
      ),
    );
  }
```



**最佳答案**

包裹您的 `SingleChildScrollView`里面`Expanded`像这样的小部件

```
 Column(
   ...

   Expanded(
     child: SingleChildScrollView(...)
   ),

   ...
 )
```





```dart
package com.example.flutter_contacts

import android.app.Activity
import android.content.Intent
import android.os.Bundle
import android.provider.ContactsContract
import io.flutter.app.FlutterActivity
import io.flutter.plugin.common.MethodChannel
import io.flutter.plugins.GeneratedPluginRegistrant

class MainActivity : FlutterActivity() {

    var lastResult: MethodChannel.Result? = null

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        GeneratedPluginRegistrant.registerWith(this)

        MethodChannel(flutterView, CHANNEL).setMethodCallHandler { call, result ->
            lastResult = result
            launchContactActivity()
        }
    }

    override fun onActivityResult(requestCode: Int, resultCode: Int, data: Intent?) {
        super.onActivityResult(requestCode, resultCode, data)
        if (requestCode == REQUEST_CODE) {
            if (resultCode == Activity.RESULT_OK) {
                lastResult?.success("Done!")
            } else {
                lastResult?.error("Error", "Can't launch contacts", "")
            }
        }
    }

    private fun launchContactActivity() {
        val intent = Intent(Intent.ACTION_VIEW)
        intent.type = ContactsContract.Contacts.CONTENT_TYPE
        startActivityForResult(intent, REQUEST_CODE)
    }

    companion object {
        private const val CHANNEL = "flutter_contacts/launch_contacts"
        private const val REQUEST_CODE = 42
    }
}
```



```dart
import 'package:flutter/material.dart';
import 'package:flutter/services.dart';

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: MyHomePage(),
    );
  }
}

class MyHomePage extends StatefulWidget {
  @override
  _MyHomePageState createState() => _MyHomePageState();
}

class _MyHomePageState extends State<MyHomePage> {
  static const platform =
      const MethodChannel('flutter_contacts/launch_contacts');

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Contacts sample'),
      ),
      body: Center(
        child: RaisedButton(
          child: Text("Launch contact activity"),
          onPressed: () async => launchContacts(),
        ),
      ),
    );
  }

  void launchContacts() async {
    try {
      await platform.invokeMethod('launch');
    } on PlatformException catch (e) {
      print("Failed to launch contacts: ${e.message}");
    }
    setState(() {
    });
  }
}
```





# 扫描条形码打开链接

我有一个关于 Flutter 中二维码扫描的小问题。二维码读取数据成功后如何打开网址？

我使用[这个](https://pub.dev/packages/barcode_scan)包来使用 QR 码，[这](https://pub.dev/packages/url_launcher#-installing-tab-)用于打开一个 URL，这是我检查来自 QR 码的数据值是否为 URL 的功能，如果它是 URL，则运行该功能以打开网站。

```dart
checkingValue() {
    if (_result != null || _result != "") {
      if (_result.contains("https") || _result.contains("http")) {
        return _launchURL(_result);
      } else {
        Toast.show("Invalide URL", context,
            duration: Toast.LENGTH_LONG, gravity: Toast.BOTTOM);
      }
    } else {
      return null;
    }
  }

  _launchURL(String urlQRCode) async {
    String url = urlQRCode;
    if (await canLaunch(url)) {
      await launch(url);
    } else {
      throw 'Could not launch $url';
    }
  }
```

该`_result`变量是一个字符串摆脱QR码数据的值。

这是我的全部代码：

```dart
class _ScannerPageState extends State<ScannerPage> {

  String _password;
  String _result = "";

  Future _scanQR() async {
    try {
      String qrResult = await BarcodeScanner.scan();
      setState(() {
        _result = qrResult;
      });
    } on PlatformException catch (ex) {
      if (ex.code == BarcodeScanner.CameraAccessDenied) {
        setState(() {
          _result = "Camera permission was denied";
          Toast.show(_result, context,
              duration: Toast.LENGTH_LONG, gravity: Toast.BOTTOM);
        });
      } else {
        setState(() {
          _result = "Unknown Error $ex";
          Toast.show(_result, context,
              duration: Toast.LENGTH_LONG, gravity: Toast.BOTTOM);
        });
      }
    } on FormatException {
      setState(() {
        _result = "You pressed the back button before scanning anything";
        Toast.show(_result, context,
            duration: Toast.LENGTH_LONG, gravity: Toast.BOTTOM);
      });
    } catch (ex) {
      setState(() {
        _result = "Unknown Error $ex";
        Toast.show(_result, context,
            duration: Toast.LENGTH_LONG, gravity: Toast.BOTTOM);
      });
    }
  }

  @override
  Widget build(BuildContext context) {
    return WillPopScope(
      onWillPop: () {
        return showDialog(
            barrierDismissible: false,
            context: context,
            builder: (BuildContext context) {
              return PopUp(
                content: "Are you sure want to exit?",
                cancelText: "No",
                acceptText: "Yes",
                onTapCancel: () => Navigator.of(context).pop(),
                onTapAccept: () async {
                  await SessionManager().removeSession();//
                  await SystemNavigator.pop();
                },
              );
            }
        );
      },
      child: Scaffold(
        appBar: AppBar(
          title: Text(widget.title),
          actions: <Widget>[
            IconButton(
              icon: Icon(Icons.lock),
              onPressed: () {
                Navigator.pushNamed(context, '/login');
              },
            ),
          ],
        ),
        body: Column(
          children: <Widget>[
            Text(_result.contains("https") || _result.contains("http") ? _result : "Invalid URL"),
          ],
        ),
        floatingActionButton: FloatingActionButton.extended(
          icon: Icon(Icons.camera_alt),
          label: Text("Scan"),
          onPressed: () => _scanQR(),
        ),
        floatingActionButtonLocation: FloatingActionButtonLocation.centerFloat,
      ),
    );
  }

  checkingValue() {
    if (_result != null || _result != "") {
      if (_result.contains("https") || _result.contains("http")) {
        return _launchURL(_result);
      } else {
        Toast.show("Invalide URL", context,
            duration: Toast.LENGTH_LONG, gravity: Toast.BOTTOM);
      }
    } else {
      return null;
    }
  }

  _launchURL(String urlQRCode) async {
    String url = urlQRCode;
    if (await canLaunch(url)) {
      await launch(url);
    } else {
      throw 'Could not launch $url';
    }
  }

}
```

那么，我把`checkingValue()`扫描二维码后运行它的功能放在哪里？

递`qrResult`给`checkingValue`方法

```dart
Future _scanQR() async {
    try {
      String qrResult = await BarcodeScanner.scan();
      checkingValue(qrResult);
     //....
    }
```

`checkingValue` 方法

```dart
checkingValue(String url) {
    //...
}
```

或拨打`checkingValue()`后

```dart
setState(() {_result = qrResult;});
checkingValue();
//...
```

# flutter - URL出现在网站名称的位置

从Android Studio运行时:
[![enter image description here](https://luckly007.oss-cn-beijing.aliyuncs.com/image/ccUfC.jpg)](https://i.stack.imgur.com/ccUfC.jpg)
托管后:
[![enter image description here](https://luckly007.oss-cn-beijing.aliyuncs.com/image/rmE8S.jpg)](https://i.stack.imgur.com/rmE8S.jpg)
我希望我的网站名称显示在该位置。
因此，如果有人可以帮助我，谢谢!

**最佳答案**

您需要设置MaterialApp的标题。像这样:

```
  MaterialApp(
      title: 'Welcome - Ajith'
```

# flutter - 如何注意用户何时松开手指

我有一个listView.Builder，想在用户在屏幕上松开手指时根据scrollController的位置进行某些计算吗？

计算部分很容易抖动，但是如何注意到用户从滚动中释放手指来执行某些操作？

**最佳答案**

使用NotificationListener窗口小部件。 [Here](https://www.youtube.com/watch?v=cAnFbFoGM50)是它的简短片段。

您可能想要的代码如下所示:

```dart
@override
Widget build(BuildContext context) {    
    return NotificationListener<ScrollNotification>(
        onNotification: (notification) {
            if (notification is ScrollStartNotification) {
                debugPrint('Started');
            }
            if (notification is ScrollUpdateNotification) {
                debugPrint('Updated');
            }
            if (notification is ScrollEndNotification) {
                debugPrint('Ended');
            }
            return false;
        },
        child: YourListView(),
    );
}   
```



# flutter - 如何创建联系人列表

这是我要从中将信息导入到ContactsPage的contacts_data页面:

```dart
class Contact {
  final String fullName;
   final String email;

   const Contact({this.fullName, this.email});
}


const kContacts = const <Contact>[
  const Contact(
  fullName: 'Romain Hoogmoed',
  email:'romain.hoogmoed@example.com'
  ),
   const Contact(
      fullName: 'Emilie Olsen',
      email:'emilie.olsen@example.com'
  ),
  const Contact(
       fullName: 'Téo Lefevre',
      email:'téo.lefevre@example.com'
   ),
  const Contact(
      fullName: 'Nicole Cruz',
      email:'nicole.cruz@example.com'
  ),
  const Contact(
      fullName: 'Ramna Peixoto',
      email:'ramna.peixoto@example.com'
  ),
  const Contact(
      fullName: 'Jose Ortiz',
      email:'jose.ortiz@example.com'
  ),
  const Contact(
     fullName: 'Alma Christensen',
     email:'alma.christensen@example.com'
  ),
  const Contact(
     fullName: 'Sergio Hill',
     email:'sergio.hill@example.com'
  ),
  const Contact(
     fullName: 'Malo Gonzalez',
     email:'malo.gonzalez@example.com'
 ),
 const Contact(
     fullName: 'Miguel Owens',
     email:'miguel.owens@example.com'
 ),
  const Contact(
     fullName: 'Lilou Dumont',
     email:'lilou.dumont@example.com'
 ),
  const Contact(
     fullName: 'Ashley Stewart',
     email:'ashley.stewart@example.com'
  ),
  const Contact(
       fullName: 'Roman Zhang',
      email:'roman.zhang@example.com'
 ),
  const Contact(
     fullName: 'Ryan Roberts',
     email:'ryan.roberts@example.com'
 ),
 const Contact(
     fullName: 'Sadie Thomas',
     email:'sadie.thomas@example.com'
 ),
 const Contact(
     fullName: 'Belen Serrano',
     email:'belen.serrano@example.com '
  )];


```








我修复了代码，有些小部件不可用。

```dart
    class ContactsPage extends StatelessWidget {
      @override
      Widget build(BuildContext context) {
        return new Scaffold(
            appBar: new AppBar(
              title: new Text("Contacts"),
            ),
            body: new ContactList(kContacts));
      }
    }

    const kContacts = const <Contact>[
      const Contact(
          fullName: 'Romain Hoogmoed', email: 'romain.hoogmoed@example.com'),
      const Contact(fullName: 'Emilie Olsen', email: 'emilie.olsen@example.com')
    ];

    class ContactList extends StatelessWidget {
      final List<Contact> _contacts;

      ContactList(this._contacts);

      @override
      Widget build(BuildContext context) {
        return new ListView.builder(
          padding: new EdgeInsets.symmetric(vertical: 8.0),
          itemBuilder: (context, index) {
            return new _ContactListItem(_contacts[index]);
          },
          itemCount: _contacts.length,
        );
      }
    }

    class _ContactListItem extends ListTile {
      _ContactListItem(Contact contact)
          : super(
                title: new Text(contact.fullName),
                subtitle: new Text(contact.email),
                leading: new CircleAvatar(child: new Text(contact.fullName[0])));
    }

    class Contact {
      final String fullName;
      final String email;

      const Contact({this.fullName, this.email});
    }
```

请让我知道这对你有没有用。

# 带有粘性标题的每个部分都应该是带有 SliverPinnedHeader 和 SliverList 的多条。然后将 pushPinnedChildren 设置为 true 应该会提供您正在寻找的粘性标题效果。





# flutter - 如何将Gridview放在Column小部件中的Card小部件附近

正在尝试为我的应用创建用户界面。我需要这样设计:
[![enter image description here](https://i.stack.imgur.com/zoN7s.jpg)](https://i.stack.imgur.com/zoN7s.jpg)
但是我最近的用户界面如下所示:
[![enter image description here](https://i.stack.imgur.com/FcKLg.jpg)](https://i.stack.imgur.com/FcKLg.jpg)
在第二个屏幕截图中，我向下滚动是因为我的gridview小部件(具有4个卡式小部件)没有更接近其他小部件。其他小部件是:标题栏小部件，具有图形和子图小部件的卡片小部件。我将标题栏和图形卡小部件放在堆栈中以在上方显示它们。我将这两个组合的小部件与gridview小部件一起放在一列中。我用SingleChildScrollView小部件包装此列小部件，因为我希望页面可滚动。 **因此，我的问题是:如何像第一个图像那样将gridview小部件放置得更靠近卡片小部件？**
这是我用于此UI的代码:

```dart
import 'package:flutter/material.dart';
import 'package:flutter_circular_chart/flutter_circular_chart.dart';
import 'package:intl/intl.dart';
import 'baskana_rapor_icon_icons.dart';

// ignore: must_be_immutable
class MainPage extends StatelessWidget {
  List<Widget> widgets = new List();
  List<CircularSegmentEntry> dataList = _loadData();
  int _totalCount;
  Widget s1, s2, total;
  final formatter = new NumberFormat("#,##");

  @override
  Widget build(BuildContext context) {
    widgets.add(_buildBody(context));

    return Scaffold(body: _buildBody(context));
  }

  Widget _buildBody(BuildContext context) {
    return LayoutBuilder(
      builder: (BuildContext context, BoxConstraints viewportConstraints) {
        return SingleChildScrollView(
          child: Column(
            crossAxisAlignment: CrossAxisAlignment.center,
            //mainAxisAlignment: MainAxisAlignment.spaceBetween,
            children: [
              Stack(
                children: <Widget>[
                  Container(
                    height: MediaQuery.of(context).size.height,
                    width: MediaQuery.of(context).size.width,
                  ),
                  _buildTitleBar(context),
                  Positioned(bottom: 65, left: 35, child: _buildCard(context)),
                ],
              ),
              _buildGridButtons(context),
            ],
          ),
        );
      },
    );
  }

  Widget _buildGridButtons(BuildContext context) {
    int itemWidth = 80;
    int itemHeight = 40;
    return SafeArea(
      child: Column(
        children: [
          GridView.count(
            crossAxisCount: 2,
            childAspectRatio: (itemWidth / itemHeight),
            shrinkWrap: true,
            primary: true,
            children: [
              GestureDetector(
                onTap: () {
                  Navigator.pushNamed(context, '/MahalleRapor');
                },
                child: Card(
                  color: Colors.white,
                  shape: RoundedRectangleBorder(
                    borderRadius: BorderRadius.all(
                      Radius.circular(25),
                    ),
                  ),
                  child: Stack(
                    alignment: Alignment.center,
                    children: [
                      Column(
                        mainAxisSize: MainAxisSize.min,
                        crossAxisAlignment: CrossAxisAlignment.center,
                        mainAxisAlignment: MainAxisAlignment.spaceAround,
                        children: [
                          Icon(
                            BaskanaRaporIcon.mahalle_raporu,
                            color: Colors.redAccent,
                            size: 30,
                          ),
                          Text(
                            'Mahalle Raporu',
                            style: TextStyle(
                              color: Colors.black87,
                              fontSize: 20,
                              fontWeight: FontWeight.bold,
                            ),
                          ),
                          SizedBox(
                            height: 5,
                          )
                        ],
                      ),
                      Positioned(
                        bottom: 0,
                        right: 1,
                        left: 1,
                        child: Divider(
                          color: Colors.redAccent,
                          endIndent: 45,
                          indent: 50,
                          thickness: 3,
                        ),
                      ),
                    ],
                  ),
                ),
              ),
              GestureDetector(
                onTap: () {
                  Navigator.pushNamed(context, '/BirimRapor');
                },
                child: Card(
                  color: Colors.white,
                  shape: RoundedRectangleBorder(
                    borderRadius: BorderRadius.all(
                      Radius.circular(25),
                    ),
                  ),
                  child: Stack(
                    alignment: Alignment.center,
                    children: [
                      Column(
                        mainAxisSize: MainAxisSize.min,
                        crossAxisAlignment: CrossAxisAlignment.center,
                        mainAxisAlignment: MainAxisAlignment.spaceAround,
                        children: [
                          Icon(
                            BaskanaRaporIcon.birim_raporu,
                            color: Colors.green[300],
                            size: 30,
                          ),
                          Text(
                            'Birim Raporu',
                            style: TextStyle(
                              color: Colors.black87,
                              fontSize: 20,
                              fontWeight: FontWeight.bold,
                            ),
                          ),
                          SizedBox(
                            height: 5,
                          )
                        ],
                      ),
                      Positioned(
                        bottom: 0,
                        right: 1,
                        left: 1,
                        child: Divider(
                          color: Colors.green[300],
                          endIndent: 45,
                          indent: 50,
                          thickness: 3,
                        ),
                      ),
                    ],
                  ),
                ),
              ),
              GestureDetector(
                onTap: () {
                  Navigator.pushNamed(context, '/GelirGider');
                },
                child: Card(
                  color: Colors.white,
                  shape: RoundedRectangleBorder(
                    borderRadius: BorderRadius.all(
                      Radius.circular(25),
                    ),
                  ),
                  child: Stack(
                    alignment: Alignment.center,
                    children: [
                      Column(
                        mainAxisSize: MainAxisSize.min,
                        crossAxisAlignment: CrossAxisAlignment.center,
                        mainAxisAlignment: MainAxisAlignment.spaceAround,
                        children: [
                          Icon(
                            BaskanaRaporIcon.gelir_gider,
                            color: Colors.yellow[700],
                            size: 30,
                          ),
                          Text(
                            'Gelir / Gider',
                            style: TextStyle(
                              color: Colors.black87,
                              fontSize: 20,
                              fontWeight: FontWeight.bold,
                            ),
                          ),
                          SizedBox(
                            height: 5,
                          )
                        ],
                      ),
                      Positioned(
                        bottom: 0,
                        right: 1,
                        left: 1,
                        child: Divider(
                          color: Colors.yellow[700],
                          endIndent: 45,
                          indent: 50,
                          thickness: 3,
                        ),
                      ),
                    ],
                  ),
                ),
              ),
              GestureDetector(
                onTap: () {
                  Navigator.pushNamed(context, '/BaskanaMesaj');
                },
                child: Card(
                  color: Colors.white,
                  shape: RoundedRectangleBorder(
                    borderRadius: BorderRadius.all(
                      Radius.circular(25),
                    ),
                  ),
                  child: Stack(
                    alignment: Alignment.center,
                    children: [
                      Column(
                        mainAxisSize: MainAxisSize.min,
                        crossAxisAlignment: CrossAxisAlignment.center,
                        mainAxisAlignment: MainAxisAlignment.spaceAround,
                        children: [
                          Icon(
                            BaskanaRaporIcon.baskana_msg,
                            color: Colors.blueAccent,
                            size: 30,
                          ),
                          Text(
                            'Başkana Mesaj',
                            style: TextStyle(
                              color: Colors.black87,
                              fontSize: 20,
                              fontWeight: FontWeight.bold,
                            ),
                          ),
                          SizedBox(
                            height: 5,
                          )
                        ],
                      ),
                      Positioned(
                        bottom: 0,
                        right: 1,
                        left: 1,
                        child: Divider(
                          color: Colors.blueAccent,
                          endIndent: 45,
                          indent: 50,
                          thickness: 3,
                        ),
                      ),
                    ],
                  ),
                ),
              ),
            ],
          )
        ],
      ),
    );
  }

  // ignore: todo
  //TODO: Better implementation of UI
  final double buttonHeight = 50;

  Widget _buildCard(BuildContext context) {
    return Center(
      child: Container(
        height: 500 + buttonHeight,
        child: Stack(
          alignment: Alignment.center,
          overflow: Overflow.visible,
          children: [
            Card(
              shape: RoundedRectangleBorder(
                borderRadius: BorderRadius.circular(15),
                side: BorderSide(color: Colors.blueGrey, width: 0.5),
              ),
              child: Container(
                height: MediaQuery.of(context).size.height * .65,
                width: MediaQuery.of(context).size.width * .80,
                child: Column(
                  crossAxisAlignment: CrossAxisAlignment.center,
                  mainAxisSize: MainAxisSize.max,
                  children: <Widget>[
                    Padding(
                      padding: const EdgeInsets.all(8.0),
                      child: Text(
                        "Genel Durum",
                        style: TextStyle(
                          fontSize: 20,
                          color: Colors.black,
                          letterSpacing: 0.3,
                        ),
                      ),
                    ),
                    Divider(
                      color: Colors.grey,
                      thickness: 0.3,
                      endIndent: 10,
                      indent: 10,
                    ),
                    _buildChart(dataList),
                    SizedBox(
                      height: 20,
                    ),
                    _buildSubGraph(),
                    SizedBox(
                      height: 40,
                    ),
                  ],
                ),
              ),
            ),
            Positioned(
              //top: -buttonHeight /2,
              bottom: 27,
              child: _buildDetailsButton(context),
            ),
          ],
        ),
      ),
    );
  }

  Widget _buildDetailsButton(BuildContext context) {
    return ButtonTheme(
      height: 50,
      minWidth: 100,
      child: RaisedButton(
        onPressed: () {
          Navigator.pushNamed(context, '/DetailPage');
        },
        color: Colors.white,
        shape: RoundedRectangleBorder(
          borderRadius: BorderRadius.circular(30),
        ),
        child: Text("Detayları Gör"),
      ),
    );
  }

  Row _buildSubGraph() {
    String s1 = formatter.format((dataList[1].value / _totalCount) * 100);
    String s0 = formatter.format((dataList[0].value / _totalCount) * 100);

    return Row(
      mainAxisAlignment: MainAxisAlignment.spaceAround,
      children: [
        Column(
          //İşlemde
          children: [
            Text(
              dataList[1].rankKey,
              style: TextStyle(
                fontSize: 18,
                color: Colors.black87,
              ),
            ),
            Container(
              decoration: BoxDecoration(
                color: dataList[1].color,
                borderRadius: BorderRadius.only(
                  topRight: Radius.circular(5),
                  bottomRight: Radius.circular(5),
                  bottomLeft: Radius.circular(5),
                ),
              ),
              height: 50,
              width: 150,
              child: Row(
                mainAxisAlignment: MainAxisAlignment.spaceEvenly,
                crossAxisAlignment: CrossAxisAlignment.center,
                children: [
                  Text(
                    '${dataList[1].value.toInt()}',
                    style: TextStyle(
                      color: Colors.white,
                      fontSize: 20,
                    ),
                  ),
                  SizedBox(
                    width: 50,
                  ),
                  Flexible(
                    child: Text(
                      '%' + s1,
                      style: TextStyle(
                        color: Colors.white,
                        fontSize: 16,
                        fontWeight: FontWeight.w300,
                      ),
                    ),
                  ),
                ],
              ),
            ),
          ],
        ),
        Column(
          //Sonuçlanan
          mainAxisAlignment: MainAxisAlignment.spaceEvenly,
          children: [
            Text(
              dataList[0].rankKey,
              style: TextStyle(
                fontSize: 18,
                color: Colors.black87,
              ),
            ),
            Container(
              height: 50,
              width: 150,
              decoration: BoxDecoration(
                color: dataList[0].color,
                borderRadius: BorderRadius.only(
                  topRight: Radius.circular(5),
                  bottomRight: Radius.circular(5),
                  bottomLeft: Radius.circular(5),
                ),
              ),
              child: Row(
                mainAxisAlignment: MainAxisAlignment.spaceEvenly,
                crossAxisAlignment: CrossAxisAlignment.center,
                children: [
                  Text(
                    '${dataList[0].value.toInt()}',
                    style: TextStyle(
                      color: Colors.white,
                      fontSize: 20,
                    ),
                  ),
                  SizedBox(
                    width: 50,
                  ),
                  Flexible(
                    child: Text(
                      '%' + s0,
                      style: TextStyle(
                        color: Colors.white,
                        fontSize: 16,
                        fontWeight: FontWeight.w300,
                      ),
                    ),
                  ),
                ],
              ),
            ),
          ],
        )
      ],
    );
  }

  //Map<String, double> dataMap, List<Color> colorList,BuildContext context
  Widget _buildChart(List<CircularSegmentEntry> dataList) {
    _totalCount = _findTotalCount(dataList);
    s1 = _createText("TOPLAMDA", 20, Colors.grey[600], false);
    s1 = _createText("TALEP", 20, Colors.grey[600], false);
    total = _createText('$_totalCount', 24, Colors.black87, true);
    return Container(
      child: AnimatedCircularChart(
        size: Size(500, 250),
        initialChartData: <CircularStackEntry>[
          CircularStackEntry(
            <CircularSegmentEntry>[
              dataList[0],
              dataList[1],
            ],
          ),
        ],
        chartType: CircularChartType.Radial,
        startAngle: -90,
        holeRadius: 25,
        holeLabel:
            "TOPLAMDA \n\t\t\t\t\t\t\t $_totalCount \n\t\t\t TALEP", // $s1 \n\t\t\t\t\t\t\t $total \n\t\t\t $s2
      ),
    );
  }

  Widget _buildTitleBar(BuildContext context) {
    return Align(
      alignment: Alignment.topCenter,
      child: Column(
        children: <Widget>[
          Container(
            height: MediaQuery.of(context).size.height * .247,
            decoration: BoxDecoration(
              color: Colors.blue,
              shape: BoxShape.rectangle,
              borderRadius: BorderRadius.vertical(
                bottom: Radius.circular(25),
              ),
            ),
            child: Row(
              mainAxisAlignment: MainAxisAlignment.center,
              children: <Widget>[
                Icon(
                  Icons.assessment,
                  color: Colors.white,
                ),
                Text(
                  "Genel Durum",
                  style: TextStyle(
                    color: Colors.white,
                    fontSize: 28,
                    fontWeight: FontWeight.bold,
                    letterSpacing: 0.5,
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

Widget _createText(String msg, double size, Color color, bool isBold) {
  return Text(
    msg,
    style: TextStyle(
      color: color,
      fontSize: size,
      fontWeight: isBold ? FontWeight.bold : FontWeight.normal,
    ),
  );
}

int _findTotalCount(List<CircularSegmentEntry> dataList) {
  int result = 0;
  for (int i = 0; i < dataList.length; i++) {
    result += (dataList[i].value).toInt();
  }
  return result;
}

List<CircularSegmentEntry> _loadData() {
  List<CircularSegmentEntry> dataList = [];
  CircularSegmentEntry chartData1 =
      new CircularSegmentEntry(150, Colors.greenAccent, rankKey: 'Sonuçlanan');
  CircularSegmentEntry chartData2 =
      new CircularSegmentEntry(150, Colors.blue, rankKey: 'İşlemde');
  dataList.add(chartData1);
  dataList.add(chartData2);
  return dataList;
}
```

**额外的问题:如果我可以做些更有效的设计，您能告诉我吗？谢谢!**



**最佳答案**

也许是由于堆栈中的这段代码而发生的。此容器占用您的全屏尺寸。这就是为什么您的GridView项目会在屏幕大小之后立即生成。尝试删除此容器或减小其高度。

```dart
Container(
     height: MediaQuery.of(context).size.height,
     width: MediaQuery.of(context).size.width,
   ),
```

**更新:**
你可以试试看我没有尝试过，但这也许会有所帮助

```dart
Stack(
    children: <Widget>[
      Align(
        alignment: Alignment.topCenter,
        child: _buildTitleBar(context),
      ),
      Align(
        alignment: Alignment.topCenter,
        child: Container(
          margin: EdgeInsets.only(top: 100),
          child: _buildCard(context),
        ),
      )
    ],
  )
```

