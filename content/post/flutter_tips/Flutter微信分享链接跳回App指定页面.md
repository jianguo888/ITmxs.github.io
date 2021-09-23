---
title: "Flutter微信分享链接跳回App指定页面"
date: 2021-09-23T10:40:46+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---

最近在使用flutter开发APP，flutter实现了一套代码同时生成Android和iOS两个平台的APP，可以实现零基础快速上手APP开发，缩短开发周期。但flutter仍处于较快增长期，版本迭代速度快，文档资料相对较少，这里将开发中遇到的一些问题整理下来，备忘+分享。



flutter版本：1.12.13+hotfix.5

# 微信分享

这里使用fluwx插件实现微信中分享，版本号1.2.1+1。

## 配置依赖

在pubspec.yaml中增加依赖

```
dependencies:
  flutter:
    sdk: flutter
  flutter_localizations:
    sdk: flutter
  #other dependencies
  fluwx: ^1.2.1+1
```

## Android配置

如果需要微信回调，需要配置WXEntryActivity和WXPayEntryActivity，如果不使用回调，则不需要特殊配置。详见官方doc

## IOS配置

如官方文档所示，需要配置ios的*URLSchema, LSApplicationQueriesSchemes or universal link* 

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/image/aa946417aadd26e2a22350ad08e24a79.png)

ios/Runner/Info.plist

```
<key>CFBundleURLTypes</key>
    <array>
    <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>your url name</string>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>your scheme</string>
        </array>
    </dict>
    <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>weixin</string>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>you wechat appid</string>
        </array>
    </dict>
</array>
<key>LSApplicationQueriesSchemes</key>
<array>
    <string>wechat</string>
    <string>weixin</string>
    <string>weixinULAPI</string>
</array>
```

其中CFBundleURLTypes的第一项是为了能够从url跳回APP所设置

ios/Runner/Runner.entitlements

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
  <!-- ... other keys -->
  <key>com.apple.developer.associated-domains</key>
  <array>
    <string>webcredentials:your universal link</string>
  </array>
  <!-- ... other keys -->
</dict>
</plist>
```

这里的universal link要与微信开放平台上的设置保持一致。

## Flutter调用

注册微信sdk，如果用于ios，必须指定*universalLink*

```
import 'package:fluwx/fluwx.dart' as fluwx;
@override
void initState() {
    fluwx.registerWxApi(
        appId: your wechat appid,
        doOnAndroid: true,
        doOnIOS: true,
        universalLink: your universal link
    );
}
```

分享，以图文链接到好友为例

```
fluwx.shareToWeChat(
    fluwx.WeChatShareWebPageModel(
        title: your title, 
        description: your description(can split by \n),
        webPage: 'https://example.com/app/jump.html?pageName=xx&id=xx',
        thumbnail: 'https://example.com/images/share_weixin.png',
        scene: fluwx.WeChatScene.SESSION
        )
    ).then((data) {
    print (data);
});

```

至此，可实现从APP分享到微信，下一步将从微信点击分享后的链接，唤起APP并直接打开指定页面。

# 打开APP指定页面

从APP分享出去只是产品宣传的第一步，下一步需要接收到的用户通过点击链接卡片可以打开我们的APP的某个指定页面。当然，用户点击分享链接后，可以打开产品的小程序or网页版，以更好的提升体验，这里仅以跳转APP为例。

很多文档提到改写Activity类来实现跳转链接参数的解析，从而进一步跳转到指定页面，但随着flutter的升级，kotlin的Activity相关API有明显变化，由于笔者并未做过Android开发，所以放弃了硬写的方案，这里使用uni_links来实现参数的解析。

## 配置依赖

```
1uni_links: ^0.2.0
2
```

## Android配置

android/app/src/main/AndroidManifest.xml

```
<activity
    android:name=".MainActivity"
    android:launchMode="singleTop"
    android:theme="@style/LaunchTheme"
    android:configChanges="orientation|keyboardHidden|keyboard|screenSize|smallestScreenSize|locale|layoutDirection|fontScale|screenLayout|density|uiMode"
    android:hardwareAccelerated="true"
    android:windowSoftInputMode="adjustResize">
    <meta-data
        android:name="io.flutter.app.android.SplashScreenUntilFirstFrame"
        android:value="true" />
    <intent-filter>
        <action android:name="android.intent.action.MAIN"/>
        <category android:name="android.intent.category.LAUNCHER"/>
    </intent-filter>
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data
            android:scheme="yourScheme"
            android:host="yourHost"
        />
    </intent-filter>
</activity>
```

## IOS配置

在上面微信配置中已加入相关配置（这里没有使用HTTPS scheme，如果使用的话，还需要一些额外配置，详见uni_link的文档）

## Flutter调用

uni_link的官方example已经非常详尽，照着来就可以实现，如下是我的实现

在main.dart中实现

```
class MyAppState extends State<MyApp> with WidgetsBindingObserver {
  StreamSubscription _sub;
  String _latestLink = 'Unknown';
  Uri _latestUri;
  // This widget is the root of your application.
  @override
  void initState() {
    super.initState();
    initPlatformState();
  }

