---
title: "Flutter打开外部链接"
date: 2021-09-20T09:51:15+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---

> 一个 Flutter 插件项目，用于帮助应用程序/深层链接（Android）和通用链接和自定义 URL 方案（iOS）。
>
> 这些链接只是类似网络浏览器的链接，可以激活您的应用程序，并且可能包含可用于加载应用程序的特定部分或从网站（或其他应用程序）继续某些用户活动的信息。
>
> 应用程序链接和通用链接是常规的 https 链接，因此如果应用程序未安装（或设置正确），它们将加载到浏览器中，允许您显示网页以供进一步操作，例如。安装应用程序。
>
> 确保您仔细阅读安装和使用指南，特别是对于应用程序/通用链接（https 方案）。



安装

```yaml
dependencies:
  uni_links:
```

## 对于android

Uni Links 支持两种类型的 Android 链接：“应用链接”和“深层链接”。

- 应用程序链接仅适用于`https`方案并需要指定的主机，以及托管文件 - `assetlinks.json`. 检查下面的指南链接。
- Deep Links 可以有任何自定义方案，不需要主机，也不需要托管文件。缺点是任何应用程序都可以声明方案 + 主机组合，因此请确保您的方案尽可能独特，例如。`HST0000001://host.com`.

您需要在 中至少声明两个意图过滤器之一`android/app/src/main/AndroidManifest.xml`：

You need to declare at least one of the two intent filters in `android/app/src/main/AndroidManifest.xml`:

```dart
<manifest ...>
  <!-- ... other tags -->
  <application ...>
    <activity ...>
      <!-- ... other tags -->

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

      <!-- App Links -->
      <intent-filter android:autoVerify="true">
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <!-- Accepts URIs that begin with https://YOUR_HOST -->
        <data
          android:scheme="https"
          android:host="[YOUR_HOST]" />
      </intent-filter>
    </activity>
  </application>
</manifest>
```

The `android:host` attribute is optional for Deep Links.

To further the specificity you can add an `android:pathPrefix` attribute:

```dart
<!-- Accepts URIs that begin with YOUR_SCHEME://YOUR_HOST/NAME/NAME... -->
<!-- Accepts URIs that begin with       https://YOUR_HOST/NAME/NAME... -->
<!-- note that the leading "/" is required for pathPrefix -->
<data
  android:scheme="[YOUR_SCHEME_OR_HTTPS]"
  android:host="[YOUR_HOST]"
  android:pathPrefix="/[NAME][/NAME...]" />
```

## 对于 iOS

iOS 中有两种链接：“通用链接”和“自定义 URL 方案”。

- 通用链接仅适用于`https`方案并需要指定的主机、权利和托管文件 - `apple-app-site-association`. 检查下面的指南链接。
- 自定义 URL 方案可以具有...任何自定义方案，并且没有主机特性、权利或托管文件。缺点是任何应用程序都可以声明任何方案，因此请确保您的方案尽可能独特，例如。`hst0000001`或`myIncrediblyAwesomeScheme`。

您需要至少声明两者之一。

——

对于**通用链接，**您需要添加或创建 `com.apple.developer.associated-domains`权利 - 通过 Xcode（见下文）或通过编辑（或创建并添加到 Xcode）`ios/Runner/Runner.entitlements` 文件。



```dart
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
  <!-- ... other keys -->
  <key>com.apple.developer.associated-domains</key>
  <array>
    <string>applinks:[YOUR_HOST]</string>
  </array>
  <!-- ... other keys -->
</dict>
</plist>
```

对于**自定义 URL 方案，**您需要在`ios/Runner/Info.plist`（或通过 Xcode 的目标信息编辑器，在 URL 类型下）声明方案 ：

