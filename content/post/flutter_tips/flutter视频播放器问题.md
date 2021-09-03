---
title: "Flutter视频播放器问题"
date: 2021-08-30T21:43:07+08:00
draft: false
tags: ["Flutter"]
categories: ["Flutter"]
---

> 本文主要介绍

<!--more-->

大约一个月前，我写了一个关于视频播放器包的小类，我在应用程序中使用它来显示来自 vimeo 的视频。今天我将这个类添加到一个新项目中，在初始化播放器时我得到了这个:
E/flutter (20716): [ERROR:flutter/lib/ui/ui_dart_state.cc(177)] 未处理的异常:PlatformException( channel 错误，无法在 channel 上建立连接。，空，空)
E/flutter (20716): #0 VideoPlayerApi.initialize (package:video_player_platform_interface/messages.dart:177:7)
E/flutter (20716):
E/flutter (20716):#1 MethodChannelVideoPlayer.init ...
奇怪的是，它在我的另一个项目中使用相同的代码/脚手架仍然可以完美地工作，所以我现在不知所措，不知道发生了什么......

**最佳答案**

在您的 MainActivity 中添加此代码



![image-20210830214436181](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210830214436181.png)

```
import android.content.Context
import android.os.Bundle
import androidx.annotation.NonNull
import io.flutter.embedding.android.FlutterActivity
import io.flutter.embedding.engine.FlutterEngine
import io.flutter.plugins.sharedpreferences.SharedPreferencesPlugin
import io.flutter.plugins.videoplayer.*





class MainActivity : FlutterActivity() {

override fun configureFlutterEngine(@NonNull flutterEngine: 
 FlutterEngine) {
    super.configureFlutterEngine(flutterEngine)
    flutterEngine.getPlugins().add(SharedPreferencesPlugin())
    flutterEngine.getPlugins().add(VideoPlayerPlugin())

}
}
```





如何将数字转换为科学记数法并获得指数？例如，如果我有 0.000000001 并想将其转换为 1e-9

**最佳答案**

