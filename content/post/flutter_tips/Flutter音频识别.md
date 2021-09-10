---
title: "Flutter音频识别"
date: 2021-08-21T10:19:18+08:00
draft: false
tags: ["Flutter"]
categories: ["Flutter"]
---

> 本文主要介绍

<!--more-->

https://carolinamalbuquerque.medium.com/audio-recognition-using-tensorflow-lite-in-flutter-application-8a4ad39964ae

机器学习 (ML) 和人工智能 (AI) 增强了移动应用程序的开发。将 ML 模型集成到应用程序中以对事件进行分类或预测，可以创建能够理解和识别用户行为的应用程序，并使他们的体验更加智能和有趣。一个易于理解的示例是根据在发送短信时键入的先前内容向用户建议的下一个单词。因此，本文将分析将 ML 模型集成到移动 Flutter 应用程序中以进行音频识别。

# 创建分类模型

首先的，它需要有一个训练有素的分类模型。例如，如果您是 ML 概念的初学者，Google Teachable Machine 是创建模型的一种快速简便的方法。该框架提供了三种项目类型的创建：图像、音频和姿势。在此示例中，将使用 Google Teachable Machine (GTM) 模型，由于主要目标是集成音频分类模型，因此必须选择音频项目。

创建模型的第一步是将数据安排到不同的类中。可以立即记录样本或上传文件。每个类都有最少数量的音频样本。背景噪声是一个默认类，它必须包含带有背景噪声的样本。在此示例中，创建了**Bell**、**Whistle**和**Xylophone 类，**其中包含这些声音的音频样本。

