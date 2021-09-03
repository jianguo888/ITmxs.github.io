---
title: "Flutter与android原生通讯"
date: 2021-08-23T11:10:39+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter"]
categories: ["Flutter"]
---



# android - FlutterActivity MethodChannel和FlutterView

因此，大约4个月前，我编写了Flutter应用程序。现在，我想做一个小小的更改，但是我不能再编译应用程序了，因为GeneratedPluginRegistrant.registerWith(this)不再起作用了，我没有更改Kotlin代码，只更改了Dart代码。

“GeneratedPluginRegistrant.registerWith( **this** )”中的“**此**”向我显示此错误:

```
Type mismatch.    
Required: FlutterEngine!    
Found: MainActivity
```


MainActivity类:

```
import android.os.Bundle
import io.flutter.app.FlutterActivity
import io.flutter.plugin.common.MethodChannel
import io.flutter.plugins.GeneratedPluginRegistrant
import io.flutter.view.FlutterMain

class MainActivity : FlutterActivity() {

  override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)

    GeneratedPluginRegistrant.registerWith(this) // here is the error: Type mismatch. Required: FlutterEngine! Found: MainActivity

    MethodChannel(flutterView, CHANNEL).setMethodCallHandler { call, result ->
      if (call.method == "helloFromNativeCode") {
        val greetings = helloFromNativeCode()
        result.success(greetings)
      }
    }
  }

  private fun helloFromNativeCode(): String {
    return "Hello from Native Android Code"
  }

  companion object {
    private const val CHANNEL = "flutter.native/helper"
  }
}
```


如果使用:

```
import io.flutter.embedding.android.FlutterActivity
```


代替

```
import io.flutter.app.FlutterActivity
```


我可以用

```
override fun configureFlutterEngine(flutterEngine: FlutterEngine) {
        GeneratedPluginRegistrant.registerWith(flutterEngine);
    }
```


但遇到以下问题:

```
MethodChannel(flutterView, CHANNEL).setMethodCallHandler { call, result ->
      if (call.method == "helloFromNativeCode") {
        val greetings = helloFromNativeCode()
        result.success(greetings)
      }
    }
```


因为我在 **flutterView** 上遇到错误:

```
Unresolved reference: flutterView
```


代码如下所示:

```
import android.os.Bundle
import io.flutter.embedding.android.FlutterActivity
import io.flutter.embedding.engine.FlutterEngine
import io.flutter.plugin.common.MethodChannel
import io.flutter.plugins.GeneratedPluginRegistrant
import io.flutter.view.FlutterMain

class MainActivity : FlutterActivity() {
    override fun configureFlutterEngine(flutterEngine: FlutterEngine) {
        GeneratedPluginRegistrant.registerWith(flutterEngine);
    }

  override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)

    MethodChannel(flutterView, CHANNEL).setMethodCallHandler { call, result -> // here is the error
      if (call.method == "helloFromNativeCode") {
        val greetings = helloFromNativeCode()
        result.success(greetings)
      }
    }
  }

  private fun helloFromNativeCode(): String {
    return "Hello from Native Android Code"
  }

  companion object {
    private const val CHANNEL = "flutter.native/helper"
  }
}
```


我希望有一个人可以帮助我。



**最佳答案**

代替flutterView，请使用flutterEngine.getDartExecutor()。

关于android - FlutterActivity MethodChannel和FlutterView，我们在Stack Overflow上找到一个类似的问题： https://stackoverflow.com/questions/59323505/





```dart
import 'package:flutter/material.dart';
import 'package:flutter/services.dart';
import 'package:flutter_package_demo/theme/themes.dart';

class UserPage extends StatefulWidget {
  const UserPage({Key key}) : super(key: key);

  @override
  _UserPageState createState() => _UserPageState();
}

const String CHANNEL =
    "www.luckly.work"; //这儿要与        MethodChannel(flutterView, CHANNEL)中CHANNEL名称一致
const String invokeMethod =
    "breeze"; //这儿要与       call.method == invokeMethod中invokeMethod名称一致
var channel = const MethodChannel(CHANNEL);

class _UserPageState extends State<UserPage> {
  String _result = "";

  @override
  Widget build(BuildContext context) {
    final theme = Theme.of(context);
    return Scaffold(
      appBar: AppBar(
        title: Text(
          "个人",
          style: TextStyle(
            color: theme.accentColor,
          ),
        ),
      ),
      body: Container(
        child: Column(
          children: [
            Text(
              "个人",
              style: TextStyle(
                color: theme.accentColor,
              ),
            ),
            FlatButton(
              onPressed: () {
                setState(() {});
                currentTheme.toggleTheme();
              },
              child: Text(
                "多主题",
                style: TextStyle(
                  color: theme.accentColor,
                ),
              ),
            ),
            Center(
              child: RaisedButton(
                onPressed: () async {
                  var result = await channel.invokeMethod(invokeMethod);
                  setState(() {
                    _result = result;
                  });

                  print(result);
                },
                child: new Text(_result),
              ),
            )
          ],
        ),
      ),
    );
  }
}

```



```kotlin
package com.example.flutter_package_demo

//import io.flutter.app.FlutterActivity
import android.os.Bundle
import io.flutter.app.FlutterActivity
import io.flutter.plugin.common.MethodChannel
import io.flutter.plugins.GeneratedPluginRegistrant

//import io.flutter.embedding.android.FlutterActivity

//import io.flutter.view.FlutterMain

class MainActivity : FlutterActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

//        GeneratedPluginRegistrant.registerWith(this) // here is the error: Type mismatch. Required: FlutterEngine! Found: MainActivity

        MethodChannel(flutterView, CHANNEL).setMethodCallHandler { call, result ->
            if (call.method == InvokeMethod) {
                val greetings = successNativeCode()
                result.success(greetings)
            }
        }
    }

    private fun successNativeCode(): String {
        return "我是android原生跑过来的数据"
    }

    companion object {
        private const val CHANNEL = "www.luckly.work"
        private const val InvokeMethod = "breeze"
    }
}
```

