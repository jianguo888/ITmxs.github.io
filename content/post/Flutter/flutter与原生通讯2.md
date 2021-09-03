---
title: "Flutter与原生通讯2"
date: 2021-08-14T21:37:41+08:00
draft: false
tags: ["Flutter"]
categories: ["Flutter"]
---



> Flutter 混合开发系列 包含如下：
>
> - 嵌入原生View-Android
> - 嵌入原生View-iOS
> - **与原生通信-MethodChannel**
> - 与原生通信-BasicMessageChannel
> - 与原生通信-EventChannel
> - 添加 Flutter 到 Android Activity
> - 添加 Flutter 到 Android Fragment
> - 添加 Flutter 到 iOS
>
> 每个工作日分享一篇，欢迎关注、点赞及转发。

#### 平台通信的3中方式

Flutter 与 Native 端通信有如下3个方法：

- **MethodChannel**：Flutter 与 Native 端相互调用，调用后可以返回结果，可以 Native 端主动调用，也可以Flutter主动调用，属于双向通信。此方式为最常用的方式， Native 端调用需要在主线程中执行。
- **BasicMessageChannel**：用于使用指定的编解码器对消息进行编码和解码，属于双向通信，可以 Native 端主动调用，也可以Flutter主动调用。
- **EventChannel**：用于数据流（event streams）的通信， Native 端主动发送数据给 Flutter，通常用于状态的监听，比如网络变化、传感器数据等。

#### 通信架构图