  @override
  void dispose() {
    if (_sub != null) _sub.cancel();
    super.dispose();
  }

  Future<void> initPlatformState() async {
    _sub = getLinksStream().listen((String link) {
      if (!mounted) return;
      setState(() {
        _latestLink = link ?? "Unknown";
        _latestUri = null;
        try {
          if (link != null) _latestUri = Uri.parse(link);
        } on FormatException {}
      });
    }, onError: (err) {
      if (!mounted) return;
      setState(() {
        _latestLink = 'Failed to get latest link: $err.';
        _latestUri = null;
      });
    });
    getLinksStream().listen((String link) {
      print('got link: $link');
    }, onError: (err) {
      print('got err: $err');
    });
    String initialLink;
    Uri initialUri;
    try {
      initialLink = await getInitialLink();
      print('initial link: $initialLink');
      if (initialLink != null) initialUri = Uri.parse(initialLink);
    } on PlatformException {
      initialLink = 'Failed to get initial link.';
      initialUri = null;
    } on FormatException {
      initialLink = 'Failed to parse the initial link as Uri.';
      initialUri = null;
    }
    if (!mounted) return;

    setState(() {
      _latestLink = initialLink;
      _latestUri = initialUri;
    });
  }

  @override
  Widget build(BuildContext context) {
    final queryParams = _latestUri?.queryParametersAll?.entries?.toList();
    // print (queryParams);
    // print (queryParams?.length);
    Widget homeWidget = new SplashPage();
    String pageType;
    String taskID;
    if (queryParams != null) {
      for (var param in queryParams) {
        if (param.key == 'pageName') {
          pageType = param.value.length > 0 ? param.value[0] : null;
        }
        if (param.key == 'taskid') {
          taskID = param.value.length > 0 ? param.value[0] : null;
        }
      }
    }
    print (pageType);
    if (pageType == 'report') {
      homeWidget = new ReportPage(taskID: taskID,);
    }
    return MaterialApp(
      routes: {
        Config.routeMain: (context) => MyHomePage()
      },
      debugShowCheckedModeBanner: false,
      title: 'your title',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: homeWidget
    );
  }
}
```

## 中转页面

这里写了一个简单的中转页面

```
<!Doctype html>
<html xmlns=http://www.w3.org/1999/xhtml>
<head>
    <meta http-equiv=Content-Type content="text/html;charset=utf-8">
    <title>Your Title</title>
</head>
<body>
    <style>
        #mobliestart{font-size:40px;}
    </style>
    <a href='yourScheme://yourHost?page=report&taskid=1' id="mobliestart">点击打开APP</a>

    <script type="text/javascript"> 
        function applink(){   
            window.location = 'yourScheme://yourHost?page=report&taskid=1'; 
        }
        applink();
    </script>
</body>
</html>
```

中转页面设置了自动跳转和点击跳转，由于微信浏览器对scheme link的限制，ios和Android都需要点击右上角的‘浏览器中打开’。ios（iPhone xr，13.3）在Safari中打开后会自动弹出对话框，Android（华为）在系统浏览器和chrome会自动弹出对话框，qq浏览器需要点击页面超链接才会弹出对话框。

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/image/ff00d89f0d3885fab302bfdf0efa6bb4.png)![img](https://luckly007.oss-cn-beijing.aliyuncs.com/image/37fa289846aa5d3eba58902528d6e721.png)

 

注：目前仍存在一个问题，如果APP处于后台打开状态，从链接打开后，只是唤起APP，并不会跳转到指定页面，通过打印日志，build函数中解析链接参数的逻辑解析正确，目前这个问题还未进一步解决。



## URL Scheme的作用

### iOS Scheme介绍

我们都知道苹果手机中的APP都有一个沙盒，APP就是一个信息孤岛，相互是不可以进行通信的。但是iOS的APP可以注册自己的URL Scheme，URL Scheme是为方便app之间互相调用而设计的。我们可以通过系统的OpenURL来打开该app，并可以传递一些参数。

例如：你在Safari里输入www.alipay.com，就可以直接打开你的支付宝app，前提是你的手机装了支付宝。如果你没有装支付宝，应该显示的是支付宝下载界面，点击会跳到AppStore的支付宝下载界面。

URL Scheme必须能唯一标识一个APP，如果你设置的URL Scheme与别的APP的URL Scheme冲突时，你的APP不一定会被启动起来。因为当你的APP在安装的时候，系统里面已经注册了你的URL Scheme。

一般情况下，是会调用先安装的app。但是iOS的系统app的URL Scheme肯定是最高的。所以我们定义URL Scheme的时候，尽量避开系统app已经定义过的URL Scheme。

### Android Scheme介绍

android中的scheme是一种页面内跳转协议;

通过定义自己的scheme协议，可以非常方便跳转app中的各个页面；

通过scheme协议，服务器可以定制化告诉App跳转到APP内部页面。

## 使用[uni_links](https://link.juejin.cn?target=https%3A%2F%2Fpub.dev%2Fpackages%2Funi_links%23-installing-tab-)库

### 1 、`pubspec.yaml`文件新增依赖

```
dependencies:
  uni_links: 0.4.0
