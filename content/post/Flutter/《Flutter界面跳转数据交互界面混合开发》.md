---
title: "《Flutter界面跳转数据交互界面混合开发》"
subtitle: ""
date: 2021-07-31T21:50:37+08:00
lastmod: 2021-07-31T21:50:37+08:00
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

## 界面跳转

Flutter界面跳转设计到两种类型：Flutter界面内部跳转、Flutter与Native界面之间跳转。

### Flutter界面内部跳转

#### 正常跳转

Flutter内部维护一个界面堆栈，跳转时可以直接跳转：

```
Navigator.push(context, new MaterialPageRoute(builder: (context) => new SecondPage()),);
```

也可以预先设置路由，通过路由名称进行跳转

```
routes: {
    "nameRoute":(BuildContext context)=>new SecondPage(),
    "thirdRoute":(BuildContext context)=>new ThirdPage(title:"请输入昵称"),
  },
  
  ···
  Navigator.of(context).pushNamed("nameRoute");
```

界面返回：

在堆栈中弹出该界面

```
Navigator.pop(context)
```

#### 带参跳转

带参跳入很简单，只需要在需要跳转的Widget中增加参数就可以了。

带参跳出通过 `Navigator.pop(context, [ T result ])`中携带具体参数。
接收方需要指定返回结果，例如下面示例：

```
Navigator.push<String>(context,
      new MaterialPageRoute(builder: (BuildContext context) {
    return new ThirdPage(title: "请输入昵称");   /// 跳转到第三页，并且传递参数过去
    })).then((String result) {

    // 接收返回值的逻辑处理，通过一个 Dialog 展示出来
    showDialog(
        context: context,
        builder: (BuildContext context) {
          return new AlertDialog(
            content: new Text("您输入的昵称为:$result"),
          );
        });
  });
```

#### 其它跳转方式

- popAndPushNamed 退栈然后入栈一个指定name的页面
- popUntil 重复调用pop直到predicate返回true
- pushAndRemoveUntil 跳转，然后清栈

### Flutter界面与Native界面跳转

#### Native界面跳转Flutter界面

最简单的方式，直接通过Native原生跳转，第二个Native中加载Flutter，不过这种方式不推荐。

实际上同样可以通过router指定具体的page(**这部分存疑，只查到预览版使用该功能，正式版该功能未找到**)，看如下代码：

```
View flutterView = Flutter.createView(
  MainActivity.this,
  getLifecycle(),
  "route1"
);
FrameLayout.LayoutParams layout = new FrameLayout.LayoutParams(600, 800);
layout.leftMargin = 100;
layout.topMargin = 200;
setContentView(flutterView);
```

