---
title: "构件flutter定位服务"
date: 2021-10-05T16:56:57+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---

> 在本教程中，我将向您展示如何从服务中获取您在 Flutter 中的位置。

在 Flutter 中获取您的位置是一项简单的任务。本教程将向您展示如何将位置包包装到易于在您的应用程序中使用的服务中。创建一个新的 Flutter 项目并继续。

## 设置

Provider 是我的默认依赖提供者/状态管理解决方案，所以我们也将使用它。我们将这两个包添加到 pubspec.yaml 文件中。

```yaml
provider: ^3.0.0
location: ^2.3.5
```

### 安卓

将位置权限添加到`AndroidManifest.xml`应用程序标签之外的清单中。

```xml
...
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />

    <application
        android:name="io.flutter.app.FlutterApplication"
        android:label="The Guardian"
        android:icon="@mipmap/ic_launcher">
        ...
    </application>
...
```

将您的 gradle.properties 文件更新为此

```text
android.enableJetifier=true
android.useAndroidX=true
org.gradle.jvmargs=-Xmx1536M
```

将您的 build.gradle 文件依赖项更新为此

```text
 dependencies {
      classpath 'com.android.tools.build:gradle:3.3.0'
      classpath 'com.google.gms:google-services:4.2.0'
  }
```

并确保您`compileSdkVersion`是 28 。

### IOS

```xml
	<key>NSLocationWhenInUseUsageDescription</key>
	<string>This app requires access to your location for FilledStacks tutorial.</string>
	<key>NSLocationAlwaysUsageDescription</key>
	<string>This app requires access to your location for FilledStacks tutorial.</string>
```

*这就是所有设置完成。如果您遇到 AndroidX 问题，请确保迁移或使用此软件包的旧版本（如果您不想迁移）。*

## 服务实施

如果有一件事我可以推荐，那就是阅读单一职责原则。基于此，我养成了使用单一用途服务构建应用程序的习惯，这些服务在需要时注入/定位。让我们创建我们的`LocationService`. 这项服务将：

1. 提供我们可以依赖的持续更新流
2. 提供对当前位置执行一次性请求的函数

在 services 文件夹下创建一个名为 location_service.dart 的新文件。我们将首先添加`getLocation()`可用于一次性检索的单一请求函数。

```dart
import 'package:location/location.dart';

class LocationService {
  UserLocation _currentLocation;

  var location = Location();

  Future<UserLocation> getLocation() async {
    try {
      var userLocation = await location.getLocation();
      _currentLocation = UserLocation(
        latitude: userLocation.latitude,
        longitude: userLocation.longitude,
      );
    } on Exception catch (e) {
      print('Could not get location: ${e.toString()}');
    }

    return _currentLocation;
  }
}
```

我们还将引入我们自己的 Location 模型，以确保我们的外部代码不依赖于模型的包表示。在模型文件夹下创建一个名为 user_location.dart 的新文件

```dart
class UserLocation {
  final double latitude;
  final double longitude;

  UserLocation({this.latitude, this.longitude});
}
```

现在让我们添加向我们发出所有用户位置更新的 Stream。下面的所有代码都在定位服务中。

```dart
  StreamController<UserLocation> _locationController =
      StreamController<UserLocation>();

  Stream<UserLocation> get locationStream => _locationController.stream;

  LocationService() {
    // Request permission to use location
    location.requestPermission().then((granted) {
      if (granted) {
        // If granted listen to the onLocationChanged stream and emit over our controller
        location.onLocationChanged().listen((locationData) {
          if (locationData != null) {
            _locationController.add(UserLocation(
              latitude: locationData.latitude,
              longitude: locationData.longitude,
            ));
          }
        });
      }
    });
  }
```

此服务旨在用于控制视图状态和处理逻辑的对象，而不是视图本身。话虽如此，为了保持本教程的简短和范围，我只会将流传递给提供者以展示我们如何使用它。我们将使用 StreamProvider 包装主应用程序，并从 LocationService 向构建器提供流。

```dart
class MyApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return StreamProvider<UserLocation>(
      builder: (context) => LocationService().locationStream,
      child: MaterialApp(
          title: 'Flutter Demo',
          theme: ThemeData(
            primarySwatch: Colors.blue,
          ),
          home: Scaffold(
            body: HomeView(),
          )),
    );
  }
}
```

然后`HomeView`我们将使用该流并打印出位置值。

```dart
class HomeView extends StatelessWidget {
  const HomeView({Key key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    var userLocation = Provider.of<UserLocation>(context);
    return Center(
      child: Text(
          'Location: Lat${userLocation?.latitude}, Long: ${userLocation?.longitude}'),
    );
  }
}
```