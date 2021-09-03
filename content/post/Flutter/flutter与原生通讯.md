---
title: "Flutter与原生通讯"
date: 2021-08-14T16:39:47+08:00
draft: false
tags: ["Flutter"]
categories: ["Flutter"]
---







今天和大家聊聊flutter与原生通讯的那些事儿

我主要负责android端

所以我会以Android端为例来进行刨析、

Flutter 混合开发系列 包含如下：

- 嵌入原生View-Android
- 嵌入原生View-iOS
- **与原生通信-MethodChannel**
- 与原生通信-BasicMessageChannel
- 与原生通信-EventChannel
- 添加 Flutter 到 Android Activity
- 添加 Flutter 到 Android Fragment
- 添加 Flutter 到 iOS




Flutter与原生之间的通信依赖灵活的消息传递方式：

- 应用的Flutter部分通过平台通道（platform channel）将消息发送到其应用程序的所在的宿主（iOS或Android）应用（原生应用）。
- 宿主监听平台通道，并接收该消息。然后它会调用该平台的API，并将响应发送回客户端，即应用程序的Flutter部分。

> 1. MethodChannel  // Flutter与原生方法相互调用，用于方法掉用
> 2. BasicMessageChannel // Flutter与原生相互发送消息，用于数据传递
> 3. EventChannel // 原生发送消息，Flutter接收，用于数据流通信