该方法说明见 [Add Flutter to existing apps](https://github.com/flutter/flutter/wiki/Add-Flutter-to-existing-apps#experiment-turn-the-flutter-project-into-a-module)

正式版中未找到相关文档说明，不过在官方代码中有如下示例：

```
···

String[] args = getArgsFromIntent(getIntent());
        FlutterMain.ensureInitializationComplete(getApplicationContext(), args);
        
···

FlutterRunArguments runArguments = new FlutterRunArguments();
        runArguments.bundlePath = FlutterMain.findAppBundlePath(getApplicationContext());
        
···

flutterView = getFlutterView();
flutterView.runFromBundle(runArguments);

···

setContentView(flutterView);
```

可以通过参数指定flutter界面的具体信息，并进行展示。

#### Flutter界面跳转Native界面

Flutter跳Native界面利用的是`MethodChannel`，也就是下面数据交互部分的内容。Flutter发送一个信号通知到Native端，执行预设的工作。

例如：

```
new MethodChannel(getFlutterView(), CHANNEL).setMethodCallHandler(
  new MethodChannel.MethodCallHandler() {
      @Override
      public void onMethodCall(MethodCall call, MethodChannel.Result result) {
          //打开新的Activity
          startActivity(new Intent(MainActivity.this, FloatingActivity.class));
      }
  });
```

## 数据交互

Flutter与Native的互相调用，必须通过Platform Channel来实现。

Flutter定义了三种不同的Channel，分别有不同的用途：

- BasicMessageChannel：用于传递字符串和半结构化的信息
- MethodChannel：用于传递方法调用，常用于Flutter调用Native
- EventChannel: 用于数据流通信，常用与Native通知Flutter

这三种数据交互采用同样的方式，有三个相同的字段：String name、BinaryMessenger messenger、MethodCodec codec.

其中name唯一标识当前channel，messenger进行消息传递，codec用于二进制格式数据与基础数据之间的编解码。

另外还有Handler用于消息处理，存在三种Handler MessageHandler、MethodHandler、StreamHandler分别对应三种Channel

示例如下：

Flutter端

```
static const EventChannel _eventChannel = const EventChannel('samples.flutter.io/charging');
static const MethodChannel _methodChannel = const MethodChannel('samples.flutter.io/battery');
```

Native端

```
public static void registerWith(Registrar registrar) {
        /**
         * Channel名称：必须与Flutter App的Channel名称一致
         */
        private static final String METHOD_CHANNEL = "samples.flutter.io/battery";
        private static final String EVENT_CHANNEL = "samples.flutter.io/charging";

        final MethodChannel methodChannel = new MethodChannel(registrar.messenger(), METHOD_CHANNEL);
        
        final EventChannel eventChannel = new EventChannel(registrar.messenger(), EVENT_CHANNEL);
        eventChannel.setStreamHandler(plugin);
    }
```

首先在Flutter与Native注册Channel的name标识，进行绑定。

### Flutter调用Native

Flutter端

```
Future<String> getBatteryLevel() async {
    String batteryLevel;
    try {
      final int result = await _methodChannel.invokeMethod('getBatteryLevel',{'paramName':'paramVale'});
      batteryLevel = 'Battery level: $result%.';
    } catch(e) {
      batteryLevel = 'Failed to get battery level.';
    }
    return batteryLevel;
  }
```

Native端

```
methodChannel.setMethodCallHandler(new MethodCallHandler(){

  @Override
    public void onMethodCall(MethodCall call, Result result) {
        if (call.method.equals("getBatteryLevel")) {
            Random random = new Random();
            result.success(random.nextInt(100));
        } else {
            result.notImplemented();
        }
    }
});
```

### Native调用Flutter

Flutter端

```
void listenNativeEvent() {
  _eventChannel.receiveBroadcastStream().listen(_onEvent, onError:_onError);
}

void _onEvent(Object event) {
  print("Battery status: ${event == 'charging' ? '' : 'dis'}charging.");
}

void _onError(Object error) {
  print('Battery status: unknown.');
}
```

Native端

```
eventChannel.setStreamHandler(new EventChannel.StreamHandler(){

  @Override
    public void onListen(Object obj, EventChannel.EventSink eventSink) {
        createChargingStateChangeReceiver(eventSink);
    }

    @Override
    public void onCancel(Object obj) {
    }
});

private BroadcastReceiver createChargingStateChangeReceiver(final EventSink events) {
    return new BroadcastReceiver() {
        @Override
        public void onReceive(Context context, Intent intent) {
            int status = intent.getIntExtra(BatteryManager.EXTRA_STATUS, -1);

            if (status == BatteryManager.BATTERY_STATUS_UNKNOWN) {
                events.error("UNAVAILABLE", "Charging status unavailable", null);
            } else {
                boolean isCharging = status == BatteryManager.BATTERY_STATUS_CHARGING ||
                        status == BatteryManager.BATTERY_STATUS_FULL;
                events.success(isCharging ? "charging" : "discharging");
            }
        }
    };
}
```

## 界面混合开发

混合开发同样分为两种:Native嵌套Flutter、Flutter嵌套Native

### Native嵌套Flutter

这部分很简单，直接将FlutterView作为一个简单的View操作就可以了，同样支持xml配置。如：

```
protected void onCreate(Bundle savedInstanceState) {
  ···
  super.onCreate(savedInstanceState);
    FlutterMain.ensureInitializationComplete(getApplicationContext(), args);
  setContentView(R.layout.flutter_view_layout);
  ···
  flutterView = findViewById(R.id.flutter_view);
  messageChannel = new BasicMessageChannel<>(flutterView, CHANNEL, StringCodec.INSTANCE);
  ···
}
```

### Flutter嵌套Native

这种方式也不复杂，在Flutter中支持AndroidView、UiKitView来分别支持Android、iOS原生控件展示。以Android为例：

首先在dart语言中设置AndroidView以及该控件的类型标识

```
new Expanded(child: AndroidView(viewType: "ACalendarView"))
```

剩下的就全部在Native端进行配置

```
// 注册类型标识
PluginRegistry.Registrar registrar = registry.registrarFor(key);
        registrar.platformViewRegistry().registerViewFactory("ACalendarView", new ACalendarViewFactory(new StandardMessageCodec(), calendarView));
```

指定需要展示的View

```
public class ACalendarViewFactory extends PlatformViewFactory {

    private View calendarView;

    public ACalendarViewFactory(MessageCodec<Object> createArgsCodec, View calendarView) {
        super(createArgsCodec);
        this.calendarView = calendarView;
    }

    @Override
    public PlatformView create(Context context, int i, Object o) {
        return new PlatformView() {
            @Override
            public View getView() {
                return calendarView;
            }

            @Override
            public void dispose() {

            }
        };
    }
}
```

## 