```dart
<?xml ...>
<!-- ... other tags -->
<plist>
<dict>
  <!-- ... other tags -->
  <key>CFBundleURLTypes</key>
  <array>
    <dict>
      <key>CFBundleTypeRole</key>
      <string>Editor</string>
      <key>CFBundleURLName</key>
      <string>[ANY_URL_NAME]</string>
      <key>CFBundleURLSchemes</key>
      <array>
        <string>[YOUR_SCHEME]</string>
      </array>
    </dict>
  </array>
  <!-- ... other tags -->
</dict>
</plist>
```



## 用法

您的应用程序将通过两种方式接收链接 - 从冷启动和从后台获取。

**注意**：`getInitialLink`/`getInitialUri`应该 在您的应用程序的生命周期中*仅*处理*一次*，因为它不会在您的应用程序的整个生命周期中发生变化。

### 初始链接（字符串）

返回应用程序启动的链接（如果有）。

您应该在应用程序生命周期的早期处理这个问题，并且只处理一次。您可以随意多次读取该值，但只能处理一次。

```
import 'dart:async';
import 'dart:io';

import 'package:uni_links/uni_links.dart';
import 'package:flutter/services.dart' show PlatformException;

// ...

  Future<void> initUniLinks() async {
    // Platform messages may fail, so we use a try/catch PlatformException.
    try {
      final initialLink = await getInitialLink();
      // Parse the link and warn the user, if it is not correct,
      // but keep in mind it could be `null`.
    } on PlatformException {
      // Handle exception by warning the user their action did not succeed
      // return?
    }
  }

// ...
```

### 初始链接 (Uri)

与 相同`getInitialLink`，但转换为`Uri`。

注意：您应该在应用程序生命周期的早期处理这个问题，并且只*处理*一次。

```
 // Uri parsing may fail, so we use a try/catch FormatException.
    try {
      final initialUri = await getInitialUri();
      // Use the uri and warn the user, if it is not correct,
      // but keep in mind it could be `null`.
    } on FormatException {
      // Handle exception by warning the user their action did not succeed
      // return?
    }
    // ... other exception handling like PlatformException
```

可以通过使用 来实现相同的目的`Uri.parse(initialLink)`，这就是这种便捷方法的作用。

### 在更改事件（字符串）

通常你会检查`getInitialLink`并监听变化。

```dart
import 'dart:async';
import 'dart:io';

import 'package:uni_links/uni_links.dart';

// ...

  StreamSubscription _sub;

  Future<void> initUniLinks() async {
    // ... check initialLink

    // Attach a listener to the stream
    _sub = linkStream.listen((String? link) {
      // Parse the link and warn the user, if it is not correct
    }, onError: (err) {
      // Handle exception by warning the user their action did not succeed
    });

    // NOTE: Don't forget to call _sub.cancel() in dispose()
  }

// ...
```

### 更改事件 (Uri)

与 相同`linkStream`，但转换为发射`Uri`对象。

通常你会检查`getInitialUri`并监听变化。

```dart
import 'dart:async';
import 'dart:io';

import 'package:uni_links/uni_links.dart';

// ...

  StreamSubscription _sub;

  Future<void> initUniLinks() async {
    // ... check initialUri

    // Attach a listener to the stream
    _sub = uriLinkStream.listen((Uri? uri) {
      // Use the uri and warn the user, if it is not correct
    }, onError: (err) {
      // Handle exception by warning the user their action did not succeed
    });

    // NOTE: Don't forget to call _sub.cancel() in dispose()
  }

// ...
```

### 有关从链接开始的应用程序的更多信息

如果应用程序被终止（或者不是在后台运行）并且操作系统必须重新启动它 - 这是一个冷启动。在这种情况下，`getInitialLink`将拥有启动您的应用程序的链接，并且 Stream 不会生成链接（在那个时间点）。

或者-如果应用程序在后台运行，操作系统必须把它带到前台流将成为一个生产环节，而 `getInitialLink`将两种`null`或初始链路，与该应用程序已启动。

由于这两种情况 - 您应该始终添加对初始链接（或 URI）的检查，并订阅链接流（或 URI）。