![](https://luckly007.oss-cn-beijing.aliyuncs.com/image/56ElhnJSyWvqpao.png)



#### 可以传递的数据结构

| Dart                       | Android              | iOS                                            |
| -------------------------- | -------------------- | ---------------------------------------------- |
| null                       | null                 | nil(NSNull when nested)                        |
| bool                       | Java.lang.Boolean    | NSNumber numberWithBool:                       |
| int                        | Java.lang.Integer    | NSNumber numberWithInt:                        |
| int, if 32 bits not enough | Java.lang.Long       | NSNumber numberWithLong:                       |
| int, if 64 bits not enough | Java.lang.BigInteger | FlutterStandardBigInteger                      |
| double                     | Java.lang.Double     | NSNumber numberWithDouble                      |
| String                     | java.lang.String     | NSString                                       |
| Unit8List                  | byte[]               | FlutterStandardTypedData typedDataWithBytes:   |
| Int32List                  | int[]                | FlutterStandardTypedData typedDataWithInt32:   |
| Int64List                  | long[]               | FlutterStandardTypedData typedDataWithInt64:   |
| Float64List                | double[]             | FlutterStandardTypedData typedDataWithFloat64: |
| List                       | Java.util.ArrayList  | NSArray                                        |
| Map                        | Java.util.HashMap    | NSDictionary                                   |



Flutter 与 Native 端通信是异步的。

#### 通信与平台线程

Native 端主动发送数据给 Flutter时，Native 端代码需要在主线程中执行，Android 端从子线程跳转到主线程方式：

Kotlin 代码：

```kotlin
Handler(Looper.getMainLooper()).post {
  
}
 
```

Java 代码：

```java
new Handler(Looper.getMainLooper()).post(new Runnable() {
  @Override
  public void run() {
    
  }
});
 
```

如果可以获取到当前 Activity，也可以使用如下方式：

```kotlin
activity.runOnUiThread {
   
}
 
```

iOS 端从子线程跳转到主线程方式：

Objective-C 代码：

```objective-c
dispatch_async(dispatch_get_main_queue(), ^{
  
});
 
```

Swift 代码：

```swift
DispatchQueue.main.async {
  
}
```





#### MethodChannel（互相调用方法）

**Android调用Flutter方法：**

Android:

1. 初始化MethodChannel

```dart
static const batteryChannel = const MethodChannel('battery');
```



```
//初始化，传递1. flutterView（MainActivity中getFlutter获取），2. name常量，Flutter中使用同名常量
MethodChannel methodChannel = new MethodChannel(flutterView, “battery”);

```





battery是 MethodChannel 的名称，原生端要与之对应。

发送消息：

```dart
   var result = await batteryChannel.invokeMethod('getBatteryLevel');
```



```dart
var result = await channel.invokeMethod('sendData',{'name': 'luckly', 'age': 18})

```

- 第一个参数表示**method**，方法名称，原生端会解析此参数。
- 第二个参数表示参数，类型任意，多个参数通常使用**Map**。
- 返回 **Future**，原生端返回的数据。




1. 调用Flutter方法

```java
private void invokeFlutterMethod() {
  if (this.mMethodChannel != null) {
    this.mMethodChannel.invokeMethod("flutterMethod", "native参数", new MethodChannel.Result() {
      @Override
      public void success(Object o) {
        Toast.makeText(mContext, o.toString(), Toast.LENGTH_LONG).show();
      }
      @Override
      public void error(String s, String s1, Object o) {
      }
      @Override
      public void notImplemented() {
      }
    });
  }
}
 
```

通过MethodChannel调用invokeMethod("方法名","传递参数",[Flutter返回参数回调，非必须]);

Flutter:

1. 初始化MethodChannel

```
static const methodChannel = const MethodChannel('testflutter');
 
```

1. 添加处理方法到MethodChannel

```
methodChannel.setMethodCallHandler(_addNativeMethod);
 
```

1. 处理android调用的方法，根据方法名

```dart
Future<dynamic> _addNativeMethod(MethodCall methodCall) async {
  switch (methodCall.method) {
    case 'flutterMethod':
      setState(() {
        _calledFromNative = 'flutter method called from native with param ' + methodCall.arguments;
      });
      return 'flutter method called from native with param ' + methodCall.arguments;
      break;
  }
}
//其中，return返回的数据在Android的回调中接收
 
```

**Flutter调用Android方法：**

Android：

1. 初始化MethodChannel，并添加自定义plugin

```
MethodChannel methodChannel = new MethodChannel(flutterView, METHOD_CHANNEL);
methodChannel.setMethodCallHandler(plugin);
 
```

1. 自定义的plugin实现MethodChannel.MethodCallHandler接口的onMethodCall方法

```
@Override
public void onMethodCall(MethodCall methodCall, MethodChannel.Result result) {
  // Flutter调用Native的方法
  if (methodCall.method.equals("getBatteryLevel")) {
    int batteryLevel = getBatteryLevel();
    if (batteryLevel != -1) {
      result.success(batteryLevel);
    } else {
      result.error("UNAVALIABLE", "battery level unavaliable", null);
    }
  } else {
    result.notImplemented();
  }
}
//在onMethodCall中监听Flutter调用什么名字的方法（此处getBatterLevel），通过result返回方法的执行结果。
 
```

Flutter：

1. 初始化MethodChannel

```
static const methodChannel = const MethodChannel('testflutter');
 
```

1. 调用Android的方法，接收返回数据

```
//方法通道的方法是异步的
Future<Null> _getBatteryLevel() async {
  String batteryLevel;
  try {
    final int result = await methodChannel.invokeMethod('getBatteryLevel');
    batteryLevel = 'Battery level $result .';
  } on PlatformException catch (e) {
    batteryLevel = 'Battery level unknown ${e.message}';
  }  
  setState(() {
    _batteryLevel = batteryLevel;
  });
}
 
```

#### BasicMessageChannel（互相发送消息）

**Android给Flutter发消息：**

Android：

1. 初始化BasicMethodChannel

```
BasicMessageChannel messageChannel = new BasicMessageChannel<>(flutterView, "messageChannel", StandardMessageCodec.INSTANCE);
 
```

1. 调用发送消息的方法

```
private void sendMessageToFlutter() {
  if (this.mBasicMessageChannel != null) {
    this.mBasicMessageChannel.send("Message From Native");
  }
}
 
```

Flutter：

1. 初始化BasicMessageChannel

```
static const basicMessageChannel = BasicMessageChannel('messageChannel', StandardMessageCodec());
 
```

1. 添加接收信息处理方法

```
void _listenMessageFromNative() {
  basicMessageChannel.setMessageHandler(_receiveMessageFromNative);
}
 
```

1. 处理接收的数据

```
//Flutter接收Native发来的消息
Future<dynamic> _receiveMessageFromNative(Object result) async {
  setState(() {
    _messageFromNative = result.toString();
  });
}
 
```

**Flutter给Android发消息：**

Android：

1. 初始化BasicMessageChannel并添加plugin给handler

```
BasicMessageChannel messageChannel = new BasicMessageChannel<>(flutterView, "messageChannel", StandardMessageCodec.INSTANCE);
messageChannel.setMessageHandler(plugin);
 
```

1. plugin实现BasicMessageChannel.MessageHandler接口的onMessage方法，处理接收到的信息

```
@Override
public void onMessage(Object o, BasicMessageChannel.Reply reply) {
  Toast.makeText(mContext, o.toString(), Toast.LENGTH_LONG).show();
  reply.reply(o.toString()+" back from native");
}
//reply返回数据给Flutter
 
```

Flutter：

1. 初始化BasicMessageChannel

```
static const basicMessageChannel = BasicMessageChannel('messageChannel', StandardMessageCodec());
 
```

1. 发送消息给Android并接收返回数据

```
Future<dynamic> _sendMessageToNative(String message) async {
  String reply = await basicMessageChannel.send(message);
  print(reply);
  setState(() {
    _replayFromNative = reply;
  });
  return reply;
}
 
```

#### EventChannel（原生发送消息，Flutter接收）

Android：

1. 初始化EventChannel并添加plugin给handler

```
EventChannel eventChannel = new EventChannel(flutterView, EVENT_CHANNEL);
eventChannel.setStreamHandler(plugin);
 
```

1. plugin实现EventChannel.StreamHandler接口及onListen、onCancel方法
2. 在onListen中通过EventChannel.EventShink的实例发消息给Flutter

```
@Override
public void onListen(Object o, EventChannel.EventSink eventSink) {
  BroadcastReceiver chargingBroadcastReceiver = createChargingBroadcaseReceiver(eventSink);
  mContext.registerReceiver(chargingBroadcastReceiver,new IntentFilter(Intent.ACTION_BATTERY_CHANGED));
}
@Override
public void onCancel(Object o) {
}
private BroadcastReceiver createChargingBroadcaseReceiver(EventChannel.EventSink eventSink) {
  return new BroadcastReceiver() {
    @Override
    public void onReceive(Context context, Intent intent) {
      int status = intent.getIntExtra(BatteryManager.EXTRA_STATUS, -1);
      if (status == BatteryManager.BATTERY_STATUS_UNKNOWN) {
        eventSink.error("UNAVALIABLE", "charging status is unavailable", null);
      } else {
        boolean isCharging = status == BatteryManager.BATTERY_STATUS_CHARGING;
        eventSink.success(isCharging ? "charging" : "disCharging");
      }
    }
  };
}

```

Flutter:

1. 初始化EventChannel

```
static const _eventChannel = const EventChannel('charging');

```

1. 添加接收数据方法

```
void listenNativeEvent() {
  _eventChannel.receiveBroadcastStream().listen(_onEvent, onError: _onError);
}
//接收返回的数据
void _onEvent(Object object) {
  String s = "Battery is ${object == 'charging' ? '' : 'dis'}Charging";
  setState(() {
    _batteryStatus = s;
  });
}

```

#### 桥接View给Flutter使用

Android：

1. 自定义View，继承自PlatformView

```
public class MyTextview implements PlatformView {
  TextView t;
  public MyTextview(Context context, MessageCodec<Object> messenger, int id, Map<String, Object> params){
    TextView textView = new TextView(context);
    //获取参数，是否有传递参数过来
    if (params.containsKey("text")){
      textView.setText(params.get("text").toString());
    }
    this.t = textView;
  }
  @Override
  public View getView() {
    return t;
  }
  
  @Override
  public void dispose() {
  }
}

```

1. 实现PlatformViewFactory

```
public class TextViewFactory extends PlatformViewFactory {
  private MessageCodec<Object> messageCodec;
  public TextViewFactory(MessageCodec<Object> createArgsCodec) {
    super(createArgsCodec);
    this.messageCodec = createArgsCodec;
  }
  
  @Override
  public PlatformView create(Context context, int i, Object o) {
    return new MyTextview(context, messageCodec, i, (Map<String, Object>) o);
  }
}

```

1. 注册View给Flutter使用

```
registrar.platformViewRegistry().registerViewFactory("TextView", new TextViewFactory(new StandardMessageCodec()));
//起名叫TextView，给Flutter用做viewType
 
```

Flutter:

1. 使用桥接的View

```
AndroidView(
  viewType: 'TextView',
  creationParams: {'text': 'TTTeeeXXXttt'},
  creationParamsCodec: new StandardMessageCodec(),
),//其中creationParams，creationParamsCodec必须同时存在或不存在
 
```



# 以下是获取电量的方法：

flutter侧：

```dart
import 'package:flutter/material.dart';
import 'package:flutter/services.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      theme: ThemeData(
        primarySwatch: Colors.red,
      ),
      home: HomePage(),
    );
  }
}

class HomePage extends StatefulWidget {
  HomePage({Key key}) : super(key: key);

  @override
  _HomePageState createState() => _HomePageState();
}

class _HomePageState extends State<HomePage> {
  String _batteryPercentage = "现在的电量";
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text("原生通讯"),
      ),
      body: Column(
        mainAxisAlignment: MainAxisAlignment.center,
        crossAxisAlignment: CrossAxisAlignment.center,
        children: [
          MaterialButton(
            onPressed: _getBatteryInformation,
            color: Colors.blue,
            child: Padding(
              padding: EdgeInsets.all(36),
              child: Text(
                "点我",
                style: TextStyle(color: Colors.red, fontSize: 50),
              ),
            ),
          ),
          Container(
            height: 20,
          ),
          Text(
            _batteryPercentage,
            style: TextStyle(color: Colors.blue, fontSize: 30),
          ),
        ],
      ),
    );
  }

  static const batteryChannel = const MethodChannel('battery');

  Future<void> _getBatteryInformation() async {
    String batteryPercentage;
    try {
      var result = await batteryChannel.invokeMethod('getBatteryLevel');
      batteryPercentage = '现在的电量是 $result%';
    } on PlatformException catch (e) {
      batteryPercentage = "Failed to get battery level: '${e.message}'.";
    }

    setState(() {
      _batteryPercentage = batteryPercentage;
    });
  }
}

```



```java
package com.example.flutterer_sliver
import android.content.Context
import android.content.ContextWrapper
import android.content.Intent
import android.content.IntentFilter
import android.os.BatteryManager
import android.os.Build
import android.os.Bundle

import io.flutter.embedding.android.FlutterActivity;
import io.flutter.plugins.GeneratedPluginRegistrant
import io.flutter.plugin.common.MethodCall
import io.flutter.plugin.common.MethodChannel.MethodCallHandler
import io.flutter.plugin.common.MethodChannel


class MainActivity: FlutterActivity() {
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
 

    MethodChannel(flutterEngine?.getDartExecutor(), "battery").setMethodCallHandler { call, result ->
      if (call.method == "getBatteryLevel") {
        val batteryLevel = getBatteryLevel()

        if (batteryLevel != -1) {
          result.success(batteryLevel)
        } else {
          result.error("Unavailable", "Battery level is not available", null)
        }
      }
    }
  }

  private fun getBatteryLevel() : Int {
    val batteryLevel: Int

    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP) {

      val batteryManager = getSystemService(Context.BATTERY_SERVICE) as BatteryManager
      batteryLevel = batteryManager.getIntProperty(BatteryManager.BATTERY_PROPERTY_CAPACITY)

    } else {

      val intent = ContextWrapper(applicationContext).registerReceiver(null, IntentFilter(
              Intent.ACTION_BATTERY_CHANGED))
      batteryLevel = intent!!.getIntExtra(BatteryManager.EXTRA_LEVEL, -1) * 100 / intent.getIntExtra(BatteryManager.EXTRA_SCALE, -1)

    }

    return batteryLevel
  }

}
```





在做通讯的时候，flutter侧的代码比较好写，但是android端的就比较奇葩了

遇到的错误也是各式各样

## 首先第一个就是MainActivity but FlutterEngine was expected



Type mismatch: inferred type is MainActivity but FlutterEngine was expected



解决办法：

参考链接  https://stackoverflow.com/questions/63349813/type-mismatch-inferred-type-is-mainactivity-but-flutterengine-was-expected

Please remove `GeneratedPluginRegistrant.registerWith(flutterEngine);` from `MainActivity.kt` and update as follows.

```haskell
import io.flutter.embedding.android.FlutterActivity

class MainActivity: FlutterActivity() {
}
```



## 第二个



解决办法



```dart
package com.codingwithboris.platformspecific

import android.content.Context
import android.content.ContextWrapper
import android.content.Intent
import android.content.IntentFilter
import android.os.BatteryManager
import android.os.Build
import android.os.Bundle

import io.flutter.app.FlutterActivity
import io.flutter.plugins.GeneratedPluginRegistrant
import io.flutter.plugin.common.MethodCall
import io.flutter.plugin.common.MethodChannel.MethodCallHandler
import io.flutter.plugin.common.MethodChannel



class MainActivity: FlutterActivity() {

  override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    GeneratedPluginRegistrant.registerWith(this) //此行注释去

    MethodChannel(flutterView, "battery").setMethodCallHandler { call, result ->
      if (call.method == "getBatteryLevel") {
        val batteryLevel = getBatteryLevel()

        if (batteryLevel != -1) {
          result.success(batteryLevel)
        } else {
          result.error("Unavailable", "Battery level is not available", null)
        }
      }
    }
  }

  private fun getBatteryLevel() : Int {
    val batteryLevel: Int

    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP) {

      val batteryManager = getSystemService(Context.BATTERY_SERVICE) as BatteryManager
      batteryLevel = batteryManager.getIntProperty(BatteryManager.BATTERY_PROPERTY_CAPACITY)

    } else {

      val intent = ContextWrapper(applicationContext).registerReceiver(null, IntentFilter(
              Intent.ACTION_BATTERY_CHANGED))
      batteryLevel = intent!!.getIntExtra(BatteryManager.EXTRA_LEVEL, -1) * 100 / intent.getIntExtra(BatteryManager.EXTRA_SCALE, -1)

    }

    return batteryLevel
  }
}
```



如果使用：

```haskell
import io.flutter.embedding.android.FlutterActivity
```

代替

```haskell
import io.flutter.app.FlutterActivity
```

我可以用

```kotlin
override fun configureFlutterEngine(flutterEngine: FlutterEngine) {
        GeneratedPluginRegistrant.registerWith(flutterEngine);
    }
```



参考链接https://stackoverflow.com/questions/59323505/flutteractivity-methodchannel-and-flutterview



## 第三个Unresolved reference: flutterView

```yaml
Unresolved reference: flutterView
```



解决办法 ：  使用 flutterEngine.getDartExecutor() 代替 flutterView。

```dart
class MainActivity: FlutterActivity() {

  override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    GeneratedPluginRegistrant.registerWith(this) //此行注释去

    MethodChannel(flutterView, "battery").setMethodCallHandler { call, result ->
      if (call.method == "getBatteryLevel") {
        val batteryLevel = getBatteryLevel()

        if (batteryLevel != -1) {
          result.success(batteryLevel)
        } else {
          result.error("Unavailable", "Battery level is not available", null)
        }
      }
    }
  }
```

参考链接：

https://stackoverflow.com/questions/59323505/flutteractivity-methodchannel-and-flutterview





## 第四个kotlin 'onCreate' overrides nothing

解决办法

上次更新中似乎有些功能发生了变化，只需`?`从`Bundle`

像这样：

```kotlin
override fun onCreate(savedInstanceState: Bundle) {
    super.onCreate(savedInstanceState)
}
```



参考链接

https://stackoverflow.com/questions/47049471/kotlin-oncreate-overrides-nothing)





致谢：https://github.com/boriszv/Programming-Addict-Code-Examples/tree/master/call_platform_specific_code/platform_specific

https://blog.csdn.net/ffa_ijj/article/details/90449561