复制代码
```

### 2 、安装

```
flutter pub get

Running "flutter pub get" in dynamic_theme...                       5.8s
Process finished with exit code 0
复制代码
```

### 3、Dart代码中使用插件

```
import 'package:uni_links/uni_links.dart';
复制代码
```

## Android 配置

**⚠️注意：Scheme 命名不支持`dynamic_theme`在`iOS`中测试了一下无法打开,改成了全小写`dynamictheme`**

### `android/app/src/main/AndroidManifest.xml`

**新增以下代码[查看完整代码](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2FTecode%2Fdynamic_theme%2Fblob%2F852afd2e3ebc85c9d143324cc5282b20cda233c2%2Fios%2FRunner%2FInfo.plist%23L22)**

```
<!-- Deep Links -->
<intent-filter>
    <action android:name="android.intent.action.VIEW" />
    <category android:name="android.intent.category.DEFAULT" />
    <category android:name="android.intent.category.BROWSABLE" />
    <!-- Accepts URIs that begin with YOUR_SCHEME://YOUR_HOST -->
    <data
          android:scheme="[YOUR_SCHEME]"
          android:host="[YOUR_HOST]" />
</intent-filter>
复制代码
```

**例子：**

```
<intent-filter>
 <action android:name="android.intent.action.VIEW"/>
  <category android:name="android.intent.category.DEFAULT"/>
  <category android:name="android.intent.category.BROWSABLE"/>
  <data android:scheme="dynamictheme"/>
  <data 
        android:host="detail"
        android:scheme="dynamictheme"/>
</intent-filter>
复制代码
```

## iOS 配置

### 打开`Xcode->Info->URL Types`设置`URL Scheme`

添加完也直接反应到配置文件`info.plist`中了，当然你要是觉得自己很厉害，也可以直接在`info.plist`添加。



![iOS 配置](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/6/11/172a182ec0deebf1~tplv-t2oaga2asx-watermark.awebp)



**`ios/Runner/Info.plist`**

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
...
<!-- 其它配置 -->
	<array>
		<dict>
			<key>CFBundleTypeRole</key>
			<string>Editor</string>
			<key>CFBundleURLSchemes</key>
			<array>
				<string>dynamictheme</string>
			</array>
		</dict>
	</array>
...
<!-- 其它配置 -->
</dict>
</plist>

复制代码
```

## Dart 代码

**不同状态下实现页面跳转**

```
Future<void> initPlatformStateForStringUniLinks() async {
    String initialLink;
    // App未打开的状态在这个地方捕获scheme
    try {
      initialLink = await getInitialLink();
      print('initial link: $initialLink');
      if (initialLink != null) {
        print('initialLink--$initialLink');
        //  跳转到指定页面
        schemeJump(context, initialLink);
      }
    } on PlatformException {
      initialLink = 'Failed to get initial link.';
    } on FormatException {
      initialLink = 'Failed to parse the initial link as Uri.';
    }
    // App打开的状态监听scheme
    _sub = getLinksStream().listen((String link) {
      if (!mounted || link == null) return;
      print('link--$link');
    //  跳转到指定页面
      schemeJump(context, link);
    }, onError: (Object err) {
      if (!mounted) return;
    });
}
复制代码
```

**解析Scheme跳转页面**

我配置的`scheme`是`dynamictheme://`使用的是`Deep Link`，你也可以配置成`http://www.xx.com`和`https://www.xx.com`这种是`App Links`。

```
final Uri _jumpUri = Uri.parse(schemeUrl.replaceFirst(
    'dynamictheme://',
    'http://path/',
  ));
  switch (_jumpUri.path) {
    case '/detail':
      Navigator.of(context).pushNamed(
        Detail.routeName,
        arguments: Detail(value: _jumpUri.queryParameters['name'] ?? '详情'),
      );
      break;
    default:
      break;
}
复制代码
```

## Scheme [测试地址](https://link.juejin.cn?target=https%3A%2F%2Ftecode.github.io%2Fdynamic_theme%2Fdocument%2Findex.html)

测试前需要先[安装APK](https://link.juejin.cn?target=https%3A%2F%2Fwww.pgyer.com%2FQChJ)(目前只有安卓APK可以下载，iOS需要自己拉代码打包)。

```
<a href="dynamictheme://"> 打开App(dynamictheme://) </a>
<a href="dynamictheme://detail"> 打开App跳转到详情页面 </a>
<a href="dynamictheme://detail?name=flutter"> 打开App跳转到详情页面带上参数 </a>
复制代码
```

## iOS 效果预览

### 未打开App（开启以后跳转）



![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/6/11/172a185159dc2656~tplv-t2oaga2asx-watermark.awebp)



### 已打开App（监听Scheme）



![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/6/11/172a1856a5cb2325~tplv-t2oaga2asx-watermark.awebp)



## Android 效果预览

### 未打开App（开启以后跳转）



![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/6/11/172a1859954d23e8~tplv-t2oaga2asx-watermark.awebp)



### 已打开App（监听Scheme）



![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/6/11/172a185c36afb118~tplv-t2oaga2asx-watermark.awebp)