### 调用链接的工具

如果你注册一个模式，比如说`unilink`，你可以使用这些 cli 工具：

### 安卓

您可以在[Android Studio 中](https://developer.android.com/studio/write/app-link-indexing#testindent)执行以下任务。

假设您已经安装了 Android Studio（使用 SDK 平台工具）：

```dart
adb shell 'am start -W -a android.intent.action.VIEW -c android.intent.category.BROWSABLE -d "unilinks://host/path/subpath"'
adb shell 'am start -W -a android.intent.action.VIEW -c android.intent.category.BROWSABLE -d "unilinks://example.com/path/portion/?uid=123&token=abc"'
adb shell 'am start -W -a android.intent.action.VIEW -c android.intent.category.BROWSABLE -d "unilinks://example.com/?arr%5b%5d=123&arr%5b%5d=abc&addr=1%20Nowhere%20Rd&addr=Rand%20City%F0%9F%98%82"'
adb shell 'am start -W -a android.intent.action.VIEW -c android.intent.category.BROWSABLE -d "unilinks://@@malformed.invalid.url/path?"'

```

如果您[`adb`](https://developer.android.com/studio/command-line/adb) 的路径中没有，但有`$ANDROID_HOME`env 变量，则使用 `"$ANDROID_HOME"/platform-tools/adb ...`.

注意：或者，您可以简单地输入 an`adb shell`并运行其中的 [`am`](https://developer.android.com/studio/command-line/adb#am)命令。

注意：我使用单引号，因为`shell`命令后面的内容是将在模拟器（或设备）中运行的内容和 shell 元字符，例如问号 ( `?`) 和与号 ( `&`)，通常与您自己的 shell 具有不同的含义。

`adb shell` 与唯一可用的设备（或模拟器）通信，因此如果您有多个设备，则必须通过以下方式指定要在其中运行 shell 的设备：

- 该*只*USB连接的设备-`adb -d shell '...'`
- 该*唯一的*模拟设备-`adb -e shell '...'`

您可以`adb devices`用来列出当前可用的设备（同样 `flutter devices`做同样的工作）。

### IOS

假设您已经安装了 Xcode：

```dart
/usr/bin/xcrun simctl openurl booted "unilinks://host/path/subpath"
/usr/bin/xcrun simctl openurl booted "unilinks://example.com/path/portion/?uid=123&token=abc"
/usr/bin/xcrun simctl openurl booted "unilinks://example.com/?arr%5b%5d=123&arr%5b%5d=abc&addr=1%20Nowhere%20Rd&addr=Rand%20City%F0%9F%98%82"
/usr/bin/xcrun simctl openurl booted "unilinks://@@malformed.invalid.url/path?"

```

如果您的路径中有`xcrun`(或`simctl`)，则可以直接调用它。

该标志`booted`假定一个`open -a Simulator`带有启动设备的开放模拟器（您可以通过 启动它 ）。您可以通过指定其 UUID（通过`xcrun simctl list`或找到`flutter devices`）来定位特定设备，替换`booted`标志。

### 应用链接或通用链接

这些类型的链接`https`用于模式，因此您可以通过替换`unilinks`为`https`.

## 示例

```dart
import 'dart:async';
import 'dart:io';

import 'package:flutter/foundation.dart';
import 'package:flutter/material.dart';
import 'package:flutter/services.dart';
import 'package:uni_links/uni_links.dart';

bool _initialUriIsHandled = false;

void main() => runApp(MaterialApp(home: MyApp()));

class MyApp extends StatefulWidget {
  @override
  _MyAppState createState() => _MyAppState();
}

class _MyAppState extends State<MyApp> with SingleTickerProviderStateMixin {
  Uri? _initialUri;
  Uri? _latestUri;
  Object? _err;

  StreamSubscription? _sub;

  final _scaffoldKey = GlobalKey();
  final _cmds = getCmds();
  final _cmdStyle = const TextStyle(
      fontFamily: 'Courier', fontSize: 12.0, fontWeight: FontWeight.w700);

  @override
  void initState() {
    super.initState();
    _handleIncomingLinks();
    _handleInitialUri();
  }

  @override
  void dispose() {
    _sub?.cancel();
    super.dispose();
  }

  /// Handle incoming links - the ones that the app will recieve from the OS
  /// while already started.
  void _handleIncomingLinks() {
    if (!kIsWeb) {
      // It will handle app links while the app is already started - be it in
      // the foreground or in the background.
      _sub = uriLinkStream.listen((Uri? uri) {
        if (!mounted) return;
        print('got uri: $uri');
        setState(() {
          _latestUri = uri;
          _err = null;
        });
      }, onError: (Object err) {
        if (!mounted) return;
        print('got err: $err');
        setState(() {
          _latestUri = null;
          if (err is FormatException) {
            _err = err;
          } else {
            _err = null;
          }
        });
      });
    }
  }

  /// Handle the initial Uri - the one the app was started with
  ///
  /// **ATTENTION**: `getInitialLink`/`getInitialUri` should be handled
  /// ONLY ONCE in your app's lifetime, since it is not meant to change
  /// throughout your app's life.
  ///
  /// We handle all exceptions, since it is called from initState.
  Future<void> _handleInitialUri() async {
    // In this example app this is an almost useless guard, but it is here to
    // show we are not going to call getInitialUri multiple times, even if this
    // was a weidget that will be disposed of (ex. a navigation route change).
    if (!_initialUriIsHandled) {
      _initialUriIsHandled = true;
      _showSnackBar('_handleInitialUri called');
      try {
        final uri = await getInitialUri();
        if (uri == null) {
          print('no initial uri');
        } else {
          print('got initial uri: $uri');
        }
        if (!mounted) return;
        setState(() => _initialUri = uri);
      } on PlatformException {
        // Platform messages may fail but we ignore the exception
        print('falied to get initial uri');
      } on FormatException catch (err) {
        if (!mounted) return;
        print('malformed initial uri');
        setState(() => _err = err);
      }
    }
  }

  @override
  Widget build(BuildContext context) {
    final queryParams = _latestUri?.queryParametersAll.entries.toList();

    return Scaffold(
      key: _scaffoldKey,
      appBar: AppBar(
        title: const Text('uni_links example app'),
      ),
      body: ListView(
        shrinkWrap: true,
        padding: const EdgeInsets.all(8.0),
        children: [
          if (_err != null)
            ListTile(
              title: const Text('Error', style: TextStyle(color: Colors.red)),
              subtitle: Text('$_err'),
            ),
          ListTile(
            title: const Text('Initial Uri'),
            subtitle: Text('$_initialUri'),
          ),
          if (!kIsWeb) ...[
            ListTile(
              title: const Text('Latest Uri'),
              subtitle: Text('$_latestUri'),
            ),
            ListTile(
              title: const Text('Latest Uri (path)'),
              subtitle: Text('${_latestUri?.path}'),
            ),
            ExpansionTile(
              initiallyExpanded: true,
              title: const Text('Latest Uri (query parameters)'),
              children: queryParams == null
                  ? const [ListTile(dense: true, title: Text('null'))]
                  : [
                      for (final item in queryParams)
                        ListTile(
                          title: Text(item.key),
                          trailing: Text(item.value.join(', ')),
                        )
                    ],
            ),
          ],
          _cmdsCard(_cmds),
          const Divider(),
          if (!kIsWeb)
            ListTile(
              leading: const Icon(Icons.error, color: Colors.red),
              title: const Text(
                'Force quit this example app',
                style: TextStyle(color: Colors.red),
              ),
              onTap: () {
                // WARNING: DO NOT USE this in production !!!
                //          Your app will (most probably) be rejected !!!
                if (Platform.isIOS) {
                  exit(0);
                } else {
                  SystemNavigator.pop();
                }
              },
            ),
        ],
      ),
    );
  }

  Widget _cmdsCard(List<String>? commands) {
    Widget platformCmds;

    if (commands == null) {
      platformCmds = const Center(child: Text('Unsupported platform'));
    } else {
      platformCmds = Column(
        children: [
          const [
            if (kIsWeb)
              Text('Append this path to the Web app\'s URL, replacing `#/`:\n')
            else
              Text('To populate above fields open a terminal shell and run:\n'),
          ],
          intersperse(
              commands.map<Widget>((cmd) => InkWell(
                    onTap: () => _printAndCopy(cmd),
                    child: Text('\n$cmd\n', style: _cmdStyle),
                  )),
              const Text('or')),
          [
            Text(
              '(tap on any of the above commands to print it to'
              ' the console/logger and copy to the device clipboard.)',
              textAlign: TextAlign.center,
              style: Theme.of(context).textTheme.caption,
            ),
          ]
        ].expand((el) => el).toList(),
      );
    }

    return Card(
      margin: const EdgeInsets.only(top: 20.0),
      child: Padding(
        padding: const EdgeInsets.all(10.0),
        child: platformCmds,
      ),
    );
  }

  Future<void> _printAndCopy(String cmd) async {
    print(cmd);

    await Clipboard.setData(ClipboardData(text: cmd));
    ScaffoldMessenger.of(context).showSnackBar(
      const SnackBar(content: Text('Copied to Clipboard')),
    );
  }

  void _showSnackBar(String msg) {
    WidgetsBinding.instance?.addPostFrameCallback((_) {
      final context = _scaffoldKey.currentContext;
      if (context != null) {
        ScaffoldMessenger.of(context).showSnackBar(SnackBar(
          content: Text(msg),
        ));
      }
    });
  }
}