使用 [intl package](https://pub.dev/packages/intl)并尝试如下

```dart
final value = 0.000000025;
String res = NumberFormat.scientificPattern(Localizations.localeOf(context).languageCode).format(value);
print("Formatted value is $res");
```

**编辑**
一种更好、更干净的方法，不需要任何额外的包，是方法 `toStringAsExponential()` .这是如何使用它

```dart
final value = 0.000000025;
print(value.toStringAsExponential());
```



# encoding - 如何将 Dart 的ByteData转换为字符串？

我正在读取一个二进制文件，并希望将其转换为字符串。如何在Dart中完成？

## **最佳答案**

尝试以下

```dart
String getStringFromBytes(ByteData data) {
  final buffer = data.buffer;
  var list = buffer.asUint8List(data.offsetInBytes, data.lengthInBytes);
  return utf8.decode(list);
}
```

[`ByteData`](https://docs.flutter.io/flutter/dart-typed_data/ByteData-class.html) 是一个抽象：

> 一个固定长度的随机访问字节序列，它还提供对这些字节表示的固定宽度整数和浮点数的随机和未对齐访问。



正如 Gunter 在评论中提到的，您可以使用[`File.writeAsBytes`](https://api.dartlang.org/dev/2.0.0-dev.52.0/dart-io/File/writeAsBytes.html). 但是，它确实需要一些 API 工作才能从`ByteData`到`List<int>`。

```dart
import 'dart:async';
import 'dart:io';
import 'dart:typed_data';

Future<void> writeToFile(ByteData data, String path) {
  final buffer = data.buffer;
  return new File(path).writeAsBytes(
      buffer.asUint8List(data.offsetInBytes, data.lengthInBytes));
```

你需要安装[path_provider](https://pub.dev/packages/path_provider)包，然后

## 这应该工作：

```dart
import 'dart:async';
import 'dart:io';
import 'dart:typed_data';
import 'package:path_provider/path_provider.dart';

final dbBytes = await rootBundle.load('assets/file'); // <= your ByteData

//=======================
Future<File> writeToFile(ByteData data) async {
    final buffer = data.buffer;
    Directory tempDir = await getTemporaryDirectory();
    String tempPath = tempDir.path;
    var filePath = tempPath + '/file_01.tmp'; // file_01.tmp is dump file, can be anything
    return new File(filePath).writeAsBytes(
        buffer.asUint8List(data.offsetInBytes, data.lengthInBytes));
}
//======================
```

获取您的文件：

```dart
var file;
try {
    file = await writeToFile(dbBytes); // <= returns File
} catch(e) {
    // catch errors here
}
```

## 如何转换`ByteData`为`List<int>`？

经过自我调查，解决方案是：

1. 用 `.cast<int>()`

```dart
ByteData audioByteData = await rootBundle.load(audioAssetsFullPath);
Uint8List audioUint8List = audioByteData.buffer.asUint8List(audioByteData.offsetInBytes, audioByteData.lengthInBytes);
List<int> audioListInt = audioUint8List.cast<int>();
```

或 2. 使用 `.map`

```dart
ByteData audioByteData = await rootBundle.load(audioAssetsFullPath);
Uint8List audioUint8List = audioByteData.buffer.asUint8List(audioByteData.offsetInBytes, audioByteData.lengthInBytes);
List<int> audioListInt = audioUint8List.map((eachUint8) => eachUint8.toInt()).toList();
```



# flutter - 如何在 dart/flutter 中收听流值

任何人帮助追踪音频的位置(即)

```dart
   if(durationtoOne(position==5)){
FlutterToast.Showtoast(msg:"I am At 5 sec");
}
```


如果在 initstate 中添加出现错误，我会被困在何处添加此代码，
我想通过音频平台收听位置

代码从这里开始

```dart
  import 'dart:async';
    import 'package:assets_audio_player/assets_audio_player.dart';
    import 'package:flutter/material.dart';
    import 'package:fluttertoast/fluttertoast.dart';

    class SmartMantra extends StatefulWidget {
    @override
  _SmartMantraState createState() => _SmartMantraState();
}

class _SmartMantraState extends State<SmartMantra> {
  StreamSubscription _positionSubscription;
  Duration position;
  AssetsAudioPlayer _assetsAudioPlayer;


  stream() {
    _positionSubscription = _assetsAudioPlayer.currentPosition
        .listen((p) => setState(() => position = p),);
  }

  @override
  void initState() {
    _assetsAudioPlayer.open("assets/shivamantra.mp3");
    stream();
    _assetsAudioPlayer.finished.listen((finished) {
      print(finished);
//      print(count);
    });
    super.initState();
  }

  @override
  void dispose() {
    _positionSubscription.cancel();
    super.dispose();
  }
  @override
  Widget build(BuildContext context) {
    return Scaffold(
        body: Column(
          children: <Widget>[
            SizedBox(
              height: 70,
            ),
            Center(
              child: Text(
               durationToone(position).toString(),
                style: TextStyle(color: Colors.black, fontSize: 12),
              ),
            ),
            //getTextContainer()
          ],
        ));
  }
int durationToone(Duration duration) {
  int twoDigits(int n) {
    if (n >= 10) return n;
    return n;
  }

  int twoDigitSeconds =
      twoDigits(duration.inSeconds.remainder(Duration.secondsPerMinute));
  return twoDigitSeconds;
}
}
```


代码到此结束

总结:在特定位置需要在位置变化时触发一些函数(即)

```dart
if(durationtoOne(position==5)){
FlutterToast.Showtoast(msg:"I am At 5 sec");
}
```


通过音频播放或应用程序在前台



**最佳答案**

如果流不是广播流，则您只能收听一次。

请参阅此 [Medium post](https://medium.com/@ayushpguptaapg/using-streams-in-flutter-62fed41662e4) 以了解有关 Streams 的更多信息。

收听 `stream` 时，您需要在 `_assetsAudioPlayer.currentPosition` 函数中添加您的代码。

```dart
import 'dart:async';
import 'package:assets_audio_player/assets_audio_player.dart';
import 'package:flutter/material.dart';
import 'package:fluttertoast/fluttertoast.dart';

class SmartMantra extends StatefulWidget {
    @override
    _SmartMantraState createState() => _SmartMantraState();
}

class _SmartMantraState extends State<SmartMantra> {
    StreamSubscription _positionSubscription;
    Duration position;
    AssetsAudioPlayer _assetsAudioPlayer;

    stream() {
        _positionSubscription = _assetsAudioPlayer.currentPosition
            .listen((p) {
                setState(() => position = p));
                // You should add your code here
                if(durationtoOne(position==5)){
                    FlutterToast.Showtoast(msg:"I am At 5 sec");
                }
            }
    }

    @override
    void initState() {
        _assetsAudioPlayer.open("assets/shivamantra.mp3");
        stream();
        _assetsAudioPlayer.finished.listen((finished) {
            print(finished);
//          print(count);
        });
        super.initState();
    }

    @override
    void dispose() {
        _positionSubscription.cancel();
        super.dispose();
    }

    @override
    Widget build(BuildContext context) {
        return Scaffold(
            body: Column(
                children: <Widget>[
                SizedBox(
                    height: 70,
                ),
                Center(
                    child: Text(
                        durationToone(position).toString(),
                        style: TextStyle(color: Colors.black, fontSize: 12),
                    ),
                ),
                //getTextContainer()
            ],
        ));
    }
    int durationToone(Duration duration) {
        int twoDigits(int n) {
            if (n >= 10) return n;
            return n;
        }

        int twoDigitSeconds =
            twoDigits(duration.inSeconds.remainder(Duration.secondsPerMinute));
        return twoDigitSeconds;
    }
}
```

11