![img](https://miro.medium.com/max/60/1*8hs87nyY1eVYbYCRm7gZgw.png?q=20)

![img](https://miro.medium.com/max/700/1*8hs87nyY1eVYbYCRm7gZgw.png)

使用 Google Teachable Machine 创建分类模型

![img](https://miro.medium.com/max/60/1*yWW1LYBA_bZ_MDPzEOIf_Q.png?q=20)

![img](https://miro.medium.com/max/500/1*yWW1LYBA_bZ_MDPzEOIf_Q.png)

将训练好的模型导出为 Tensorflow Lite 模型

数据按类分离后，必须**训练**模型并**导出为 Tensorflow Lite 格式**。下载的模型包含两个文件：(`labels.txt`指定类的标签)和`soundclassifier.tflite`(模型)。这些文件将被添加到移动应用程序中，以便在 tensorflow 的帮助下可以读取它们并加载模型。

**注意：**如果您对 ML 概念感到满意，我建议您开发自己的模型并对其进行训练，因为在您的应用程序中使用 GTM 模型可以提高其大小。Tensorflow 文档有一个帮助创建模型的[指南](https://www.tensorflow.org/tutorials/audio/simple_audio)。



创建了一个简单的应用程序，其中包含文本小部件和用于开始录制音频的 MaterialButton。

我在这里留下了`main.dart`基本代码：

# 创建flutter 应用程序

创建了一个简单的应用程序，其中包含文本小部件和用于开始录制音频的 MaterialButton。

我在这里留下了`main.dart`基本代码：

```dart
import 'package:flutter/material.dart';
import 'package:tflite_audio/tflite_audio.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      theme: ThemeData.dark(),
      home: MyHomePage(),
    );
  }
}

class MyHomePage extends StatefulWidget {
  @override
  _MyHomePageState createState() => _MyHomePageState();
}

class _MyHomePageState extends State<MyHomePage> {
  String _sound = "Press the button to start";
  bool _recording = false;

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Container(
        decoration: BoxDecoration(
          image: DecorationImage(
            image: AssetImage("assets/background.jpg"),
            fit: BoxFit.cover,
          ),
        ),
        child: Center(
          child: Column(
            mainAxisAlignment: MainAxisAlignment.spaceEvenly,
            children: <Widget>[
              Container(
                padding: EdgeInsets.all(20),
                child: Text(
                  "What's this sound?",
                  style: TextStyle(
                    color: Colors.white,
                    fontSize: 60,
                    fontWeight: FontWeight.w200,
                  ),
                ),
              ),
              MaterialButton(
                onPressed: (){},
                color: _recording ? Colors.grey : Colors.pink,
                textColor: Colors.white,
                child: Icon(Icons.mic, size: 60),
                shape: CircleBorder(),
                padding: EdgeInsets.all(25),
              ),
              Text(
                '$_sound',
                style: Theme.of(context).textTheme.headline5,
              ),
            ],
          ),
        ),
      ),
    );
  }
}
```

# 将模型集成到应用程序中

为了在移动应用程序中集成 tensorflow 模型，Tensorflow Lite 框架有助于在移动设备上运行这些模型。为了访问 Tensorflow Lite，为此需要一个**flutter 插件**：[**tflite**](https://pub.dev/packages/tflite)。但是，tflite 插件还没有分析音频。因此，将使用类似的最新插件进行音频处理：[**tflite_audio**](https://pub.dev/packages/tflite_audio)。除了这个插件支持 GTM 模型和带有解码波形输入的模型外，它还有一些文档中描述的限制，即只有在物理移动设备上运行时才有效。在结论中，将更详细地讨论这个主题。

首先，在您的`pubspec.yaml`文件中添加以下依赖项：

```
dependencies:
	tflite_audio: ^0.1.5+3
```

要安装软件包，请运行：

```
flutter pub get
```

创建一个新`/assets`目录，添加从 Google Teachable Machine 框架下载的`soundclassifier.tflite`和`labels.txt`文件，并更改项目中`pubspec.yaml`包含`/assets`文件夹的配置。

```
flutter:
  # To add assets to your application, add an assets section:
  assets:
    - assets/labels.txt
    - assets/soundclassifier.tflite
```

之后需要配置Android和iOS环境，使用GTM模型需要选择tensorflow算子。

# 安卓配置

1. 将以下**权限**添加到`AndroidManifest.xml`：

```
<uses-permission android:name="android.permission.RECORD_AUDIO" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

2.将**aaptOptions**添加到`app/build.gradle`文件中：

```
android {
   (...)
    aaptOptions {
        noCompress 'tflite'
    }
    lintOptions {
        disable 'InvalidPackage'
    }
(...)
}
```

3.在文件中启用**选择操作**`app/build.gradle`：

```
dependencies {
    implementation "org.jetbrains.kotlin:kotlin-stdlib-jdk7:$kotlin_version"
    implementation "org.tensorflow:tensorflow-lite-select-tf-ops:+"
}
```

4.更改**的minSdkVersion**到**至少21**的`app/build.gradle`文件。

# iOS 配置

将以下权限添加到`ios/Runner/Info.plist`：

```
<key>NSMicrophoneUsageDescription</key>
<string>Record audio for playback</string>
```

有必要将目标部署更改**为** **至少 12.0**。例如，打开`iOS/Runner.xcworkspace`XCode，选择**Runner**并在**Info**选项卡中更改 iOS 部署目标：

![img](https://miro.medium.com/freeze/max/60/1*nK3YABQqtiw-qggB__Ay4g.gif?q=20)

![img](https://miro.medium.com/max/700/1*nK3YABQqtiw-qggB__Ay4g.gif)

将 iOS 部署目标更改为 12.0

现在，您需要在中指定 iOS 目标部署 `ios/Runner/Podfile`

```
platform :ios, '12.0'
```

由于模型是 GTM 模型，`pod 'TensorFlowLiteSelectTfOps'`在目标中添加行`ios/Runner/Podfile`

```
target 'Runner' do
  use_frameworks!
  use_modular_headers!
  pod 'TensorFlowLiteSelectTfOps'
  flutter_install_all_ios_pods File.dirname(File.realpath(__FILE__))
end
```

之后，在 XCode 上打开项目，选择**Targets > Runner > Build Settings > All**并将以下行添加到**Linking > Other Linker Flag**：

```
-force_load $(SRCROOT)/Pods/TensorFlowLiteSelectTfOps/Frameworks/TensorFlowLiteSelectTfOps.framework/TensorFlowLiteSelectTfOps
```

![img](https://miro.medium.com/freeze/max/60/1*aKozvuHGTXbGcwiqEfUzJg.gif?q=20)

![img](https://miro.medium.com/max/700/1*aKozvuHGTXbGcwiqEfUzJg.gif)

添加强制加载命令

最后，`/ios`在终端上运行以下命令进入目录：

```
$ flutter pub get
$ pod install
$ flutter clean
```

确保您已**更新 CocoaPods**。在`pod install`此期间可能会发出警告。

```
[!] CocoaPods did not set the base configuration of your project because your project already has 
a custom config set. In order for CocoaPods integration to work at all, please either set the base 
configurations of the target `Runner` to `Target Support Files/Pods-Runner/Pods-Runner.profile.xcconfig` 
or include the `Target Support Files/Pods-Runner/Pods-Runner.profile.xcconfig` in your build configuration
(`Flutter/Release.xcconfig`).
```

如果您在`pod install`命令后收到此警告，请将以下行添加到您的`ios/Flutter/Release.xcconfig`文件中并再次运行`pod install`：

```
#include “Pods/Target Support Files/Pods-Runner/Pods-Runner.profile.xcconfig”
```

# 加载和使用模型

在您的**initState() 方法上**，您必须**加载模型**：

```
TfliteAudio.loadModel(
    model: 'assets/soundclassifier.tflite',
    label: 'assets/labels.txt',
    numThreads: 1,
    isAsset: true);；
```

现在，我建议实施一种新方法来记录音频并对其进行分析。tflite_audio 包的**startAudioRecognition()**方法具有以下必须通过参数传递的参数：

- **numOfInferences** — 指定将重复的推理次数
- **inputType** — 指定输入音频的类型
- **sampleRate** — 每秒样本数
- **RecordingLength** — 确定张量输入的大小
- **bufferSize** — 处理任何传入音频信号的时间量

由于分类模型是 GTM 模型，因此您必须将音频识别的**inputType**指定为**'rawAudio'。**如果您使用自己的模型(decodedwav 模型)而不是 GTM 模型，则 inputType 必须是“decodedWav”。对于**sampleRate**，[packages文档](https://pub.dev/packages/tflite_audio)上的推荐值是**16000 、 22050**或**44100**，将使用较高的值以提高准确性。该**recordingLength**值**必须等于输入张**和**缓冲区大小**应该是**recordingLenght值的一半**。

```
void _recorder() {
    String recognition = "";
    if (!_recording) {
      setState(() => _recording = true);
      result = TfliteAudio.startAudioRecognition(
        numOfInferences: 1,
        inputType: 'rawAudio',
        sampleRate: 44100,
        recordingLength: 44032,
        bufferSize: 22016,
      );
      result.listen((event) {
        recognition = event["recognitionResult"];
      }).onDone(() {
        setState(() {
          _recording = false;
          _sound = recognition.split(" ")[1];
        });
      });
   }
  } 
```

此外，必须声明结果流变量：

```
class _MyHomePageState extends State<MyHomePage> {
  String _sound = "Press the button to start";
  bool _recording = false;
  Stream<Map<dynamic, dynamic>> result;
```

如果你想在执行时**停止音频识别**，你可以使用这个方法：

```
TfliteAudio.stopAudioRecognition(); 
```

最后，应用程序可以使用了！😄 请注意，如前所述，该应用程序只能在 Android 和 iOS 物理设备上运行。如果你在 iOS 设备的部署过程中遇到一些问题，我推荐这个[文档](https://flutter.dev/docs/deployment/ios)。另外，请注意，当您第一次在 iOS 设备上运行该应用程序时，它会崩溃，因为您必须信任它(详情[在此处](https://flutter.dev/docs/deployment/ios)解释)。

![img](https://miro.medium.com/max/2160/1*2wEA54uAttc-h6EZAzXhvg.gif)

![img](https://miro.medium.com/max/2160/1*OhOxEOV0JXMw4zeZ_PfQOA.gif)

iOS(左)和 Android(右)设备中的应用程序预览

# 代码

如果要查看完整代码：[https](https://github.com/cmalbuquerque/audio_recognition_app) : [//github.com/cmalbuquerque/audio_recognition_app](https://github.com/cmalbuquerque/audio_recognition_app)

请查看我的[Github](https://github.com/cmalbuquerque)个人资料！😉