此图为官方的[架构图](https://link.juejin.cn?target=https%3A%2F%2Fflutter.dev%2Fdocs%2Fdevelopment%2Fplatform-integration%2Fplatform-channels)

![img](https:////p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/971fe1636576451d88376144f75ebb9a~tplv-k3u1fbpfcp-watermark.awebp)

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

```
dispatch_async(dispatch_get_main_queue(), ^{
  
});
  
```

Swift 代码：

```
DispatchQueue.main.async {
  
}
  
```

### MethodChannel

#### Flutter 端

Flutter 端创建 **MethodChannel** 通道，用于与原生端通信：

```dart
var channel = MethodChannel('com.flutter.guide.MethodChannel');
  
```

**com.flutter.guide.MethodChannel** 是 MethodChannel 的名称，原生端要与之对应。

发送消息：

```dart
var result = await channel.invokeMethod('sendData',{'name': 'laomeng', 'age': 18})
  
```

- 第一个参数表示**method**，方法名称，原生端会解析此参数。
- 第二个参数表示参数，类型任意，多个参数通常使用**Map**。
- 返回 **Future**，原生端返回的数据。

完整代码：

```dart
class MethodChannelDemo extends StatefulWidget {
  @override
  _MethodChannelDemoState createState() => _MethodChannelDemoState();
}

class _MethodChannelDemoState extends State<MethodChannelDemo> {
  var channel = MethodChannel('com.flutter.guide.MethodChannel');

  var _data;

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(),
      body: Column(
        children: [
          SizedBox(
            height: 50,
          ),
          RaisedButton(
            child: Text('发送数据到原生'),
            onPressed: () async {
              var result = await channel
                  .invokeMethod('sendData', {'name': 'laomeng', 'age': 18});
              var name = result['name'];
              var age = result['age'];
              setState(() {
                _data = '$name,$age';
              });
            },
          ),
          Text('原生返回数据：$_data')
        ],
      ),
    );
  }
}
  
```

#### Android 端

android 下创建 **MethodChannelDemo**：

```kotlin
package com.flutter.guide

import io.flutter.plugin.common.BinaryMessenger
import io.flutter.plugin.common.MethodCall
import io.flutter.plugin.common.MethodChannel

/**
 * des:
 */
class MethodChannelDemo(messenger: BinaryMessenger): MethodChannel.MethodCallHandler {

    private var channel: MethodChannel

    init {
        channel = MethodChannel(messenger, "com.flutter.guide.MethodChannel")
        channel.setMethodCallHandler(this)
    }

    override fun onMethodCall(call: MethodCall, result: MethodChannel.Result) {
        
    }
}
  
```

![img](https:////p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fdf2ed2f86994c2e9d30aba49cd96f51~tplv-k3u1fbpfcp-watermark.awebp)

**onMethodCall** 方法在 Flutter 端调用 invokeMethod 方法回调，解析方法如下：

```kotlin
override fun onMethodCall(call: MethodCall, result: MethodChannel.Result) {
    if (call.method == "sendData") {
        val name = call.argument("name") as String?
        val age = call.argument("age") as Int?

        var map = mapOf("name" to "hello,$name",
                "age" to "$age"
        )
        result.success(map)
    }
}
  
```

- **call.method** 字符串就是 invokeMethod 方法传入的 **method**。
- **call.argument** 是 invokeMethod 传入的参数，由于 Flutter 端传入的是 Map，所以上面的解析按照 Map 解析。
- **result.success()** 是返回给 Flutter 的结果。

Flutter 端解析：

```dart
var result = await channel
    .invokeMethod('sendData', {'name': 'laomeng', 'age': 18});
var name = result['name'];
var age = result['age'];
  
```

> 两端的解析要相互对应。

在 **MainActivity** 启动：

```kotlin
class MainActivity : FlutterActivity() {
    
    override fun configureFlutterEngine(flutterEngine: FlutterEngine) {
        super.configureFlutterEngine(flutterEngine)
        MethodChannelDemo(flutterEngine.dartExecutor.binaryMessenger)
    }
}
  
```

![img](https:////p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0cfb04c8c0214139b6d61ac0f3bed411~tplv-k3u1fbpfcp-watermark.awebp)

#### iOS 端

ios 下创建 **MethodChannelDemo**，按如下方式：

```swift
import Flutter
import UIKit

public class MethodChannelDemo {
    
    init(messenger: FlutterBinaryMessenger) {
        let channel = FlutterMethodChannel(name: "com.flutter.guide.MethodChannel", binaryMessenger: messenger)
        channel.setMethodCallHandler { (call:FlutterMethodCall, result:@escaping FlutterResult) in
            if (call.method == "sendData") {
                if let dict = call.arguments as? Dictionary<String, Any> {
                    let name:String = dict["name"] as? String ?? ""
                    let age:Int = dict["age"] as? Int ?? -1
                    result(["name":"hello,\(name)","age":age])
                }
            }
        }
    }
}

  
```

![img](https:////p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/32c25cc5ec1547a1ba2fe55aa185066a~tplv-k3u1fbpfcp-watermark.awebp)

在 **AppDelegate** 启动：

```swift
import UIKit
import Flutter

@UIApplicationMain
@objc class AppDelegate: FlutterAppDelegate {
  override func application(
    _ application: UIApplication,
    didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?
  ) -> Bool {
    
    let controller : FlutterViewController = window?.rootViewController as! FlutterViewController
    MethodChannelDemo(messenger: controller.binaryMessenger)
    GeneratedPluginRegistrant.register(with: self)
    
    return super.application(application, didFinishLaunchingWithOptions: launchOptions)
  }
}

  
```

![img](https:////p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2752886e9f654294aa88b13f90ce24e4~tplv-k3u1fbpfcp-watermark.awebp)

#### 原生端主动发送消息给Flutter

##### Flutter 端接收数据

```dart
@override
void initState() {
  super.initState();
  channel.setMethodCallHandler((call) {
    setState(() {
      _nativeData = call.arguments['count'];
    });
  });
}
  
```

##### Android 发送数据

原生端启动定时器，每隔一秒向 Flutter 发送数据，Android 端代码：

```dart
class MethodChannelDemo(var activity: Activity, messenger: BinaryMessenger) : MethodChannel.MethodCallHandler {

    private var channel: MethodChannel
    private var count = 0

    init {
        channel = MethodChannel(messenger, "com.flutter.guide.MethodChannel")
        channel.setMethodCallHandler(this)
        startTimer()
    }


    fun startTimer() {
        var timer = Timer().schedule(timerTask {
            activity.runOnUiThread {
                var map = mapOf("count" to count++)
                channel.invokeMethod("timer", map)
            }
        }, 0, 1000)

    }

    override fun onMethodCall(call: MethodCall, result: MethodChannel.Result) {
        if (call.method == "sendData") {
            val name = call.argument("name") as String?
            val age = call.argument("age") as Int?

            var map = mapOf("name" to "hello,$name",
                    "age" to "$age"
            )
            result.success(map)
        }
    }
}
  
```

> 注意：Android 端发送数据要在**主现场**中调用，即：
>
> ```kotlin
> activity.runOnUiThread {
>              var map = mapOf("count" to count++)
>              channel.invokeMethod("timer", map)
>          }
>   
> ```

启动修改如下：

```kotlin
class MainActivity : FlutterActivity() {

    override fun configureFlutterEngine(flutterEngine: FlutterEngine) {
        super.configureFlutterEngine(flutterEngine)
        MethodChannelDemo(this,flutterEngine.dartExecutor.binaryMessenger)
        flutterEngine.plugins.add(MyPlugin())
    }
}
  
```

![img](https:////p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b05f3f5b2ba54ed2b89036faf20c3b13~tplv-k3u1fbpfcp-watermark.awebp)

##### iOS 发送数据

iOS 端启动定时器代码如下：

```dart
import Flutter
import UIKit

public class MethodChannelDemo {
    var count =  0
    var channel:FlutterMethodChannel
    init(messenger: FlutterBinaryMessenger) {
        channel = FlutterMethodChannel(name: "com.flutter.guide.MethodChannel", binaryMessenger: messenger)
        channel.setMethodCallHandler { (call:FlutterMethodCall, result:@escaping FlutterResult) in
            if (call.method == "sendData") {
                if let dict = call.arguments as? Dictionary<String, Any> {
                    let name:String = dict["name"] as? String ?? ""
                    let age:Int = dict["age"] as? Int ?? -1
                    result(["name":"hello,\(name)","age":age])
                }
            }
        }
        startTimer()
    }
    
    func startTimer() {
        var timer = Timer.scheduledTimer(timeInterval:1, target: self, selector:#selector(self.tickDown),userInfo:nil,repeats: true)
    }
    @objc func tickDown(){
        count += 1
        var args = ["count":count]
        channel.invokeMethod("timer", arguments:args)
    }
}

  
```

![img](https:////p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1cb98e88f11248338f65de502e050071~tplv-k3u1fbpfcp-watermark.awebp)