List<String>? getCmds() {
  late final String cmd;
  var cmdSuffix = '';

  const plainPath = 'path/subpath';
  const args = 'path/portion/?uid=123&token=abc';
  const emojiArgs =
      '?arr%5b%5d=123&arr%5b%5d=abc&addr=1%20Nowhere%20Rd&addr=Rand%20City%F0%9F%98%82';

  if (kIsWeb) {
    return [
      plainPath,
      args,
      emojiArgs,
      // Cannot create malformed url, since the browser will ensure it is valid
    ];
  }

  if (Platform.isIOS) {
    cmd = '/usr/bin/xcrun simctl openurl booted';
  } else if (Platform.isAndroid) {
    cmd = '\$ANDROID_HOME/platform-tools/adb shell \'am start'
        ' -a android.intent.action.VIEW'
        ' -c android.intent.category.BROWSABLE -d';
    cmdSuffix = "'";
  } else {
    return null;
  }

  // https://orchid-forgery.glitch.me/mobile/redirect/
  return [
    '$cmd "unilinks://host/$plainPath"$cmdSuffix',
    '$cmd "unilinks://example.com/$args"$cmdSuffix',
    '$cmd "unilinks://example.com/$emojiArgs"$cmdSuffix',
    '$cmd "unilinks://@@malformed.invalid.url/path?"$cmdSuffix',
  ];
}

List<Widget> intersperse(Iterable<Widget> list, Widget item) {
  final initialValue = <Widget>[];
  return list.fold(initialValue, (all, el) {
    if (all.isNotEmpty) all.add(item);
    all.add(el);
    return all;
  });
}
```





```dart
//
// Generated file. Do not edit.
//

// ignore_for_file: lines_longer_than_80_chars

import 'package:uni_links_web/uni_links_web.dart';

import 'package:flutter_web_plugins/flutter_web_plugins.dart';

// ignore: public_member_api_docs
void registerPlugins(Registrar registrar) {
  UniLinksPlugin.registerWith(registrar);
  registrar.registerMessageHandler();
}
```

